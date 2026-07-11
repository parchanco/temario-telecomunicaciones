# Bloque 20 — Programación y arquitectura de computadores

Las telecomunicaciones modernas son sistemas programables. Comprender qué ocurre entre el código, la memoria, el sistema operativo y la red permite razonar sobre rendimiento y fallos sin tratar el software como una caja negra.

## De un problema a un algoritmo

Un algoritmo es una secuencia finita de pasos que transforma entradas en salidas. Un programa añade representaciones concretas, gestión de errores e interacción con el entorno. Dos algoritmos correctos pueden necesitar cantidades muy distintas de tiempo o memoria al crecer los datos.

La complejidad describe esa tendencia de crecimiento, no los segundos exactos. Antes de optimizar, hay que identificar qué recurso limita el sistema y medirlo.

## Cómo se representan los datos

Bits agrupados representan enteros, texto, sonido o instrucciones según una convención. El mismo patrón no tiene significado sin tipo, formato y contexto. El rango de un entero depende del número de bits; los números en coma flotante aproximan reales y pueden acumular error.

Serializar convierte estructuras en bytes para almacenarlas o enviarlas. Formatos textuales como JSON facilitan inspección; formatos binarios suelen ahorrar espacio y procesado, a cambio de menor legibilidad y mayor necesidad de esquema.

## Procesador, memoria y entrada/salida

La CPU ejecuta instrucciones y opera sobre datos cercanos. Registros y cachés son rápidos y pequeños; RAM y almacenamiento, progresivamente mayores y más lentos. El coste de mover datos puede superar al de calcular.

Los periféricos y la interfaz de red trabajan a ritmos propios. Interrupciones y acceso directo a memoria evitan que la CPU tenga que preguntar continuamente si llegó algo.

## Sistema operativo, procesos e hilos

El sistema operativo reparte CPU, memoria y dispositivos entre programas. Un proceso tiene un espacio de memoria aislado; varios hilos comparten recursos dentro de él. La concurrencia mejora uso y capacidad de respuesta, pero introduce carreras, bloqueos y orden no determinista.

Una llamada de sistema cruza la frontera entre aplicación y kernel. Abrir un socket, leer un archivo o esperar datos requiere esa mediación.

## Programación de red

Un socket es una interfaz de software para enviar y recibir datos mediante un protocolo. TCP entrega un flujo de bytes, no mensajes: la aplicación debe definir cómo delimitar cada unidad. UDP conserva datagramas, pero no garantiza entrega ni orden.

Bloqueante, no bloqueante y asíncrono describen cómo espera el programa, no qué protocolo usa. Una arquitectura eficiente combina el modelo de concurrencia con el patrón de carga y los límites del sistema.

## Profundización

### Del código fuente a la ejecución

Un compilador traduce antes de ejecutar; un intérprete procesa mediante otro programa; muchas plataformas combinan bytecode, máquina virtual y compilación en caliente. Todas terminan coordinando instrucciones, memoria y sistema operativo.

### Tiempo real

Un sistema de tiempo real necesita responder antes de un límite conocido; no basta con ser rápido de media. Audio, radio definida por software y control industrial prefieren latencia predecible a throughput máximo.

### Hardware programable y sistemas embebidos

Microcontroladores integran CPU, memoria y periféricos para controlar dispositivos. Una FPGA configura lógica paralela en hardware. La elección depende de consumo, coste, latencia, volumen y facilidad de actualización.

## Ejercicio práctico

Escribe un cliente pequeño que consulte un nombre DNS y realice una petición HTTP, o usa herramientas del sistema si no quieres programar. Registra resolución, conexión, envío, primer byte y descarga. Relaciona cada intervalo con CPU, sistema operativo o red.

## Autoevaluación

1. ¿Por qué un patrón de bits no tiene significado por sí solo?
2. ¿Qué diferencia hay entre latencia de acceso y capacidad de almacenamiento?
3. ¿Qué comparten y qué no comparten dos hilos de un proceso?
4. ¿Por qué TCP obliga a la aplicación a delimitar mensajes?
5. ¿Cuándo “rápido de media” no es suficiente?

---

**Navegación:** [⬅️ Bloque 19 — Electromagnetismo y líneas de transmisión](19-electromagnetismo.md) · [🏠 Índice](README.md) · [Bloque 21 — Automática y control ➡️](21-automatica-control.md)
