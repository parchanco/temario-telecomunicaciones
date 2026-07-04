# Bloque 11 — De telecom a desarrollo fullstack

> Este bloque conecta lo que ya sabes de desarrollo web con los conceptos de telecomunicaciones de los bloques anteriores. Es probablemente la parte que más vas a usar en el día a día.

## WebRTC: telecomunicaciones dentro del navegador

**WebRTC** permite comunicación de audio, vídeo y datos **en tiempo real, directamente entre navegadores**, sin pasar por un servidor intermedio para el contenido en sí (aunque sí hace falta un servidor para la "presentación" inicial). Es, literalmente, aplicar los conceptos del bloque 2 (codificación, compresión) y del bloque 5 (UDP) dentro de una API de navegador:

- Usa **UDP** por debajo (no TCP), por la misma razón que se explicó en el bloque 5: en una videollamada, es peor esperar a que se retransmita un paquete perdido que simplemente perder ese frame y seguir.
- Tiene que resolver el problema de **NAT traversal**: la mayoría de dispositivos están detrás de un router doméstico que no tiene una IP pública propia, así que dos navegadores no pueden "verse" directamente sin ayuda. Para esto WebRTC usa protocolos como **STUN** (para que cada extremo descubra su IP pública tal y como la ve el exterior) e **ICE** (para negociar la mejor ruta posible entre ambos); si ni con eso consiguen conectar directamente, cae a un servidor **TURN** que retransmite el tráfico como intermediario — con el coste de latencia y ancho de banda de servidor que eso implica.
- La señalización inicial (quién quiere hablar con quién, qué códecs soporta cada uno) no la define WebRTC — se hace por tu propio canal (normalmente un WebSocket contra tu backend), y solo después el audio/vídeo empieza a viajar directamente entre los pares.

Si alguna vez has integrado una videollamada en una app (Zoom SDK, Twilio, LiveKit, o WebRTC puro), estabas usando exactamente esta arquitectura.

## WebSockets vs HTTP tradicional vs long polling

HTTP fue diseñado originalmente como petición-respuesta: el cliente pide, el servidor responde, la conexión (conceptualmente) termina. Eso es un mal encaje para casos donde el servidor necesita **avisar al cliente** de algo sin que el cliente pregunte primero (un chat, notificaciones en vivo, un dashboard en tiempo real).

- **Long polling**: el cliente hace una petición HTTP normal, pero el servidor **no responde inmediatamente** — la deja "colgada" hasta que hay algo que contar (o hasta un timeout), y entonces responde y el cliente vuelve a pedir. Es un parche ingenioso sobre un protocolo que no estaba pensado para esto — funciona, pero con overhead de reabrir conexiones constantemente.
- **WebSocket**: es un protocolo aparte que **arranca como una petición HTTP** (para aprovechar la infraestructura existente: puertos, proxies, firewalls que ya dejan pasar HTTP) pero luego "actualiza" (upgrade) esa conexión a un canal **full-duplex persistente** — ambos extremos pueden mandar mensajes en cualquier momento, sin la sobrecarga de cabeceras HTTP en cada mensaje ni de reabrir conexión. Es la solución nativa moderna al mismo problema que long polling resolvía de forma artesanal.

La conexión con telecom: un WebSocket es, conceptualmente, muy parecido a establecer un "circuito" dedicado (bloque 5) entre cliente y servidor para toda la sesión, en vez de la conmutación de paquetes independientes que implica cada petición HTTP suelta.

## HTTP/2 y HTTP/3 (QUIC): la evolución del protocolo que usas cada día

- **HTTP/1.1**: una conexión TCP podía quedar bloqueada esperando la respuesta de una petición antes de poder aprovecharse bien para la siguiente (mitigado en la práctica abriendo varias conexiones en paralelo al mismo servidor).
- **HTTP/2**: introduce **multiplexación** real sobre una única conexión TCP — varias peticiones y respuestas pueden viajar entrelazadas por el mismo canal sin bloquearse entre sí, más compresión de cabeceras. Es el mismo concepto de multiplexación del bloque 2 (FDM/TDM/CDM), aplicado a compartir una conexión entre múltiples streams de una app web.
- **HTTP/3**: el cambio más profundo — abandona TCP y se construye sobre **QUIC**, un protocolo de transporte nuevo sobre UDP. La razón: en HTTP/2, si se pierde un solo paquete TCP, **todos** los streams multiplexados en esa conexión se quedan esperando (porque TCP garantiza orden estricto a nivel de conexión completa, bloque 5) — esto se llama *head-of-line blocking* a nivel de transporte. QUIC resuelve esto llevando la fiabilidad y el orden a nivel de **cada stream individual**, no de la conexión entera, así que la pérdida de un paquete de un stream no bloquea a los demás. QUIC también integra el cifrado (TLS) directamente en el protocolo de transporte, en vez de como una capa añadida encima.

Cuando configuras un servidor web o depuras por qué una API es lenta en redes móviles con pérdida de paquetes, esto deja de ser una curiosidad de bajo nivel: explica directamente comportamientos que ves en producción.

## CDN y edge networks: el "handover" del contenido estático

Un **CDN (Content Delivery Network)** distribuye copias de tu contenido (assets estáticos, y cada vez más contenido dinámico cacheado) en servidores repartidos geográficamente, para que el usuario los reciba desde el punto más cercano posible — reduciendo la latencia por la simple razón física de que la distancia (y el número de saltos de red) importa (bloque 4 y 5).

La lógica de "elegir el nodo más cercano" tiene el mismo espíritu que la reutilización de células del bloque 6 o el edge computing del bloque 10: **acercar el recurso (antena, servidor de cómputo, o aquí, copia de contenido) al usuario**, en vez de que cada petición tenga que viajar hasta un origen centralizado y lejano. Servicios como Cloudflare, Fastly o CloudFront, cuando ejecutan lógica en el edge (Cloudflare Workers, Lambda@Edge), están aplicando literalmente el concepto de edge computing del bloque 10, pero para servir peticiones web en vez de procesar datos de sensores IoT.

## Colas de mensajes: MQTT, Kafka, y la multiplexación de eventos

- **MQTT**: protocolo ligero de publicación/suscripción diseñado originalmente para IoT (bloque 10) — pensado para dispositivos con conexión intermitente y poco ancho de banda. Cada vez se usa más también en apps web/móviles para notificaciones en tiempo real por su bajo overhead.
- **Kafka (y similares)**: sistemas de colas/streams de eventos usados en backends para desacoplar servicios — un servicio publica eventos sin saber ni importarle quién los consume, y varios consumidores pueden leer el mismo flujo de forma independiente.

La idea de fondo que comparten con la multiplexación del bloque 2: en vez de que cada emisor tenga que hablar directamente con cada receptor (una conexión por par, no escala), se introduce un canal compartido con reglas claras de cómo repartir/etiquetar los mensajes — muy parecido, conceptualmente, a cómo FDM/TDM/CDM permiten que muchos usuarios compartan un mismo medio de forma ordenada.

## Herramientas de debug de red que ya deberías tener en tu caja de herramientas

Estas herramientas convierten toda la teoría anterior en algo que puedes **ver** en tu propia máquina:

- **`curl -v`**: muestra la negociación HTTP completa (cabeceras, código de estado, tiempos) — útil para ver exactamente qué está pasando en una petición que falla.
- **`dig` / `nslookup`**: consulta DNS directamente (bloque 5) — imprescindible cuando algo "no resuelve" o resuelve a la IP equivocada (propagación de DNS, TTLs, registros mal configurados).
- **Wireshark / tcpdump**: capturan el tráfico de red bit a bit — te permiten ver en vivo la conmutación de paquetes, el handshake TCP, o por qué una conexión TLS está fallando, en vez de imaginártelo.
- **`mtr` / `traceroute`**: muestran el camino salto a salto (routers intermedios) que sigue un paquete hasta su destino (bloque 5) — muy útil para diagnosticar dónde exactamente se está introduciendo latencia o pérdida de paquetes.
- **DevTools → pestaña Network**: es, en el fondo, una versión simplificada y orientada a HTTP de las mismas ideas de Wireshark, ya integrada en el navegador que usas todos los días.

Saber usar estas herramientas con soltura es donde la teoría de los bloques anteriores deja de ser abstracta y se convierte en una habilidad de debugging real.
