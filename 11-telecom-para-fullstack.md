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

---

## Profundización

### SSE: la tercera opción que casi todos olvidan

Entre "HTTP normal" y "WebSocket" hay un punto intermedio infrautilizado: **Server-Sent Events (SSE)**. Es una conexión HTTP normal que el servidor mantiene abierta y por la que empuja eventos de texto cuando quiere — unidireccional (solo servidor → cliente), con reconexión automática incluida en el estándar del navegador (`EventSource`), y sin protocolo nuevo que configurar en proxies y balanceadores.

La guía de decisión honesta: si solo necesitas que el servidor notifique (dashboards, feeds, progreso de tareas, streaming de respuestas de un LLM — los tokens que "van apareciendo" en ChatGPT o Claude llegan por SSE o variantes), SSE es más simple y robusto que WebSocket. WebSocket se justifica cuando el cliente también emite con frecuencia (chat, juegos, edición colaborativa). Un clásico de sobre-ingeniería es montar WebSocket, con toda su gestión de estado y reconexión manual, para algo que era un caso de SSE.

### Caché HTTP: el sistema distribuido que ya usas sin saberlo

La capa de rendimiento más rentable del desarrollo web es también la peor entendida. El modelo mental correcto, con las cabeceras clave:

- **`Cache-Control: max-age=N`**: "esta respuesta es válida N segundos" — durante ese tiempo, el navegador (o el CDN) ni siquiera pregunta al servidor. Máxima velocidad, riesgo de servir contenido viejo.
- **`ETag` + validación**: la respuesta lleva una huella (conceptualmente, un hash — bloque 9); cuando caduca, el cliente pregunta "¿sigue siendo la versión `abc123`?" y el servidor responde `304 Not Modified` (sin cuerpo, casi gratis) o la versión nueva. Cambias transferencia por un RTT (bloque 5).
- **El patrón que resuelve el dilema**: assets con hash en el nombre (`app.9f8e2.js`) y caché infinita — si el contenido cambia, cambia el nombre, así que nunca se sirve nada viejo — más HTML sin caché (o con validación) que referencia esos nombres. Es lo que hace tu bundler, y ahora sabes por qué.

La conexión conceptual: la caché HTTP es **consistencia eventual elegida a mano** (bloque 12) — con `max-age` estás decidiendo, cabecera a cabecera, cuánta "vejez" toleras a cambio de velocidad. El mismo trade-off de CAP, en miniatura y bajo tu control directo.

### gRPC y los formatos binarios: cuándo JSON deja de ser gratis

**gRPC** empaqueta una idea triple: contratos tipados definidos en un fichero (`.proto`) de los que se genera código cliente y servidor, serialización binaria compacta (Protobuf — volviendo al bloque 2: JSON tiene muchísima redundancia; un formato binario con esquema conocido está mucho más cerca de la entropía real de los datos), y HTTP/2 debajo (multiplexación y streaming bidireccional de serie).

La guía de decisión: entre microservicios internos con mucho tráfico, gRPC gana (menos bytes, menos CPU de parseo, contratos que rompen en compilación en vez de en producción). Hacia navegadores y APIs públicas, REST/JSON sigue ganando por legibilidad, depurabilidad (`curl` y a mirar) y ecosistema. El error a evitar es el cargo-cult en ambas direcciones: gRPC "porque es lo moderno" en una API pública, o JSON "porque siempre" entre dos servicios que se intercambian millones de mensajes por segundo.

## Ejercicio práctico

1. **Desglosa una petición por fases**, con la teoría del bloque 5 visible:
   ```bash
   curl -w "DNS: %{time_namelookup}s | TCP: %{time_connect}s | TLS: %{time_appconnect}s | primer byte: %{time_starttransfer}s | total: %{time_total}s\n" -o /dev/null -s https://github.com
   ```
   Ejecútalo dos veces seguidas y compara: la segunda reutiliza caché DNS. Pruébalo contra un servidor lejano geográficamente y verás la latencia física del bloque 4 en cada fase.
2. **Audita la caché de tu propio proyecto**: abre DevTools → Network en una app tuya, recarga, y clasifica cada recurso: ¿cuáles vienen "(from disk cache)", cuáles devuelven 304, cuáles se re-descargan enteros sin necesidad? Es raro el proyecto que no tiene algo mal aquí.
3. **Mira SSE en vivo**: abre DevTools en cualquier chat con un LLM (ChatGPT, Claude) mientras genera una respuesta larga — en Network verás la conexión de streaming y los eventos llegando token a token.

## Autoevaluación

1. Un cliente te pide "notificaciones en tiempo real" en un dashboard de solo lectura. ¿SSE, WebSocket o long polling? Defiende la elección y di qué te haría cambiarla.
2. ¿Por qué HTTP/3 abandonó TCP? Explica el head-of-line blocking con un ejemplo de una página que carga 20 recursos.
3. Explica el patrón "hash en el nombre + caché infinita" y por qué elimina el clásico "dile al usuario que borre caché".
4. ¿En qué sentido preciso configurar `Cache-Control` es tomar una decisión del teorema CAP?
