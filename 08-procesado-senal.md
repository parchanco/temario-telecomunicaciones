# Bloque 8 — Procesado digital de señal (a nivel de idea)

## Por qué casi todo se hizo digital

En el bloque 1 ya se vio la razón principal (regeneración sin pérdida de calidad, facilidad de procesado). En este bloque se trata específicamente el **procesado**: una vez que una señal está en forma de números, se puede manipular con total precisión y repetibilidad usando algoritmos — filtrarla, comprimirla, analizarla, combinarla con otras — sin que el propio proceso de manipulación introduzca degradación (a diferencia de un circuito analógico, donde cada componente añade su propio ruido e imprecisión, y donde replicar exactamente el mismo comportamiento en otro dispositivo es prácticamente imposible por las tolerancias de fabricación).

La contrapartida: procesar digitalmente requiere haber convertido antes la señal (ADC, bloque 1) y, para procesar en tiempo real, necesitas suficiente capacidad de cómputo — algo que hoy es barato y abundante, pero que hace unas décadas era la razón por la que muchos sistemas seguían siendo analógicos.

## Qué hace un filtro (sin diseñarlo)

Un **filtro** es un sistema que deja pasar ciertas frecuencias de una señal y atenúa o bloquea otras. Pensando en el dominio de la frecuencia (bloque 1), cualquier señal es una mezcla de muchas frecuencias distintas; un filtro decide, selectivamente, con cuáles te quedas.

Tipos básicos, por su función (no por su diseño interno):

- **Paso bajo**: deja pasar las frecuencias por debajo de un umbral, bloquea las altas. Útil, por ejemplo, para eliminar ruido de alta frecuencia de una señal de audio.
- **Paso alto**: al revés, deja pasar las frecuencias altas y bloquea las bajas. Útil para eliminar zumbidos graves no deseados.
- **Paso banda**: deja pasar solo una franja intermedia de frecuencias, bloqueando tanto las muy bajas como las muy altas — es lo que hace, por ejemplo, el filtro de frecuencia intermedia de un receptor de radio (bloque 3), quedándose solo con el canal que interesa y descartando el resto del espectro captado por la antena.

La intuición útil, sin entrar en el diseño matemático del filtro: un filtro es, conceptualmente, una especie de "criba" que decide qué componentes de frecuencia sobreviven a la salida y cuáles no. Diseñar un buen filtro consiste en un compromiso entre lo abrupto que sea ese corte y otros efectos secundarios (retardo introducido, complejidad de cómputo) — pero esos detalles pertenecen a una asignatura más matemática de procesado de señal, no a este resumen conceptual.

## Compresión: por qué el audio y el vídeo se pueden reducir tanto

Un archivo de audio o vídeo sin comprimir ocupa muchísimo espacio. Sin embargo, formatos como MP3, JPEG o los códecs de vídeo modernos (H.264, H.265, AV1) consiguen reducir ese tamaño drásticamente, muchas veces a una fracción muy pequeña del original, sin que la calidad percibida baje demasiado. Esto es posible por dos ideas complementarias:

- **Redundancia en los datos**: la información "real" (útil) que contiene una señal suele ser mucho menor que la cantidad de datos en bruto que la representan. Un vídeo, por ejemplo, tiene fotogramas consecutivos que se parecen muchísimo entre sí (el fondo no cambia, solo se mueve un objeto pequeño) — en vez de guardar cada fotograma entero, basta con guardar el primero y luego solo "qué ha cambiado" respecto al anterior. De forma parecida, una imagen con un cielo azul uniforme tiene miles de píxeles casi idénticos — no hace falta almacenar cada uno por separado con total precisión.
- **Percepción humana (compresión con pérdida)**: el oído y el ojo humanos no perciben toda la información con la misma sensibilidad. El oído, por ejemplo, apenas percibe ciertas frecuencias cuando están "enmascaradas" por otras más fuertes ocurriendo al mismo tiempo (enmascaramiento auditivo); el ojo es mucho menos sensible a pequeños cambios de color que a cambios de brillo. Los códecs de compresión con pérdida (MP3, JPEG, vídeo) aprovechan esto deliberadamente: descartan o simplifican precisamente la información que el ser humano difícilmente notaría que falta, y así consiguen reducciones de tamaño mucho mayores que si solo eliminaran redundancia "objetiva".

La combinación de ambas ideas —quitar lo que es matemáticamente redundante, y quitar además lo que el ser humano apenas percibiría— es lo que explica por qué una canción de varios minutos cabe en unos pocos megabytes, o por qué se puede hacer streaming de vídeo en alta definición con un ancho de banda (bloque 1) que, en crudo, sería completamente insuficiente para esa misma calidad de imagen.

---

## Profundización

### La FFT: el algoritmo más importante del que nadie habla

La **FFT (Fast Fourier Transform)** es el algoritmo que calcula la descomposición en frecuencias del bloque 1 de forma eficiente — y esa palabra, "eficiente", cambió el mundo. Calcular la transformada de Fourier "a lo bruto" de N muestras cuesta del orden de N² operaciones; la FFT lo hace en N·log(N) — para un segundo de audio, la diferencia entre miles de millones de operaciones y unos pocos millones. Como desarrollador reconocerás el patrón: es divide y vencerás, la misma familia que mergesort.

Sin esa mejora, nada de lo que hace tu móvil en tiempo real sería viable: cada símbolo OFDM del WiFi y el 5G (bloque 2) se genera con una FFT inversa y se decodifica con una FFT; el espectrograma del bloque 1 es una FFT por cada rebanada de tiempo; el MP3 decide qué frecuencias descartar mirando FFTs. Se ha dicho, con poca exageración, que es el algoritmo numérico más ejecutado del planeta — probablemente se están calculando varios miles de FFTs en tu bolsillo mientras lees esto.

### Correlación: cómo se encuentra una señal escondida

La segunda herramienta universal del DSP: la **correlación** mide cuánto se parece una señal recibida a un patrón que buscas, probando todos los desplazamientos posibles — conceptualmente, deslizar una plantilla sobre la señal y anotar cuánto encaja en cada posición.

Su superpoder: funciona incluso cuando la señal está **muy por debajo del ruido**. Si el patrón es largo y bien elegido, el ruido (aleatorio) se cancela a sí mismo en la suma mientras que la coincidencia real se acumula — la señal "emerge" del ruido. Esto resuelve el misterio pendiente del GPS (bloque 16): el receptor conoce el código exacto que emite cada satélite y correla la señal recibida contra él; aunque la señal llegue enterrada bajo el ruido térmico, la correlación la encuentra, y el desplazamiento donde encaja es precisamente el retardo del que se deduce la distancia. Es también como el receptor WiFi encuentra el inicio exacto de cada trama, y como Shazam encuentra tu canción en su base de datos.

### El precio oculto: la latencia de procesado

Un matiz que conecta con los bloques 5 y 14: procesar digitalmente no es gratis en **tiempo**. Muchos algoritmos necesitan acumular un bloque de muestras antes de poder operar (una FFT necesita sus N muestras completas; un códec necesita su trama entera), y eso añade retardo estructural: el famoso desfase de los auriculares Bluetooth con el vídeo, o los ~20-40 ms que un códec de voz añade a una llamada, son mayormente esto — buffers y bloques, no distancia. Es otra cara de la disyuntiva del jitter buffer (bloque 16): calidad y eficiencia de procesado, a cambio de latencia. En sistemas de audio profesional y en el 5G de baja latencia, gran parte de la ingeniería consiste en encoger estos bloques sin perder las ventajas de procesarlos.

## Ejercicio práctico

Como desarrollador, este es tu bloque más accesible — la FFT en ~10 líneas de Python:

```python
import numpy as np

fs = 1000                          # 1000 muestras/segundo
t = np.arange(0, 1, 1/fs)          # 1 segundo de tiempo
señal = (np.sin(2*np.pi*50*t)      # un tono de 50 Hz
       + 0.5*np.sin(2*np.pi*120*t) # otro de 120 Hz, más débil
       + 2.0*np.random.randn(len(t)))  # ruido MÁS FUERTE que ambos

espectro = np.abs(np.fft.rfft(señal))
freqs = np.fft.rfftfreq(len(señal), 1/fs)
print(freqs[espectro.argsort()[-2:]])  # las 2 frecuencias dominantes
```

En el dominio del tiempo, `señal` es ruido inservible a simple vista (imprímela o grafícala con matplotlib). La FFT recupera los tonos de 50 y 120 Hz limpiamente. Acabas de ver el SNR del bloque 1, el cambio de dominio, y la razón de existir del DSP, en un script. Experimenta: sube el ruido a 5.0 y observa cuándo se "pierden" los tonos.

## Autoevaluación

1. ¿Por qué la eficiencia de la FFT (N·log N vs N²) es la diferencia entre "posible" y "imposible" para el tiempo real? Da un ejemplo con números redondos.
2. Explica cómo la correlación encuentra una señal que está por debajo del ruido. ¿Qué papel juega la longitud del patrón?
3. El audio de tus auriculares Bluetooth llega tarde respecto al vídeo. ¿Qué parte de ese retardo es distancia y qué parte es DSP?
4. Un archivo JPEG de un cielo despejado ocupa mucho menos que uno de un bosque detallado, con la misma resolución. Explica por qué con los dos principios de la compresión.
