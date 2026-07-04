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
