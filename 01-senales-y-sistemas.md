# Bloque 1 — Señales y sistemas (lo mínimo conceptual)

## Analógico vs digital, y por qué digitalizamos

Una señal **analógica** es una magnitud física que varía de forma continua (la presión del aire al hablar, el voltaje que sale de un micrófono). Una señal **digital** representa esa misma información con un número finito de valores discretos, casi siempre bits (0 y 1).

¿Por qué convertir todo a digital si el mundo es analógico? Tres razones prácticas:

- **Ruido**: una señal analógica se degrada cada vez que se transmite, amplifica o almacena — el ruido se acumula y es imposible de separar de la señal original. Una señal digital, en cambio, se puede **regenerar**: mientras el ruido no sea tan grande como para confundir un 0 con un 1, puedes reconstruir la señal original perfecta, tantas veces como haga falta.
- **Procesado**: es mucho más fácil comprimir, cifrar, corregir errores o manipular información cuando está en forma de números que cuando es una onda continua.
- **Uniformidad**: da igual si el dato original es voz, vídeo, texto o un sensor — una vez digitalizado, todo es la misma cosa (bits) y se puede mezclar, almacenar y transmitir con la misma infraestructura.

El precio a pagar es que hace falta convertir (ADC/DAC, muestrear, cuantizar) y eso introduce cierta pérdida de información controlada — pero es un precio que casi siempre merece la pena.

## Dominio del tiempo vs dominio de la frecuencia

Normalmente pensamos en una señal como "algo que cambia con el tiempo" — un gráfico de amplitud frente a tiempo. Esa es la vista en el **dominio del tiempo**.

Pero existe otra forma de mirar la misma señal: en vez de preguntar "¿cuánto vale en cada instante?", preguntamos "¿de qué frecuencias está hecha, y cuánta 'cantidad' hay de cada una?". Esa es la vista en el **dominio de la frecuencia**.

La idea central (Fourier) es que **cualquier señal, por complicada que parezca, se puede descomponer en una suma de ondas sinusoidales simples de distintas frecuencias**. Un acorde de piano es la suma de varias notas puras; la voz humana es la suma de muchas frecuencias con distinta intensidad. El dominio de la frecuencia simplemente te dice "cuánto hay de cada nota".

¿Por qué importa esto en telecomunicaciones? Porque casi todo lo que hacemos — filtrar, modular, compartir el medio entre varios usuarios, entender cuánta "información cabe" — se entiende y se diseña mucho más fácilmente mirando en frecuencia que mirando en tiempo. Es un cambio de perspectiva, no una operación matemática que necesites saber calcular a mano.

## Ancho de banda

El **ancho de banda** es el rango de frecuencias que ocupa una señal (o que un canal es capaz de transportar). Si una señal de voz ocupa frecuencias entre 300 Hz y 3400 Hz, su ancho de banda son unos 3 kHz.

La intuición clave: **más ancho de banda disponible = más información puedes mandar por unidad de tiempo**. Es como comparar una carretera de un carril con una autopista de ocho carriles: ambas "llegan" al mismo sitio, pero la autopista mueve más coches (bits) a la vez. Esta es la razón última por la que el 5G usa frecuencias más altas: ahí hay más "espacio" (ancho de banda) libre para usar, aunque como precio esas frecuencias altas viajan peor (bloque 4).

## Muestreo y el teorema de Nyquist

Para convertir una señal analógica en digital hay que **muestrear**: medir su valor en instantes de tiempo discretos, muy seguidos, en vez de tenerla "continuamente".

La pregunta obvia es: ¿con qué frecuencia hay que muestrear para no perder información? El teorema de Nyquist-Shannon responde: **hay que muestrear al menos al doble de la frecuencia máxima que contiene la señal**. Si la voz llega hasta 4 kHz, hay que muestrear a 8 kHz como mínimo (por eso la telefonía clásica usa 8000 muestras/segundo).

La intuición (sin demostración): si muestreas más lento que eso, distintas señales de frecuencias distintas producen exactamente las mismas muestras — no hay forma de distinguirlas después. Esto se llama **aliasing**, y es el mismo fenómeno por el que las ruedas de un coche parecen girar al revés en una película (la cámara "muestrea" a una frecuencia insuficiente para el giro real de la rueda).

## Ruido y SNR

El **ruido** es cualquier perturbación no deseada que se mezcla con la señal útil: interferencias electromagnéticas, calor de los componentes electrónicos, otras transmisiones cercanas.

La **relación señal-ruido (SNR, signal-to-noise ratio)** compara cuánta señal útil hay frente a cuánto ruido hay, normalmente en decibelios (dB). Es, con diferencia, **la métrica más importante en comunicaciones**: determina cuántos bits por segundo puedes transmitir de forma fiable (está directamente ligada a la capacidad de canal de Shannon, bloque 2), qué tan lejos puede llegar una señal de radio, y qué calidad final tendrá una llamada o una conexión de datos.

Regla de oro para recordar: **casi cualquier problema de comunicaciones se puede reformular como "cómo mejorar el SNR" o "cómo vivir con el SNR que tienes"** — más potencia, mejor antena, menos interferencia, códigos de corrección de errores más eficientes... todo apunta al mismo sitio.

---

## Profundización

### Decibelios: el idioma en que se habla todo esto

Los **decibelios (dB)** son omnipresentes en telecomunicaciones y conviene entenderlos de una vez, porque no son una unidad sino una **forma de expresar cocientes en escala logarítmica**. La chuleta mínima, que cubre el 95% de los usos:

- **+3 dB = el doble** de potencia; **−3 dB = la mitad**.
- **+10 dB = ×10**; **+20 dB = ×100**; **+30 dB = ×1000**.
- Los dB **se suman** donde las potencias se multiplicarían: una señal que pasa por un amplificador de +20 dB y luego un cable que pierde −3 dB acaba con +17 dB netos. Esta es la razón de usarlos: convierten cadenas de multiplicaciones en sumas que puedes hacer de cabeza.

¿Por qué escala logarítmica? Porque las magnitudes en comunicaciones abarcan rangos brutales: entre la potencia que emite tu router WiFi y la que llega a tu móvil en la otra punta de casa puede haber un factor de mil millones. Decir "−90 dB" es manejable; decir "0,000000001 veces" no. Cuando veas dBm (potencia respecto a 1 milivatio) o dBi (ganancia de antena respecto a una ideal), es la misma idea con una referencia fija.

### Cuantización: el segundo paso de la digitalización

Muestrear (medir en instantes discretos) es solo la mitad de digitalizar. La otra mitad es **cuantizar**: cada muestra medida, que en principio puede valer cualquier cosa, se redondea al valor más cercano de una lista finita de niveles — y ese redondeo es el que se representa con bits. Con 8 bits tienes 256 niveles posibles; con 16 bits, 65.536; con 24 bits, unos 16 millones.

El redondeo introduce un pequeño error inevitable, el **ruido de cuantización**: la diferencia entre el valor real y el nivel elegido. Más bits = niveles más finos = menos error. Esto explica cifras que ya conoces: el CD usa 16 bits y 44.100 muestras/segundo (Nyquist sobre los ~20 kHz que oye el ser humano, con margen); el audio "de estudio" usa 24 bits no porque suene mejor al oído, sino para tener margen de manipulación antes de que el ruido de cuantización acumulado sea audible.

### El espectrograma: las dos vistas a la vez

Hay una tercera representación que combina tiempo y frecuencia: el **espectrograma**, un mapa donde el eje horizontal es tiempo, el vertical es frecuencia, y el color indica cuánta energía hay en cada frecuencia en cada instante. Es la herramienta con la que "se ve" la voz (las vocales aparecen como bandas horizontales), se identifican pájaros por su canto, y Shazam reconoce canciones (su algoritmo extrae huellas del espectrograma). Es la forma más rápida de desarrollar intuición sobre Fourier: ver señales reales descompuestas en frecuencias, en movimiento.

## Ejercicio práctico

Instala **Audacity** (gratis) o abre cualquier editor de audio online con vista de espectrograma:

1. Graba 5 segundos de tu voz y mira la forma de onda (dominio del tiempo) — verás poco más que un garabato.
2. Cambia a la vista de espectrograma — de repente se distinguen las vocales, las consonantes sibilantes (energía en frecuencias altas) y el tono grave de fondo.
3. Silba una nota constante y grábala: en el espectrograma aparecerá como una línea horizontal casi pura — la señal más parecida a un seno que puede producir tu cuerpo.

Con esto habrás "visto" la diferencia entre tiempo y frecuencia con tus propios datos, que vale más que cualquier definición.

## Autoevaluación

1. ¿Por qué una señal digital se puede regenerar sin acumular ruido y una analógica no?
2. Un enlace tiene ganancia de +30 dB y luego pierde −13 dB en cables y conectores. ¿Cuál es el factor neto, aproximadamente, sin calculadora?
3. ¿Por qué el teléfono clásico muestreaba a 8 kHz y el CD a 44,1 kHz? ¿Qué decidió cada cifra?
4. Explica el aliasing con el ejemplo de las ruedas de coche en vídeo, sin usar la palabra "frecuencia" más de una vez.
