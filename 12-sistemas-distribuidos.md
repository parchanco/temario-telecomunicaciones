# Bloque 12 — Sistemas distribuidos

[← 19. Tema anterior](11-telecom-para-fullstack.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [Mi progreso](PROGRESO.md) · [21. Tema siguiente →](13-redes-contenedores-cloud.md)

> **Objetivo del bloque:** razonar sobre coordinación, consistencia, reintentos y fallos cuando el estado se reparte entre varias máquinas.

> Cuando una red de comunicaciones deja de ser solo "tuberías por las que viajan bits" y empieza a conectar máquinas que tienen que **ponerse de acuerdo** entre sí, aparecen problemas nuevos, muy relevantes en cualquier backend moderno.

## El teorema CAP

Cuando tienes datos replicados en varios nodos (varias réplicas de una base de datos, por ejemplo, en distintos centros de datos), el **teorema CAP** dice que, ante un fallo de red que separe esos nodos (una **partición**, la P de CAP), tienes que elegir entre:

- **Consistencia (C)**: todos los nodos ven siempre los mismos datos, aunque eso signifique rechazar peticiones si un nodo no puede confirmar que está sincronizado con los demás.
- **Disponibilidad (A, availability)**: el sistema sigue respondiendo a peticiones aunque algún nodo esté aislado, aunque eso signifique que distintos nodos puedan devolver, momentáneamente, respuestas distintas.

La idea clave, y la razón por la que esto es un teorema y no una simple recomendación de diseño: **cuando ocurre una partición de red, no puedes tener ambas cosas a la vez**. Si un nodo no puede hablar con el resto y aun así decide responder a una petición, no puede garantizar que su copia de los datos sea la más actualizada (sacrifica consistencia). Si en cambio decide no responder hasta confirmar que está sincronizado, sacrifica disponibilidad.

Esto conecta directamente con el bloque 5 (redes): las particiones de red no son un caso hipotético raro — son una consecuencia inevitable de que las redes reales fallan, tienen latencia variable y pueden perder paquetes. Bases de datos como PostgreSQL en modo de réplica síncrona tienden a priorizar consistencia; sistemas como DynamoDB o Cassandra, en su configuración por defecto, priorizan disponibilidad. Ninguna elección es "la correcta" en abstracto — depende de si tu aplicación tolera mejor un error temporal o un dato desactualizado.

## Consenso: cómo se ponen de acuerdo varias máquinas (Raft/Paxos, a nivel de idea)

Si tienes varias réplicas de un mismo dato y quieres que **todas acaben de acuerdo en un mismo valor**, aunque algunas fallen o la red se comporte de forma errática, necesitas un **algoritmo de consenso**. Paxos (el original, muy influyente pero notoriamente difícil de entender) y Raft (diseñado explícitamente para ser más comprensible) son los más conocidos.

La intuición, sin entrar en el protocolo exacto: en vez de que cualquier nodo pueda decidir un valor por su cuenta, se elige un **líder** (mediante un proceso de votación entre los nodos) que coordina las decisiones, y un cambio solo se considera "confirmado" cuando **una mayoría** de nodos lo ha reconocido — no todos, porque exigir unanimidad haría que el sistema entero se parara si un solo nodo fallara. Con mayoría basta porque, matemáticamente, dos mayorías distintas siempre se solapan en al menos un nodo, lo que evita que el sistema tome dos decisiones contradictorias en paralelo.

Esto no es una curiosidad académica: es lo que hace posible, por debajo, sistemas que usas o de los que dependes constantemente — desde el propio etcd que coordina un clúster de Kubernetes (bloque 13), hasta cómo una base de datos distribuida decide quién es el nodo "primario" en cada momento tras una caída.

## Colas de mensajes y arquitecturas de microservicios

En el bloque 11 ya se introdujeron Kafka/MQTT desde el ángulo de "multiplexar eventos". Aquí interesa la razón de diseño más de fondo: en una arquitectura de **microservicios**, cada servicio es responsable de una parte pequeña del sistema, y necesitan comunicarse entre sí sin que un fallo o una lentitud en uno tumbe a los demás — el mismo principio de "capas independientes" del bloque 0 y 5, aplicado ahora a servicios en vez de a protocolos de red.

Dos patrones de comunicación entre servicios, con la misma disyuntiva de siempre (TCP vs UDP del bloque 5, en otra forma):

- **Síncrono (petición-respuesta, ej. REST/gRPC directo)**: sencillo de razonar, pero acopla la disponibilidad de un servicio a la de sus dependencias — si el servicio B está caído, cualquier llamada del servicio A a B falla o se cuelga.
- **Asíncrono (a través de una cola/evento, ej. Kafka, RabbitMQ, SQS)**: el servicio A publica un evento y sigue su camino sin esperar; el servicio B lo procesará cuando pueda. Esto añade robustez frente a caídas temporales (los eventos quedan en la cola esperando), a costa de mayor complejidad para razonar sobre el estado del sistema en un instante dado (consistencia eventual, en vez de inmediata).

Esta tensión —acoplamiento y simplicidad vs. resiliencia y complejidad— es la misma disyuntiva de fondo que ya viste en modulación (velocidad vs robustez, bloque 2) y en redes de acceso (capacidad dedicada vs compartida, bloque 5): en sistemas distribuidos, casi todo diseño es, en el fondo, decidir dónde colocarte en un espectro de compromisos, no encontrar una solución sin contrapartidas.

## Consistencia eventual, en una frase

Cuando eliges disponibilidad sobre consistencia estricta (CAP), el sistema normalmente ofrece **consistencia eventual**: garantiza que, si dejas de escribir datos nuevos, todas las réplicas **acabarán** convergiendo al mismo valor — pero no te dice cuánto tiempo tardarán en hacerlo. Es la razón por la que, a veces, subes una foto de perfil y durante unos segundos algunos de tus amigos la ven y otros todavía ven la antigua: distintas réplicas del sistema aún no se han sincronizado entre sí.

---

## Profundización

### Idempotencia: la propiedad que hace seguros los reintentos

El problema más traicionero de los sistemas distribuidos cabe en una frase: **cuando una petición no recibe respuesta, es imposible saber si no llegó o si se ejecutó y se perdió la confirmación**. Si reintentas un "cobra 50€" que sí se había ejecutado, cobras 100€; si no reintentas y no se había ejecutado, no cobras nada. No hay forma de distinguir los casos desde fuera — es una limitación fundamental, no un bug.

La salida es hacer que reintentar sea inofensivo: una operación es **idempotente** si ejecutarla N veces equivale a ejecutarla una. Algunas lo son de nacimiento ("pon el estado en 'enviado'"); las que no ("suma 50€"), se convierten con una **clave de idempotencia**: el cliente genera un identificador único por operación y el servidor recuerda los ya procesados, respondiendo a los duplicados con el resultado original sin re-ejecutar. Es exactamente lo que hace el header `Idempotency-Key` de la API de Stripe, y el motivo por el que los mensajes de una cola (bloque 11) se entregan "al menos una vez" y tus consumidores deben tolerar duplicados: el mítico "exactly-once" a través de una red no fiable es, en general, este mismo problema disfrazado — lo que existe en la práctica es "al menos una vez + idempotencia".

La regla de diseño que se deriva, y que vale oro en cualquier backend: **todo endpoint que pueda ser reintentado (o sea, todos) debería ser idempotente o rechazar duplicados explícitamente.**

### El tiempo es una ilusión (distribuida)

Segunda trampa fundamental: **no existe "la misma hora" en dos máquinas**. Cada reloj físico deriva, NTP los sincroniza solo hasta cierto punto (milisegundos en el mejor caso, y con saltos), así que ordenar eventos de máquinas distintas por su timestamp es una lotería — dos escrituras separadas por microsegundos reales pueden llevar timestamps en orden inverso.

Las dos salidas conceptuales: (1) **relojes lógicos** (Lamport): renunciar al tiempo físico y usar contadores que solo garantizan respetar la causalidad — "si A causó B, A tiene número menor" — suficiente para muchísimos casos; (2) la fuerza bruta elegante de Google Spanner: relojes atómicos y GPS (bloque 16 — otra vez GPS como servicio de tiempo) en cada centro de datos, más una API que devuelve el tiempo **como intervalo de incertidumbre** y espera a que el intervalo pase antes de confirmar. La lección transferible aunque nunca toques Spanner: cuando dos servicios discuten sobre "qué pasó primero", el timestamp no es un árbitro fiable; necesitas causalidad explícita (versiones, secuencias) o un único punto de ordenación.

### Backpressure: qué pasa cuando el consumidor no da abasto

Toda cadena productor → cola → consumidor tiene una pregunta incómoda: ¿qué pasa si el productor genera más rápido de lo que el consumidor procesa, de forma sostenida? La cola amortigua ráfagas, pero contra un desequilibrio sostenido solo pospone el colapso (memoria/disco infinitos no existen). Las únicas tres respuestas posibles: **frenar al productor** (backpressure real — que la presión "suba" hacia atrás hasta quien genera), **descartar** con criterio (¿los mensajes más viejos? ¿los menos importantes? — la voz sobre UDP del bloque 16 eligió esto), o **escalar consumidores** (bloque 13, si el cuello de botella lo permite). Un sistema que no ha elegido conscientemente una de las tres la tiene elegida por defecto: caerse. Es la versión software de la congestión de redes del bloque 5 — TCP lleva 40 años haciendo backpressure (control de flujo y congestión) y por eso internet no colapsa cada tarde.

## Ejercicio práctico

Diseña sobre papel (15 minutos, sin código) el endpoint `POST /transferencias` de un banco ficticio, respondiendo explícitamente:

1. El cliente hace la petición y se le corta el WiFi antes de recibir respuesta. ¿Qué debe hacer su app: reintentar, preguntar, rendirse? ¿Qué necesita del servidor para que reintentar sea seguro?
2. ¿Dónde guardas las claves de idempotencia ya vistas y cuánto tiempo? ¿Qué pasa si esa tabla está en una réplica con consistencia eventual? (Aquí se te juntarán las dos secciones — es a propósito.)
3. Dos transferencias simultáneas desde la misma cuenta con saldo para una sola, procesadas por dos instancias distintas del servicio. ¿Qué mecanismo decide cuál gana?

Después compara tus decisiones con la documentación de idempotencia de Stripe (es corta y excelente) — verás que un equipo de primer nivel llegó a las mismas preguntas.

## Autoevaluación

1. ¿Por qué es *imposible* (no difícil) saber si una petición sin respuesta se ejecutó? ¿Cuál es la única salida práctica?
2. "Exactly-once delivery" — ¿por qué los sistemas serios prometen en cambio "al menos una vez" y qué te exige eso como consumidor?
3. Dos servidores registran eventos con timestamps de sus relojes. ¿Por qué ordenarlos por timestamp puede violar la causalidad, y qué alternativas hay?
4. Tu cola de trabajos crece sin parar desde hace una hora. Enumera las tres únicas familias de solución y qué pregunta de negocio decide entre ellas.

---

## Continúa el recorrido

Antes de avanzar, intenta responder la autoevaluación sin mirar y registra la práctica en [PROGRESO.md](PROGRESO.md). Si puedes explicar la idea central, dibujarla y conectarla con un bloque anterior, continúa.

[← 19. Tema anterior](11-telecom-para-fullstack.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [21. Tema siguiente →](13-redes-contenedores-cloud.md)
