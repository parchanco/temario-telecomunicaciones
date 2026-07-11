# Bloque 3 — Electrónica de comunicaciones (nivel de sistema, no de circuito)

[← 9. Tema anterior](02-teoria-comunicacion.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [Mi progreso](PROGRESO.md) · [11. Tema siguiente →](04-antenas-propagacion.md)

> **Objetivo del bloque:** recorrer la cadena completa de un transceptor y justificar la función y los límites de cada bloque.

> Este bloque se queda deliberadamente en el nivel de "qué bloque hace qué y por qué", sin entrar en el diseño de circuitos — eso es un temario aparte (electrónica analógica/RF de diseño) mucho más matemático.

## Anatomía de un transceptor de radio

Un transceptor (transmisor + receptor) es el aparato que convierte bits en ondas de radio y viceversa. A nivel de bloques, un receptor típico sigue esta cadena:

```
Antena → LNA (amplificador de bajo ruido) → Mezclador → Filtro → ADC → Procesado digital
```

- La **antena** capta la señal de radio del aire (muy débil, del orden de microvoltios).
- El **LNA (Low Noise Amplifier)** amplifica esa señal débil lo primero de todo, con el mínimo ruido añadido posible — porque cualquier ruido introducido aquí, al principio de la cadena, se amplificará también en las etapas siguientes junto con la señal. Es la etapa más crítica para la sensibilidad del receptor.
- El **mezclador** combina la señal recibida con una señal generada localmente (oscilador local) para trasladarla a una frecuencia más cómoda de procesar (ver heterodino, abajo).
- El **filtro** elimina todo lo que no sea la señal de interés (otras emisoras, ruido fuera de banda).
- El **ADC (conversor analógico-digital)** convierte la señal filtrada en bits para que el resto del sistema la procese digitalmente.

El transmisor hace básicamente el camino inverso: DAC → mezclador (sube la señal a la frecuencia de transmisión) → amplificador de potencia → antena.

## Frecuencia intermedia y el receptor heterodino

Un problema práctico: las señales de radio útiles pueden estar en frecuencias muy altas (cientos de MHz o GHz), donde es difícil y caro construir filtros y amplificadores precisos directamente.

La solución clásica, inventada hace más de 100 años y todavía dominante, es el **receptor heterodino**: en vez de procesar la señal directamente en su frecuencia original, se **mezcla** con una señal generada localmente para "bajarla" a una frecuencia fija intermedia (la **FI**, frecuencia intermedia), mucho más baja y manejable. Ahí es donde se hacen el filtrado fino y la amplificación principal, antes de convertir a digital.

La ventaja clave: en vez de necesitar un filtro distinto y muy preciso para cada frecuencia posible de recepción, solo necesitas un filtro fijo, bien optimizado, en la frecuencia intermedia — y para "sintonizar" una emisora distinta simplemente cambias la frecuencia del oscilador local que usas para mezclar, no el hardware de filtrado. Es la misma idea que usa cualquier radio con dial: giras el dial y lo que realmente cambia es el oscilador local, no el resto del circuito.

## Amplificadores: ganancia, saturación, distorsión

Un **amplificador** aumenta la potencia de una señal. Tres conceptos que aparecen constantemente al hablar de ellos:

- **Ganancia**: cuánto amplifica, normalmente en dB (una ganancia de 20 dB significa multiplicar la potencia por 100).
- **Saturación**: todo amplificador real tiene un límite de salida — a partir de cierta potencia de entrada, ya no puede seguir amplificando proporcionalmente y la señal de salida se "aplana" (clipping). Es lo mismo que le pasa a un altavoz cuando subes demasiado el volumen y empieza a distorsionar.
- **Punto de compresión / distorsión**: cuando un amplificador se acerca a su saturación, empieza a generar componentes de frecuencia que no estaban en la señal original (armónicos, intermodulación) — esto ensucia el espectro y puede interferir con canales vecinos. Por eso en los sistemas reales se opera con margen respecto al punto de saturación ("back-off"), sacrificando algo de eficiencia energética a cambio de una señal más limpia.

La tensión de fondo aquí es, otra vez, un compromiso: un amplificador más eficiente energéticamente suele operar más cerca de la saturación, pero eso genera más distorsión. Es una disyuntiva de ingeniería que aparece en el diseño de cualquier estación base o terminal móvil.

## Osciladores y PLLs

Un **oscilador** es un circuito que genera una señal periódica (una onda) de una frecuencia determinada por sí solo, sin necesitar una señal de entrada — es la "fuente de ritmo" de cualquier sistema de comunicación: se necesita para generar la portadora que se transmite, para el oscilador local del mezclador del receptor, para sincronizar relojes digitales, etc.

El problema es que un oscilador libre no es muy preciso ni estable (varía con la temperatura, con el tiempo). Ahí entra el **PLL (Phase-Locked Loop, bucle de enganche de fase)**: un sistema de realimentación que "engancha" la frecuencia de un oscilador a una referencia muy precisa y estable (normalmente un cristal de cuarzo), corrigiendo continuamente cualquier desviación.

No hace falta entender el diseño interno del PLL — la idea que hay que quedarse es: **los PLL son la pieza que permite generar frecuencias precisas y estables, y también "seguir" o sincronizarse con la frecuencia de una señal recibida** (por ejemplo, para que el receptor sepa exactamente en qué frecuencia y fase está transmitiendo el otro extremo). Aparecen en prácticamente cualquier equipo de radio, en relojes de procesadores, y en sistemas de sincronización de redes.

---

## Profundización

### Ruido térmico y figura de ruido: el suelo bajo el que no se puede bajar

Todo componente electrónico a temperatura ambiente genera **ruido térmico**: los electrones se agitan por el simple hecho de estar calientes, y esa agitación es una señal aleatoria inevitable. Esto marca un **suelo de ruido** físico: por debajo de cierta potencia, una señal recibida es indistinguible de la agitación térmica del propio receptor. Es la razón última por la que no basta con "amplificar más" una señal débil — amplificas el ruido junto con ella.

De ahí la **figura de ruido**: una medida (en dB) de cuánto ruido *añade* cada componente además del que ya venía. Y de ahí también una regla de diseño que ya viste con el LNA: **el primer eslabón de la cadena domina el ruido total** — por eso los radiotelescopios y las estaciones terrenas de satélite llegan a enfriar criogénicamente sus primeros amplificadores: bajar la temperatura baja literalmente el ruido térmico. En el extremo cotidiano, es también la razón por la que un amplificador de antena de TV barato mal colocado puede *empeorar* la recepción: si añade más ruido del que compensa, el SNR neto baja.

### Duplexación: cómo se habla y escucha a la vez

Un detalle que se da por sentado: tu móvil transmite y recibe *simultáneamente* (hablas y escuchas en una llamada). ¿Cómo, si su propio transmisor es millones de veces más potente que la señal que intenta recibir? Dos estrategias:

- **FDD (Frequency Division Duplex)**: transmitir y recibir en frecuencias distintas, con filtros muy selectivos (el *duplexor*) separándolas. Es como hablar y escuchar en dos idiomas distintos a la vez.
- **TDD (Time Division Duplex)**: transmitir y recibir en la misma frecuencia pero alternando en el tiempo, tan rápido que parece simultáneo. Ventaja moderna clave: se puede repartir el tiempo de forma asimétrica (más ranuras de bajada que de subida, que es como realmente usamos internet), y por eso la mayoría del 5G en bandas nuevas es TDD.

### SDR: la radio hecha software

La tendencia que ha transformado este campo: en el receptor clásico, cada bloque (mezclador, filtro, demodulador) era hardware físico. En una **SDR (Software Defined Radio)**, el ADC se coloca lo más cerca posible de la antena y **todo lo demás se hace en software**: filtrar, mezclar, demodular son ahora funciones de procesado digital (bloque 8) que puedes cambiar recargando un programa, no resoldando un circuito.

Esto importa por dos razones. Práctica: una misma placa barata puede ser hoy un receptor de FM, mañana un decodificador de aviones y pasado un analizador de espectro. Conceptual: es la demostración física de que **modulación, filtrado y demodulación son matemáticas, no electrónica** — la electrónica solo era la forma de calcularlas antes de que el cómputo fuera barato. Las estaciones base 5G modernas son, en gran parte, SDRs industriales.

## Ejercicio práctico

Compra un **RTL-SDR** (~30€, un USB que nació como sintonizador de TV) o usa un receptor SDR público online (busca "WebSDR" — hay receptores reales controlables desde el navegador, gratis):

1. Abre el espectro en vivo y "ve" las emisoras de FM como jorobas de energía — cada joroba es el ancho de banda de una emisora (bloque 1, en vivo).
2. Sintoniza una y observa cómo el software la mezcla, filtra y demodula — toda la cadena del receptor de este bloque, visible en pantalla.
3. Fíjate en el suelo de ruido: la "hierba" del fondo del espectro es el ruido térmico de esta sección. Toda señal visible sobresale de esa hierba; la que no sobresale, no existe para el receptor.

Es probablemente la mejor relación coste/intuición de todo el temario.

## Autoevaluación

1. ¿Por qué el componente más crítico para la sensibilidad de un receptor es el primero de la cadena y no el más potente?
2. ¿Qué problema resuelve el receptor heterodino y qué pieza cambia realmente cuando "giras el dial"?
3. ¿Por qué amplificar más no ayuda con una señal por debajo del suelo de ruido?
4. FDD vs TDD: ¿cuál encaja mejor con un tráfico de internet muy asimétrico y por qué?

---

## Continúa el recorrido

Antes de avanzar, intenta responder la autoevaluación sin mirar y registra la práctica en [PROGRESO.md](PROGRESO.md). Si puedes explicar la idea central, dibujarla y conectarla con un bloque anterior, continúa.

[← 9. Tema anterior](02-teoria-comunicacion.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [11. Tema siguiente →](04-antenas-propagacion.md)
