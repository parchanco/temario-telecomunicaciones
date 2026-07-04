# Bloque 7 — Comunicaciones ópticas

## Por qué la fibra permite tanto ancho de banda

Una fibra óptica transmite información usando **luz** en vez de electricidad. La razón de fondo por la que esto permite tanto ancho de banda (bloque 1) es una cuestión de frecuencia: la luz que se usa en fibra óptica (infrarroja, cercana al espectro visible) oscila a frecuencias del orden de **cientos de terahercios** — muchísimo más alto que cualquier frecuencia de radio usada en comunicaciones inalámbricas (que llegan como mucho a decenas de gigahercios).

Recordando la intuición del bloque 1 ("más ancho de banda disponible = más información puedes mandar"): al trabajar a una frecuencia base tan altísima, hay muchísimo más "espacio" alrededor de esa frecuencia para modular información, sin ni siquiera acercarse a los límites físicos de la fibra. A esto se suma que la fibra es un **medio guiado y cerrado** (a diferencia del aire): no sufre interferencias de otras señales externas, ni se ve afectada por el clima, obstáculos o multitrayecto (bloque 4) — la señal viaja confinada dentro del propio cable, reflejándose internamente en sus paredes, con muy poca pérdida por kilómetro. La combinación de "frecuencia base altísima" + "medio limpio y confinado" es lo que da a la fibra órdenes de magnitud más capacidad que el cobre o el aire.

## Fibra monomodo vs multimodo

La luz puede viajar por el núcleo de la fibra siguiendo distintos "caminos" o **modos** (ángulos de reflexión interna ligeramente distintos). Esto da lugar a dos tipos de fibra:

- **Multimodo**: el núcleo es relativamente ancho, y permite que la luz viaje por varios modos (caminos) simultáneamente. El problema es que, como cada modo recorre un camino ligeramente distinto, no todos llegan al final exactamente al mismo tiempo — esto emborrona la señal a medida que se acumula distancia (un fenómeno llamado dispersión modal). Por eso la fibra multimodo se usa para **distancias cortas** (dentro de un edificio, un centro de datos), donde es más barata de instalar y de conectorizar.
- **Monomodo**: el núcleo es mucho más estrecho, lo suficiente para forzar a la luz a viajar por un único modo (un único camino posible). Esto elimina el problema de la dispersión modal, permitiendo transmitir a **distancias muy largas** (decenas o cientos de kilómetros, incluso miles con amplificación) sin que la señal se degrade tanto. Es la que se usa en las redes troncales de internet, en cables submarinos, y cada vez más en el tramo final hasta las viviendas (FTTH).

La idea clave para recordar sin memorizar el detalle físico: **es la misma disyuntiva de siempre entre coste/sencillez y alcance** — multimodo es más barata y suficiente para trayectos cortos, monomodo es necesaria (y algo más cara) cuando hay que recorrer distancias largas sin degradar la señal.

## WDM: multiplexación por longitud de onda

Igual que en el bloque 2 se vio que se puede compartir un medio entre varios usuarios dividiendo frecuencias (FDM), tiempo (TDM) o códigos (CDM), en fibra óptica existe una técnica análoga pensada específicamente para luz: **WDM (Wavelength Division Multiplexing)**.

La idea: en vez de mandar un único "color" (longitud de onda) de luz por la fibra, se mandan **varios colores distintos simultáneamente**, cada uno modulado con su propia información independiente, todos viajando a la vez por el mismo hilo de fibra sin interferirse entre sí — de forma parecida a como un prisma puede separar la luz blanca en varios colores, y cada color puede llevar un "mensaje" distinto sin mezclarse con los demás. En el extremo receptor, otro dispositivo óptico (similar a un prisma en su función) separa de nuevo cada color y lo dirige a su receptor correspondiente.

Esta técnica es la que ha permitido multiplicar brutalmente la capacidad de los cables de fibra ya instalados (incluyendo los cables submarinos intercontinentales) sin tener que tender fibra nueva: en vez de añadir más hilos físicos, simplemente se añaden más "colores" de luz aprovechando la fibra existente. Es, en esencia, la versión óptica de "aprovechar mejor el mismo cable" en lugar de "poner más cables".
