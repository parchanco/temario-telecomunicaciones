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
