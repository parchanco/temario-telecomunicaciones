# Bloque 0 — Mapa mental antes de empezar

[← README](README.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [Mi progreso](PROGRESO.md) · [2. Tema siguiente →](17-matematicas-para-telecom.md)

> **Objetivo del bloque:** construir una visión de conjunto del viaje de la información y reconocer los compromisos básicos que reaparecerán durante todo el curso.

## Qué hace un ingeniero de telecomunicación en la práctica

El título es muy amplio a propósito, y por eso en el día a día la gente se especializa en direcciones muy distintas. Conviene tener claro el mapa de salidas antes de estudiar, porque te ayuda a decidir dónde profundizar más:

- **RF / Radiofrecuencia**: diseño de antenas, transceptores, radioenlaces. Trabajas cerca de la física de la propagación y la electrónica de alta frecuencia. Empresas: fabricantes de equipos (Ericsson, Nokia, Huawei), operadoras, defensa/aeroespacial.
- **Redes y telemática**: diseño y operación de redes IP, centros de datos, ISPs, redes móviles core. Aquí el conocimiento se parece mucho al de un ingeniero informático especializado en infraestructura.
- **Sistemas / I+D**: diseño de sistemas de comunicación completos (satélites, sistemas de radar, comunicaciones militares), investigación en universidades o centros tecnológicos.
- **No-telco**: muchísimos ingenieros de telecomunicación acaban en roles que no tienen "telecomunicación" en el nombre: desarrollo de software, ciberseguridad, ciencia de datos, IoT industrial. La base de señales/redes/electrónica sirve de fondo común.

No hace falta ser experto en todo. Este temario te da la base común; luego cada bloque es la puerta de entrada a una especialización si te interesa.

## El viaje de un bit: de tu móvil a un servidor y vuelta

Antes de entrar en teoría, conviene tener una imagen mental de conjunto. Cuando abres una app y pides un dato, esto es (a grandes rasgos) lo que pasa:

1. **Tu aplicación** genera datos (una petición HTTP, por ejemplo).
2. Esos datos bajan por las **capas de red** de tu teléfono: se les añade información de transporte (TCP/UDP), de red (IP) y de enlace, como si metieras una carta dentro de varios sobres, uno dentro de otro.
3. El teléfono convierte esos bits en una **señal de radio**: los datos digitales modulan una onda electromagnética (bloque 2) que sale por la antena.
4. Esa onda viaja por el aire hasta la **estación base** más cercana (bloque 6), sufriendo atenuación, reflexiones y ruido por el camino (bloque 4).
5. La estación base demodula la señal, la convierte de nuevo en bits, y la mete en la **red core** del operador, normalmente por fibra óptica (bloque 7).
6. Esa red enruta el paquete a través de **internet** (bloque 5) — routers que consultan tablas y deciden el siguiente salto — hasta llegar al servidor.
7. El servidor responde, y el mismo proceso ocurre en sentido inverso: sobres que se van abriendo capa a capa hasta llegar a tu app.

Todo el temario que sigue es, en el fondo, una forma de rellenar de detalle cada uno de estos pasos. Cuando te atasques en un bloque muy técnico, vuelve a esta imagen: te recuerda **para qué sirve** lo que estás estudiando.

## Por qué existen tantas "capas"

Un patrón que se repite constantemente en telecomunicaciones es dividir un problema complejo en capas independientes (bloque 5 lo formaliza con el modelo OSI). La idea de fondo, que conviene interiorizar ya:

> Cada capa resuelve un problema concreto y le oculta esa complejidad a la capa de arriba.

Ejemplo: a la aplicación que pides un vídeo no le importa si el bit físico viajó por WiFi, fibra o 5G — eso lo resuelven capas inferiores. Este patrón de "abstracción por capas" aparece una y otra vez: en redes, en sistemas de comunicación, en el propio software. Reconocerlo te ahorra mucha confusión más adelante.

---

## Profundización

### Las cinco disyuntivas que se repiten en todo el temario

Casi todos los conceptos que vas a ver son variaciones de un puñado de compromisos fundamentales. Tenerlos identificados desde el principio convierte el temario en algo mucho más pequeño de lo que parece:

1. **Velocidad vs robustez**: más bits por símbolo = más rápido pero más frágil ante ruido (modulación, bloque 2). Aparece también en TCP vs UDP, en códecs, en FEC.
2. **Alcance vs capacidad**: frecuencias bajas llegan lejos pero llevan poco; frecuencias altas llevan mucho pero llegan cerca (bloque 4). Aparece en 5G, en WiFi de 2.4 vs 5 GHz, en LoRa vs NB-IoT.
3. **Dedicado vs compartido**: reservar recursos garantiza calidad pero desperdicia capacidad; compartir dinámicamente aprovecha mejor pero sin garantías (circuitos vs paquetes, bloque 5). Aparece en QoS, en cloud, en autoescalado.
4. **Control vs comodidad**: cada capa de abstracción te quita trabajo y te quita opciones a la vez (IaaS vs PaaS, serverless vs contenedores, bloque 15).
5. **Consistencia vs disponibilidad**: cuando algo está replicado y la red falla, eliges entre respuestas correctas o respuestas rápidas (CAP, bloque 12).

Cuando un concepto nuevo te parezca arbitrario, pregúntate cuál de estas cinco tensiones está resolviendo — casi siempre es una de ellas.

### Cómo estudiar este temario (método, no solo contenido)

- **Técnica Feynman**: el criterio de las checklists ("explicarlo sin apuntes") es exactamente esto — intenta explicar el concepto en voz alta o por escrito como si se lo contaras a alguien sin formación técnica. Donde te trabes, ahí está el hueco real.
- **Espaciado**: es mucho más eficaz tocar un bloque 3 veces en 3 semanas que estudiarlo 3 horas seguidas una vez. Las checklists sirven también para esto: repasa los ítems marcados de bloques anteriores antes de empezar uno nuevo.
- **Ancla cada concepto a algo que ya uses**: como desarrollador, casi todo tiene un equivalente en tu día a día (los bloques 11-15 existen para eso). Un concepto anclado a experiencia propia no se olvida.

## Ejercicio práctico

Sin herramientas: cuéntale a alguien (o escríbelo en una nota) el viaje completo de un bit desde tu móvil hasta un servidor, con el nivel de detalle que tengas ahora. Guarda esa nota. Cuando termines el temario, repite el ejercicio sin mirar la primera versión y compáralas — es la mejor medida de progreso que existe.

## Autoevaluación

1. ¿Por qué dividir un sistema en capas facilita que cada una evolucione por separado? Da un ejemplo real (pista: tu app web no cambió cuando llegó el 5G).
2. De las cinco disyuntivas, ¿cuál reconoces ya en alguna decisión técnica que hayas tomado como desarrollador?
3. ¿En qué paso del "viaje del bit" dirías ahora mismo que tienes la imagen más borrosa? (Ese es tu próximo bloque.)

---

## Continúa el recorrido

Antes de avanzar, intenta responder la autoevaluación sin mirar y registra la práctica en [PROGRESO.md](PROGRESO.md). Si puedes explicar la idea central, dibujarla y conectarla con un bloque anterior, continúa.

[← README](README.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [2. Tema siguiente →](17-matematicas-para-telecom.md)
