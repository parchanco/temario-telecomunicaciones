# Bloque 4 — Antenas y propagación

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
