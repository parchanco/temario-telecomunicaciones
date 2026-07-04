# Bloque 6 — Redes móviles y sistemas celulares

## La idea de célula y la reutilización de frecuencias

El problema que resuelven las redes móviles es sutil: hay **espectro limitado** (bloque 4) pero **millones de usuarios** que quieren usarlo a la vez, en una superficie geográfica enorme. La solución, que le da nombre a la propia tecnología, es dividir el territorio en **células** (celdas): zonas relativamente pequeñas, cada una servida por una estación base con un alcance limitado.

La genialidad del sistema celular no es solo "poner muchas antenas", sino que permite **reutilizar las mismas frecuencias** en células distintas, siempre que estén suficientemente alejadas entre sí como para no interferirse. Es decir: la misma porción de espectro que usa una antena en un barrio se puede volver a usar en otro barrio lejano, sirviendo a otro grupo de usuarios completamente distinto, sin que se molesten mutuamente porque la señal de una ya se ha atenuado lo suficiente (bloque 4) al llegar a la zona de la otra.

Esto es lo que permite que una red móvil **escale geográficamente**: para dar servicio a más usuarios en una ciudad densa, no hace falta más espectro (que es fijo y escaso) — basta con hacer las células más pequeñas y poner más estaciones base, cada una sirviendo a menos usuarios y por tanto pudiendo darles más capacidad individual. Por eso en el centro de una ciudad las antenas están mucho más juntas que en una zona rural.

## Evolución generacional: qué problema resolvió cada una

Es fácil perderse en la sopa de siglas (2G, 3G, 4G, 5G), pero cada generación se entiende mejor si te preguntas **qué limitación de la anterior vino a resolver**:

- **1G (analógica)**: la primera telefonía móvil, puramente analógica y sin cifrado ni datos — solo voz, de baja calidad y poco segura.
- **2G (GSM)**: digitalizó la voz (bloque 1) — mejor calidad, cifrado básico, y de propina permitió los SMS y una velocidad de datos muy limitada (suficiente para texto, no para navegar).
- **3G (UMTS)**: pensada explícitamente para **datos móviles** — llevar internet "de verdad" al móvil, con velocidades que ya permitían navegar por la web y streaming básico.
- **4G (LTE)**: rediseñó la red pensando en **banda ancha móvil**: mucha más velocidad y menor latencia, con una arquitectura simplificada orientada completamente a IP (todo, incluida la voz — VoLTE — pasa a ser datos).
- **5G**: no resuelve un único problema, sino tres a la vez, con perfiles de red distintos según el caso de uso (bloque 10 profundiza): banda ancha móvil mejorada (más velocidad todavía, aprovechando frecuencias altas), baja latencia ultra-fiable (para aplicaciones críticas, coches autónomos, industria), y comunicaciones masivas de IoT (millones de sensores de bajo consumo conectados a la vez).

La lección conceptual: **cada generación no es solo "más rápida"**, sino que responde a una necesidad de uso distinta que emergió en su momento — voz digital, luego internet móvil, luego banda ancha real, luego casos de uso especializados más allá del móvil de una persona.

## Handover: por qué no se corta la llamada al moverte

Cuando te mueves (en coche, caminando) y sales del alcance de una célula para entrar en otra, la red tiene que **transferir tu conexión de una estación base a otra sin interrupción perceptible**. A esto se le llama **handover** o traspaso.

La idea de fondo: mientras estás conectado a una célula, tu teléfono está constantemente midiendo también la calidad de señal de las células vecinas (sin que tú lo notes). Cuando la red detecta que una célula vecina ofrece mejor señal que la actual, coordina el cambio: prepara los recursos en la nueva célula, y conmuta tu sesión activa hacia ella, típicamente en milisegundos. Es parecido a un relevo en una carrera: el testigo (tu llamada o tu conexión de datos) se pasa de una mano a otra sin que la carrera (tu llamada) se detenga.

Esto es más complejo de lo que parece porque hay que resolver el "cuándo" con cuidado: cambiar de célula demasiado pronto o demasiado tarde, o hacerlo repetidamente cerca del límite entre dos células (efecto "ping-pong"), degrada la experiencia — por eso los criterios de handover incluyen márgenes e histéresis (no cambiar hasta que la diferencia de señal sea claramente significativa y estable).

## Arquitectura básica: quién hace qué

Simplificando mucho, una red móvil tiene tres grandes bloques:

- **Terminal (UE, User Equipment)**: tu móvil, tablet o módem — el dispositivo que se conecta.
- **Red de acceso radio (RAN)**: las estaciones base (torres/antenas) que dan cobertura de radio a los terminales cercanos y gestionan aspectos como el handover.
- **Core de red (Core Network)**: el "cerebro" central, normalmente en centros de datos del operador, que se encarga de cosas que no tienen que ver con la radio en sí: autenticar quién eres (para facturarte y evitar fraude), decidir cómo encaminar tu tráfico hacia internet o hacia otra llamada, gestionar tu movilidad a nivel de red (no solo de radio) mientras te desplazas entre zonas más amplias, y aplicar políticas (por ejemplo, límites de velocidad según tu tarifa).

La separación es de nuevo el mismo patrón de capas que ya viste en el bloque 0 y el bloque 5: la parte de radio (RAN) se ocupa solo de "cómo llega la señal por el aire", y el core se ocupa de toda la lógica de red, autenticación y encaminamiento — de forma que se pueden actualizar o rediseñar por separado. De hecho, esta separación es la que ha permitido que el 5G "virtualice" buena parte del core en software genérico ejecutándose en servidores estándar, en vez de hardware especializado propietario (idea que se retoma en el bloque 10 con SDN/NFV).
