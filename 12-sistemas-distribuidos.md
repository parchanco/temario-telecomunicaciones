# Bloque 12 — Sistemas distribuidos

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
