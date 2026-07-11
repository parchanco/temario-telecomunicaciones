# Bloque 22 — Audio, imagen y comunicaciones multimedia

Audio, imagen y vídeo conectan señales con percepción humana. Un sistema multimedia no necesita reproducir cada muestra con exactitud: necesita conservar lo que importa para la tarea y para quien lo percibe.

## Sonido y audición

El sonido es una variación de presión que se propaga por un medio. Frecuencia se relaciona con tono; amplitud, con nivel, pero la percepción no es lineal. El oído tiene sensibilidad distinta según frecuencia y puede ocultar un sonido débil cerca de otro más fuerte.

Un micrófono convierte presión en señal eléctrica y un altavoz realiza el camino inverso. Sala, posición y reflexiones forman parte de la cadena tanto como el dispositivo.

## Imagen, color y visión

Una imagen digital muestrea el espacio en píxeles y cuantiza brillo o color. RGB describe emisión de luz en pantallas; otros espacios separan luminancia y crominancia porque el ojo tolera menor detalle de color. Resolución espacial, profundidad de bits y rango dinámico describen dimensiones diferentes de calidad.

Más píxeles no garantizan mejor imagen: óptica, sensor, ruido, compresión, pantalla y distancia de observación limitan el resultado.

## Del fotograma al vídeo

El vídeo añade muestreo temporal. La frecuencia de fotogramas afecta fluidez y desenfoque de movimiento; la resolución afecta detalle espacial. Subir ambas aumenta drásticamente la tasa de datos sin asegurar una mejora perceptible equivalente.

Audio y vídeo deben compartir una línea temporal. Buffers absorben variación de llegada, pero añaden latencia. En directo hay que equilibrar continuidad, sincronía y retraso.

## Compresión con y sin pérdidas

La compresión sin pérdidas elimina redundancia reversible. La compresión con pérdidas descarta información menos perceptible. JPEG trabaja sobre bloques y frecuencias espaciales; los códecs de vídeo además predicen entre fotogramas y transmiten diferencias.

Bitrate, resolución y calidad no son intercambiables de forma simple. Escenas con mucho movimiento o textura necesitan más datos que una diapositiva casi estática.

## Transporte y calidad de experiencia

Una descarga puede esperar a tener el archivo completo; una conversación en tiempo real no. Streaming adaptativo cambia de representación según capacidad estimada. En comunicación interactiva, retransmitir un paquete tarde puede ser peor que ocultar su pérdida.

La calidad de servicio mide red; la calidad de experiencia intenta describir lo que percibe la persona. Congelaciones, tiempo de arranque, sincronía y claridad pueden importar más que el bitrate medio.

## Profundización

### Muestreo espacial y aliasing

Patrones más finos que la rejilla del sensor producen moiré, el equivalente espacial del aliasing temporal. Óptica, filtro previo y densidad de píxeles deben diseñarse como una cadena.

### Eco y cancelación

En una llamada, el altavoz puede volver al micrófono. Un cancelador estima ese camino y resta una copia adaptada. Si la sala o la posición cambia, el filtro debe reaprender: procesado de señal aplicado a una experiencia cotidiana.

### Accesibilidad también es ingeniería

Subtítulos, audiodescripción, contraste y control de velocidad no son añadidos decorativos. Cambian representación e interfaz para que la información sobreviva a capacidades, contextos y dispositivos distintos.

## Ejercicio práctico

Toma un audio o vídeo corto y expórtalo con tres bitrates. Compara tamaño, artefactos y tiempo de transmisión estimado para una conexión lenta. Elige una versión para archivo, otra para streaming y otra para videollamada, justificando prioridades distintas.

## Autoevaluación

1. ¿Por qué amplitud física y volumen percibido no son equivalentes?
2. ¿Qué diferencias hay entre resolución, profundidad de bits y rango dinámico?
3. ¿Qué redundancias aprovecha un códec de vídeo?
4. ¿Por qué un buffer mejora continuidad pero empeora interactividad?
5. Da un caso en el que menos fidelidad produzca mejor experiencia.

---

**Navegación:** [⬅️ Bloque 21 — Automática y control](21-automatica-control.md) · [🏠 Índice](README.md) · [Índice ➡️](README.md)
