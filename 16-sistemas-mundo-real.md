# Bloque 16 — Sistemas de telecomunicación del mundo real: VoIP, GPS y radiodifusión

> Tres sistemas que la carrera trata como asignaturas propias y que el temario original no cubría. Los tres son ejemplos magníficos de "toda la teoría anterior, junta y funcionando".

## VoIP: cuando la voz se convirtió en una app más

La telefonía clásica (bloque 5) reservaba un circuito dedicado para cada llamada. **VoIP (Voice over IP)** dio la vuelta a eso: la voz se digitaliza (bloque 1), se comprime (bloque 8), se trocea en paquetes y viaja por la misma red IP que cualquier otro dato. La consecuencia histórica es enorme: la voz dejó de necesitar una red propia — hoy una llamada de WhatsApp, una reunión de Meet y tu "línea fija" de fibra son exactamente la misma cosa técnica.

Las piezas, que reutilizan casi todo lo visto:

- **Señalización — SIP (Session Initiation Protocol)**: el protocolo que establece, modifica y cuelga la llamada — quién llama a quién, qué códecs soportan ambos, en qué IPs y puertos va a fluir el audio. Es el equivalente a la señalización de WebRTC del bloque 11 (de hecho, WebRTC es en gran parte "VoIP metido en el navegador").
- **Transporte del audio — RTP sobre UDP**: el audio en sí viaja en paquetes RTP sobre UDP, por la razón ya conocida (bloque 5): en tiempo real, retransmitir un paquete perdido llega demasiado tarde para ser útil.
- **Códecs**: el compromiso del bloque 8 aplicado a la voz — códecs de alta compresión (menos ancho de banda, algo menos de calidad) frente a códecs más fieles (Opus, el códec moderno usado por WhatsApp o Discord, ajusta ese compromiso dinámicamente según la red).
- **El enemigo real — el jitter**: como se vio en el bloque 5, para la voz importa menos el throughput que la regularidad de llegada. Los receptores usan un *jitter buffer*: retrasan deliberadamente la reproducción unas decenas de milisegundos para absorber la variación entre paquetes. Es un compromiso puro — más buffer = audio más estable pero más retardo en la conversación.

VoIP es el mejor ejemplo de por qué QoS (bloque 5) existe: en una red congestionada, o priorizas los paquetes de voz o la llamada se degrada, aunque el ancho de banda total sobre.

## GPS: relojes que emiten la hora, y nada más

El GPS es probablemente el sistema de telecomunicaciones más elegante que existe, porque hace algo enorme con una idea mínima: **cada satélite se limita a emitir, continuamente, "soy el satélite X, esta es mi posición, y son exactamente las 12:00:00.000000001"**. No hay comunicación bidireccional — tu móvil solo escucha.

Cómo funciona la magia:

- La señal viaja a la velocidad de la luz, que es enorme pero finita. Tu receptor compara la hora de emisión (contenida en el mensaje) con la hora de recepción: la diferencia, multiplicada por la velocidad de la luz, es la **distancia a ese satélite**.
- Con la distancia a un satélite, estás en algún punto de una esfera alrededor de él. Con tres satélites, las tres esferas se cortan en un punto: tu posición (**trilateración** — medir distancias, no ángulos).
- El truco fino: tu móvil no tiene un reloj atómico, así que su hora es imprecisa — y un error de un microsegundo son 300 metros de error. La solución es escuchar a un **cuarto satélite** y tratar el desfase de tu propio reloj como una incógnita más a resolver. Consecuencia deliciosa: cualquier receptor GPS conoce la hora con precisión de reloj atómico como subproducto — por eso GPS es también el sistema que sincroniza redes eléctricas, bolsas de valores y las propias redes móviles del bloque 6.

Y conecta con todo lo anterior: la señal GPS llega a la Tierra debilísima, muy por debajo del ruido de fondo (bloque 1 — un caso extremo de SNR bajo), y solo es recuperable gracias a códigos de espectro ensanchado, la misma familia de técnicas que el CDM del bloque 2. Incluso hay que corregir efectos relativistas: los relojes de los satélites laten distinto que los tuyos por su velocidad y su altura — sin esa corrección, el GPS acumularía kilómetros de error al día.

## Radiodifusión: el modelo uno-a-todos

Todo lo visto hasta ahora era comunicación punto a punto o punto a multipunto gestionado. La **radiodifusión** (broadcast) es el modelo opuesto: un transmisor, millones de receptores, **sin canal de retorno** — la emisora no sabe quién escucha, y no puede retransmitir nada si un receptor pierde datos.

Esa restricción lo condiciona todo, y explica decisiones que ya puedes leer con las herramientas de los bloques anteriores:

- **Sin retorno no hay retransmisiones**: toda la robustez debe ir "por adelantado" — FEC agresivo (bloque 2) y modulaciones conservadoras, porque el receptor tiene que apañárselas solo con lo que le llega.
- **TDT (TV digital terrestre)**: usa OFDM — miles de subportadoras estrechas en paralelo en lugar de una portadora ancha — precisamente para sobrevivir al multitrayecto del bloque 4 (la señal de TV rebota en edificios y montañas; con OFDM, los ecos dejan de ser destructivos e incluso permiten que varios repetidores emitan lo mismo en la misma frecuencia). La misma técnica OFDM es la base de la capa física de WiFi, 4G y 5G — la TDT y tu móvil son parientes cercanos.
- **El "apagón analógico"**: la TV analógica gastaba un canal entero por programa; la digital, gracias a la compresión del bloque 8, mete varios programas en el mismo canal y con mejor calidad. El espectro liberado (el "dividendo digital") se subastó para redes móviles — un ejemplo perfecto de la escasez de espectro del bloque 4 moviendo industrias enteras.
- **Streaming vs broadcast, la tensión actual**: emitir por internet (unicast: una conexión por espectador, bloque 5) escala mal para audiencias masivas — un partido visto por 10 millones de personas son 10 millones de flujos, contra un único transmisor de TDT que sirve a todos por igual. Los CDNs del bloque 11 son, en el fondo, el intento de internet de reconstruir la eficiencia del broadcast que la radiodifusión tenía gratis.

La moraleja del bloque completo: estos tres sistemas no introducen teoría nueva — son la demostración de que con los bloques 1-11 ya tienes el vocabulario para entender casi cualquier sistema de telecomunicaciones que te encuentres, presente o futuro.

---

## Profundización

### Streaming de vídeo: cómo funciona Netflix de verdad

El cuarto sistema que merece estar aquí, porque es el mayor consumidor de internet del planeta y porque su diseño es contraintuitivo: el streaming de vídeo moderno (Netflix, YouTube, Twitch) **no usa un protocolo de streaming** — es HTTP normal y corriente, el mismo del bloque 5.

El truco se llama **HLS/DASH (streaming adaptativo)**: el vídeo se trocea en segmentos de pocos segundos, cada segmento se pre-codifica en varias calidades (bloque 8 — el mismo contenido a 240p, 720p, 4K...), y un fichero índice (*manifest*) lista dónde está cada trozo de cada calidad. El reproductor es quien manda: descarga segmentos por HTTP como si fueran ficheros cualesquiera, mide continuamente cuánto tardan en llegar (¡su propio presupuesto de enlace en vivo!), y **decide segmento a segmento qué calidad pedir** — la red empeora, baja a 480p sin cortarse; mejora, vuelve a subir. Es la misma filosofía de la modulación adaptativa del bloque 2 (ajustar la densidad al estado del canal), implementada en JavaScript.

Por qué esta arquitectura ganó a los protocolos de streaming "de verdad" que existían: al ser HTTP puro, **aprovecha gratis toda la infraestructura existente** — los CDNs del bloque 11 cachean los segmentos como cachean cualquier fichero (y Netflix llega al extremo lógico: mete servidores propios llenos de contenido *dentro* de las redes de los operadores, para que tu serie viaje solo unos kilómetros), atraviesa cualquier firewall y NAT sin configuración, y escala con la web en vez de contra ella. El coste: esos segundos de buffer implican que el "directo" de Twitch va 5-30 segundos por detrás de la realidad — para latencia de verdad (videollamada) sigue haciendo falta WebRTC (bloque 11). Directo *masivo* y *barato*, o *instantáneo* — otra vez una disyuntiva, no una solución perfecta.

### NTP: la hora de internet

El pariente humilde del GPS que ejecutas sin saberlo: **NTP (Network Time Protocol)** mantiene el reloj de todo ordenador conectado, y su problema es bonito — sincronizar la hora **a través de una red cuya latencia es variable y desconocida** (bloque 5). Si el servidor te dice "son las 12:00:00" y el mensaje tardó 40 ms en llegar, ya es tarde cuando lo lees; y no sabes si tardó 40.

La solución es elegante: el cliente marca cuándo pregunta, el servidor marca cuándo recibe y cuándo responde, el cliente marca cuándo llega la respuesta — cuatro sellos de tiempo de los que se despeja el desfase del reloj *asumiendo que la ida tardó lo mismo que la vuelta*. Esa asimetría no medible es precisamente su límite de precisión (milisegundos por internet). La jerarquía completa cierra el círculo del temario: los servidores NTP de *stratum* 1 toman la hora de relojes atómicos y del GPS (sección anterior) — así que la hora de tu portátil desciende, por una cadena de servidores, de los mismos relojes espaciales que te posicionan. Y el bloque 12 ya te contó la otra mitad: milisegundos de precisión no bastan para ordenar eventos distribuidos, por eso existen los relojes lógicos.

### eSIM y el iPhone sin ranura: un cierre de círculo

Un epílogo breve que une bloques 6, 9 y 15: la eSIM no es solo "SIM sin plástico". Al volverse **software aprovisionable remotamente** (un perfil que se descarga con un QR), la identidad móvil adquiere propiedades de cloud: cambiar de operador sin tocar hardware, tener varios perfiles a la vez (línea personal y de empresa en el mismo móvil), y activar una línea local al aterrizar en otro país en dos minutos — el roaming del bloque 6 convertido en decisión de usuario. La seguridad sigue descansando en el mismo hardware inviolable (el secreto sigue sin poder salir del chip), pero el aprovisionamiento se volvió una API. Es un microcosmos del temario: hardware criptográfico (bloque 9), identidad de red (bloque 6) y filosofía cloud (bloque 15) en un chip de milímetros.

## Ejercicio práctico

Disecciona el streaming adaptativo en tu propio navegador:

1. Abre un directo de Twitch o un vídeo de YouTube con DevTools → Network abierto, filtra por el tipo de peticiones que se repiten y observa: cada pocos segundos, una petición HTTP nueva por el siguiente segmento (`.ts`, `.m4s` o similar). Ese goteo *es* el streaming.
2. Busca el fichero manifest (filtra por `m3u8` en HLS): ábrelo — texto plano listando calidades disponibles y URLs de segmentos. Estás leyendo el índice que el reproductor consulta.
3. El experimento de verdad: mientras el vídeo corre, estrangula la conexión (DevTools → Network → throttling → "Fast 3G") y observa la cascada — los segmentos empiezan a tardar, el reproductor baja de calidad (velo en "estadísticas para nerds" en YouTube: clic derecho sobre el vídeo), y las peticiones siguientes son de ficheros más pequeños. Acabas de ver la adaptación al canal del bloque 2, ejecutándose en tu pestaña.
4. Extra de un minuto: `sntp time.apple.com` (macOS) te muestra el desfase de tu reloj en vivo — NTP funcionando.

## Autoevaluación

1. ¿Por qué Netflix eligió HTTP a trozos en vez de un protocolo de streaming dedicado? Enumera tres ventajas concretas y el precio que paga.
2. El "directo" de Twitch va 15 segundos tarde y una videollamada no puede permitírselo. ¿Qué decisión de arquitectura separa ambos mundos?
3. ¿Cómo estima NTP el desfase de tu reloj sin conocer la latencia de la red, y qué suposición marca su límite de precisión?
4. El examen final del temario: elige VoIP, GPS, TDT o streaming, y explícaselo a alguien no técnico en cinco minutos usando solo analogías. Si lo consigues sin atascarte, este temario ha cumplido su función.

---

**Navegación:** [⬅️ Bloque 15 — Cloud computing (fundamentos conceptuales)](15-cloud-fundamentos.md) · [🏠 Índice](README.md) · [Índice ➡️](README.md)
