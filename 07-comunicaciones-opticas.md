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

---

## Profundización

### El EDFA: el amplificador que hizo posible internet global

Incluso con las pérdidas bajísimas de la fibra (~0,2 dB/km), tras cien o doscientos kilómetros la señal necesita amplificarse. La solución primitiva era carísima: convertir la luz a electricidad, regenerar la señal y volver a convertirla a luz — un equipo completo por cada longitud de onda, cada pocas decenas de kilómetros.

El **EDFA (amplificador de fibra dopada con erbio)** cambió la historia: un tramo de fibra especial, "bombeado" con un láser auxiliar, que **amplifica la luz directamente como luz**, sin conversión eléctrica — y, crucialmente, amplifica **todos los colores del WDM a la vez** con un solo dispositivo. Sin el EDFA, cada nuevo color añadido a un cable submarino habría exigido regeneradores adicionales en cada punto de amplificación bajo el océano; con él, la capacidad de un cable ya tendido se multiplica cambiando solo los equipos de los extremos. Es difícil exagerar su importancia: el abaratamiento brutal del tráfico intercontinental que hizo viable el internet global de los 2000 es, en gran parte, este dispositivo.

Nota el matiz frente al bloque 1: el EDFA amplifica de forma *analógica* (amplifica también el ruido acumulado), así que en distancias extremas sigue haciendo falta regenerar digitalmente de vez en cuando. La cadena real es un compromiso entre amplificadores baratos y regeneradores caros.

### Cables submarinos: la columna vertebral real de internet

Más del 95% del tráfico intercontinental viaja por unos ~500 cables submarinos — no por satélite, como mucha gente asume. Un cable moderno tiene el grosor de una manguera de jardín en aguas profundas (el blindaje grueso solo hace falta cerca de la costa, donde anclas y redes de pesca causan la mayoría de los ~100 cortes anuales), contiene unos pocos pares de fibra, y lleva integrados EDFAs alimentados eléctricamente desde las estaciones de los extremos con corriente que viaja por el propio cable.

Dos ideas que conviene retener: (1) la **capacidad de un cable se multiplica sin tocarlo** — los cables tendidos en los 2000 transportan hoy cientos de veces su capacidad de diseño original, solo actualizando los transceptores de tierra (más colores WDM, modulaciones más densas — el bloque 2 aplicado a láseres); (2) la **geografía de los cables es geopolítica**: las rutas evitan zonas de conflicto, los países compiten por ser puntos de amarre, y la redundancia de rutas es un asunto de seguridad nacional — cuando un barco arrastra un ancla en el sitio equivocado, países enteros pierden capacidad.

### GPON: cómo llega realmente la fibra a tu casa

La fibra de tu casa no es un hilo dedicado hasta la central. Las redes **GPON (Gigabit Passive Optical Network)** usan una arquitectura de árbol: una sola fibra sale de la central y se va dividiendo con **splitters pasivos** — simples divisores de luz sin electrónica ni alimentación, de ahí "pasiva" — hasta llegar a 32 o 64 hogares que **comparten** la fibra troncal.

¿Cómo comparten? Con las herramientas del bloque 2: en bajada, la central emite a todos y cada router descarta lo que no es suyo (cifrado, por eso importa); en subida, cada hogar tiene asignadas ranuras de tiempo (TDM puro) para no pisarse. Esto explica el diseño económico del FTTH — el coste de la electrónica se concentra en los extremos y la planta exterior es vidrio pasivo que no falla ni consume — y de paso desmonta un mito comercial: tu "fibra simétrica de 1 Gbps" comparte árbol con tus vecinos, y funciona porque casi nunca todos transmiten a tope a la vez (la misma apuesta estadística de la conmutación de paquetes del bloque 5).

## Ejercicio práctico

1. Abre el mapa interactivo de cables submarinos en `submarinecablemap.com` (gratuito). Busca: ¿por dónde sale el tráfico de España hacia América? ¿Cuántos cables amarran en tu país y dónde? Elige un cable y mira su año, longitud y propietarios — verás nombres como Google y Meta, que ya poseen o coposeen buena parte de los cables nuevos (les sale más barato que alquilar).
2. Si tienes fibra en casa: localiza el router del operador (ONT integrada o separada) y busca en su panel de estado la potencia óptica recibida, típicamente entre −15 y −25 dBm. Compárala con los −40/−80 dBm del WiFi del bloque 4 — y piensa que esa luz ha atravesado varios splitters pasivos desde la central.

## Autoevaluación

1. ¿Por qué el EDFA fue tan transformador? ¿Qué dos conversiones elimina y qué amplifica "de más" como precio?
2. ¿Cómo puede un cable submarino de hace 20 años transportar hoy cien veces más datos sin haberlo tocado?
3. En una red GPON, ¿qué evita que veas el tráfico de bajada de tus vecinos, y qué evita que vuestras subidas colisionen? (Son dos mecanismos distintos.)
4. ¿Por qué monomodo para largas distancias? Explica la dispersión modal con la analogía que prefieras.

---

**Navegación:** [⬅️ Bloque 6 — Redes móviles y sistemas celulares](06-redes-moviles.md) · [🏠 Índice](README.md) · [Bloque 8 — Procesado digital de señal (a nivel de idea) ➡️](08-procesado-senal.md)
