# Bloque 0 — Mapa mental antes de empezar

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
