# Bloque 3 — Electrónica de comunicaciones (nivel de sistema, no de circuito)

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
