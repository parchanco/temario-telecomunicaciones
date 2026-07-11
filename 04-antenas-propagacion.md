# Bloque 4 — Antenas y propagación

[← 10. Tema anterior](03-electronica-comunicaciones.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [Mi progreso](PROGRESO.md) · [12. Tema siguiente →](07-comunicaciones-opticas.md)

> **Objetivo del bloque:** razonar sobre radiación, cobertura y calidad mediante patrón, polarización, multitrayecto y presupuesto de enlace.

## Qué es una antena, conceptualmente

Una antena es un **transductor**: un dispositivo que convierte energía de un tipo en otro. En este caso, convierte **corriente eléctrica variable en onda electromagnética** (al transmitir) y **onda electromagnética en corriente eléctrica** (al recibir). Es exactamente la misma pieza física haciendo ambas cosas — por eso la misma antena de tu móvil transmite y recibe.

No hace falta entender las ecuaciones de Maxwell para tener la intuición correcta: una corriente eléctrica que oscila (sube y baja de forma periódica) genera a su alrededor un campo electromagnético que también oscila, y ese campo se "desprende" del conductor y se propaga por el espacio como una onda — igual que agitar la mano en el agua genera ondas que se alejan de tu mano. La antena es, literalmente, el trozo de metal diseñado para que esa oscilación se "desprenda" de forma eficiente en vez de quedarse pegada al conductor.

## Patrón de radiación, ganancia y polarización

- **Patrón de radiación**: es un mapa de "hacia dónde" radia más o menos energía una antena. Ninguna antena real radia por igual en todas direcciones; algunas están diseñadas para ser lo más omnidireccionales posible (una antena de estación base clásica, para cubrir una zona amplia), y otras están diseñadas para concentrar toda la energía en una dirección muy estrecha (una antena parabólica de enlace punto a punto o de satélite).
- **Ganancia**: mide cuánto concentra una antena la energía en su dirección preferida, comparada con una antena de referencia que radiara igual en todas direcciones. Más ganancia en una dirección significa, necesariamente, menos energía en las demás direcciones — es un reparto, no un "regalo" de energía extra. Una antena de gran ganancia permite llegar mucho más lejos en una dirección concreta, a costa de tener que apuntarla con precisión.
- **Polarización**: es la orientación del campo eléctrico de la onda (vertical, horizontal, circular...). Para que la comunicación funcione bien, el transmisor y el receptor deben usar antenas con polarizaciones compatibles — es la razón por la que las antenas de WiFi suelen ser verticales y coincidir en orientación entre el router y el dispositivo, y por la que las antenas de TV por satélite tienen una orientación de polarización muy específica.

## Propagación: cómo viaja realmente una onda

En el mundo real, una onda de radio casi nunca viaja en línea recta desde el transmisor hasta el receptor sin más. Los fenómenos clave:

- **Línea de vista (LOS, line of sight)**: el caso ideal, sin obstáculos entre antenas. A frecuencias altas (microondas, mmWave de 5G) esto es casi un requisito, porque estas ondas se comportan casi como la luz: si hay un obstáculo de por medio, la señal se bloquea de forma severa.
- **Reflexión**: la onda rebota en superficies grandes (edificios, el suelo, coches), igual que la luz rebota en un espejo. Esto puede ayudar (llega señal a sitios sin línea de vista directa) o perjudicar (interferencia entre el rayo directo y el reflejado).
- **Difracción**: la onda se "dobla" al pasar cerca del borde de un obstáculo, permitiendo que llegue algo de señal incluso detrás de esquinas o colinas — es más pronunciada cuanto más baja es la frecuencia.
- **Multitrayecto (multipath)**: en un entorno real (una ciudad, un interior), la señal llega al receptor por varios caminos distintos a la vez (directo, reflejado en un edificio, reflejado en el suelo...), cada uno con un retardo y una fase ligeramente distintos. Cuando esas copias se recombinan en la antena receptora, pueden sumarse constructivamente (mejor señal) o destructivamente (la señal se debilita o distorsiona, un efecto llamado *desvanecimiento* o *fading*). Esto explica por qué mover tu móvil unos centímetros a veces cambia radicalmente la cobertura: has pasado de un punto de interferencia destructiva a uno constructivo, o viceversa.

## Atenuación con la distancia y la frecuencia

Toda onda de radio pierde intensidad al alejarse de la fuente, simplemente porque la misma energía se reparte sobre un área cada vez mayor (la energía total se "diluye" en una esfera cada vez más grande, de forma parecida a como una luz se ve más débil cuanto más lejos estás de la bombilla).

Además, **cuanto más alta es la frecuencia, peor viaja la onda** en términos generales: se atenúa más rápido con la distancia y es absorbida más fácilmente por obstáculos (paredes, hojas de árboles, incluso la lluvia en frecuencias muy altas). Esta es la razón práctica detrás de una disyuntiva central en redes móviles:

> Frecuencias bajas → más alcance y mejor penetración en interiores, pero menos ancho de banda disponible (bloque 1) → menos velocidad.
> Frecuencias altas (mmWave del 5G) → muchísimo ancho de banda disponible → muchísima velocidad, pero alcance muy corto y se bloquean con facilidad (una mano delante del móvil puede cortar la señal).

Por eso el 5G en frecuencias altas necesita muchas más antenas, más cercanas entre sí, que las redes 4G de frecuencias bajas: está intercambiando alcance por velocidad.

## El espectro radioeléctrico como recurso escaso

El **espectro radioeléctrico** es el conjunto de todas las frecuencias utilizables para transmitir ondas de radio. A diferencia de otros recursos de ingeniería, el espectro **no se puede fabricar ni ampliar** — solo hay una cantidad finita de "sitio" en cada franja de frecuencias, y dos transmisores usando la misma frecuencia en la misma zona se interfieren mutuamente.

Por esto, el espectro está **regulado por organismos gubernamentales e internacionales** (en España la CNMC/SETSI, a nivel internacional la ITU), que asignan bandas concretas a usos concretos (radiodifusión, móviles, WiFi, radar, militar, satélite...) y conceden **licencias** — a menudo mediante subastas millonarias, como las que los operadores móviles pagan para poder usar ciertas bandas en exclusiva. Bandas como el WiFi (2.4 y 5 GHz) son, en cambio, de "uso libre" dentro de ciertos límites de potencia, precisamente para permitir su uso sin licencia individual. Entender esto explica por qué el 5G, el WiFi 6 o cualquier nueva tecnología de radio siempre viene acompañada de discusiones regulatorias sobre qué banda de frecuencias se le va a asignar.

---

## Profundización

### MIMO: convertir el multitrayecto de enemigo en aliado

La idea más importante de las últimas dos décadas en radio, y la razón de que tu router tenga varias antenas. **MIMO (Multiple Input, Multiple Output)** usa varias antenas en transmisión y recepción a la vez, y consigue algo que parece violar la intuición: **transmitir varios flujos de datos distintos, simultáneamente, en la misma frecuencia**, multiplicando la capacidad sin gastar más espectro.

El truco es que se apoya precisamente en el multitrayecto que en este bloque aparecía como problema: si el entorno está lleno de reflexiones, la señal de cada antena transmisora llega a cada antena receptora por una "mezcla de caminos" ligeramente distinta. Cuando esas mezclas son suficientemente diferentes entre sí, el receptor puede resolver matemáticamente el sistema y separar los flujos — como distinguir varias conversaciones en una sala porque cada una te llega con un eco distinto. Consecuencia curiosa: MIMO funciona *mejor* en entornos con muchos rebotes (interiores, ciudades) que en campo abierto con línea de vista limpia. WiFi moderno, 4G y 5G dependen críticamente de esto.

### Beamforming: la antena que apunta sin moverse

Con un conjunto (*array*) de muchas antenas pequeñas transmitiendo la misma señal con fases ligeramente distintas, las ondas individuales se suman constructivamente en una dirección elegida y se cancelan en las demás — el resultado es un **haz orientable electrónicamente**, sin ninguna pieza móvil, que puede cambiar de dirección en microsegundos y apuntar a cada usuario individualmente.

Es la tecnología que hace viable el 5G de frecuencias altas (la energía se concentra hacia cada usuario en vez de desperdiciarse en todas direcciones, compensando el poco alcance de esas frecuencias) y la que usan las antenas planas de Starlink para seguir satélites que cruzan el cielo en minutos. El principio físico —interferencia constructiva y destructiva de ondas— es el mismo del multitrayecto, otra vez usado a favor en vez de sufrido.

### El presupuesto de enlace: la contabilidad de los dB

El **presupuesto de enlace** (*link budget*) es la herramienta central del diseño de radioenlaces, y con los dB del bloque 1 se reduce a una suma: partes de la potencia transmitida, **sumas** las ganancias (antenas en ambos extremos) y **restas** las pérdidas (propagación por distancia, cables, obstáculos, lluvia si es frecuencia alta), y comparas el resultado con la **sensibilidad** del receptor (la potencia mínima que necesita, dictada por su suelo de ruido, bloque 3). Si sobra margen, el enlace funciona; el margen extra es tu seguro contra imprevistos (desvanecimientos por multitrayecto, clima).

No necesitas calcular presupuestos de enlace — necesitas saber que **cada enlace de radio del planeta, del WiFi de tu salón al enlace con una sonda en Marte, es viable o no según esta suma**. Cuando el WiFi "no llega" al fondo de casa, tu presupuesto de enlace ha entrado en números rojos: cada pared restó sus dB.

## Ejercicio práctico

Instala una app de análisis WiFi (WiFi Analyzer en Android; en macOS mantén pulsado ⌥ y haz clic en el icono de WiFi, o usa la app "Diagnóstico Inalámbrico"):

1. Mira la potencia recibida (RSSI) en dBm junto al router: típicamente −40 a −50 dBm. Aléjate y ve cómo cae; atraviesa dos paredes y verás −70 u −80 dBm. Cada pared son unos −3 a −10 dB — estás midiendo tu presupuesto de enlace en vivo.
2. Compara 2.4 GHz vs 5 GHz desde el mismo punto lejano: verás en números la disyuntiva alcance/capacidad — el 5 GHz (más rápido) llega con menos potencia que el 2.4 GHz a través de las mismas paredes.
3. Mueve el portátil medio metro y observa fluctuar el RSSI sin que nada cambie aparentemente: eso es el multitrayecto reconfigurándose a tu alrededor.

## Autoevaluación

1. ¿Por qué una antena de mucha ganancia no "crea" energía extra? ¿Qué está haciendo en realidad?
2. ¿Por qué MIMO funciona mejor dentro de un edificio que en un descampado con visión directa?
3. Tu WiFi de 5 GHz va más rápido cerca del router pero peor que el de 2.4 GHz en la habitación del fondo. Explica ambas cosas con conceptos de este bloque.
4. ¿Qué hace exactamente el beamforming y por qué es imprescindible para el 5G de ondas milimétricas?

---

## Continúa el recorrido

Antes de avanzar, intenta responder la autoevaluación sin mirar y registra la práctica en [PROGRESO.md](PROGRESO.md). Si puedes explicar la idea central, dibujarla y conectarla con un bloque anterior, continúa.

[← 10. Tema anterior](03-electronica-comunicaciones.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [12. Tema siguiente →](07-comunicaciones-opticas.md)
