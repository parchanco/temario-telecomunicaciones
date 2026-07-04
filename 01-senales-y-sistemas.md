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
