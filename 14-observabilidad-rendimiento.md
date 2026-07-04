# Bloque 14 — Observabilidad y rendimiento

> El bloque 5 introdujo latencia, throughput y jitter como conceptos de red. Aquí se retoman aplicados a "tu API en producción" — y se añade la maquinaria para poder verlos y entenderlos cuando algo va mal.

## Percentiles de latencia: por qué la media miente

Cuando alguien dice "la latencia media de mi API es 80 ms", esa cifra esconde muchísimo. Si 99 de cada 100 peticiones tardan 50 ms pero una tarda 3000 ms, la media sale relativamente baja y "parece" que todo va bien — pero ese 1% de usuarios está teniendo una experiencia muy mala, y ese 1% no es aleatorio: suele repetirse en los mismos usuarios (los que tienen peor conexión, los que piden los recursos más pesados), convirtiéndose en un problema real y persistente para alguien.

Por eso en observabilidad casi nunca se habla de medias, sino de **percentiles**:

- **p50 (mediana)**: la mitad de las peticiones son más rápidas que este valor, la mitad más lentas — da una idea de la experiencia "típica".
- **p95 / p99**: el 95% (o 99%) de las peticiones son más rápidas que este valor. Estos son los números que de verdad importan para detectar problemas, porque capturan la "cola" de peticiones lentas que la media disuelve y esconde.

La intuición clave: **optimizar la media y optimizar el p99 son problemas distintos**, y a veces hasta contrapuestos (una estrategia de caché puede bajar la media espectacularmente mientras deja intacto, o incluso empeora, el peor caso para quien no tiene esa caché disponible). Cualquier objetivo serio de rendimiento (un SLO, *Service Level Objective*) se define casi siempre en términos de percentiles, no de medias.

## Tracing distribuido: seguir una petición a través de muchos servicios

En una arquitectura de microservicios (bloque 12), una sola petición de usuario puede atravesar seis, diez o veinte servicios distintos antes de completarse. Cuando esa petición tarda demasiado o falla, ¿cómo sabes **cuál** de esos servicios fue el responsable?

El **tracing distribuido** (herramientas como Jaeger, Zipkin, o las capacidades nativas de OpenTelemetry) resuelve esto propagando un **identificador único de traza** a través de toda la cadena de llamadas — cada servicio, al recibir la petición, añade su propio "tramo" (span) a esa traza, con su tiempo de inicio y fin, antes de pasarla al siguiente servicio. Al final, puedes reconstruir un diagrama tipo cascada (*waterfall*) que muestra exactamente cuánto tiempo pasó en cada servicio y en qué orden — mostrando de un vistazo, por ejemplo, que el 90% del tiempo total se fue en una única llamada a una base de datos en el sexto servicio de la cadena, no en los otros diecinueve.

Esta idea de "propagar un identificador para poder reconstruir el camino completo" no es nueva en este temario: es conceptualmente similar a cómo un paquete de red (bloque 5) podría, en teoría, llevar registrado cada salto de router que ha atravesado (de hecho, herramientas como `traceroute`, mencionada en el bloque 11, hacen exactamente eso a nivel de red) — el tracing distribuido aplica la misma lógica una capa más arriba, a nivel de llamadas entre servicios de aplicación en vez de saltos de router.

## Métricas, logs y traces: las tres patas de la observabilidad

Conviene distinguir para qué sirve cada una, porque se usan para preguntas distintas:

- **Métricas**: números agregados a lo largo del tiempo (peticiones por segundo, percentil de latencia, uso de CPU). Responden a "¿cuánto?" y "¿cuándo empezó a ir mal?" — son baratas de almacenar y perfectas para paneles y alertas, pero no te dicen *por qué* algo falló en un caso concreto.
- **Logs**: registros de eventos discretos, normalmente con texto descriptivo ("usuario X inició sesión", "error al conectar con la base de datos"). Responden a "¿qué pasó exactamente, con detalle?" — pero se vuelven difíciles de correlacionar entre servicios sin un identificador de traza compartido.
- **Traces**: como se explicó arriba, responden a "¿por dónde pasó esta petición concreta, y dónde se fue el tiempo?".

La combinación habitual en la práctica: una métrica de alta latencia dispara una alerta ("¿cuánto está fallando?"), un trace concreto de una petición lenta te dice en qué servicio se atascó ("¿dónde?"), y los logs de ese servicio en ese momento te dan el detalle final ("¿por qué exactamente?"). Ninguna de las tres sustituye a las otras dos.

## Profiling de red: cuando el cuello de botella no es tu código

Antes de asumir que un problema de rendimiento es culpa de tu lógica de aplicación, merece la pena descartar la red como causa — reconectando con el bloque 5 y el bloque 11:

- ¿Es un problema de **latencia de ida y vuelta** (RTT) por la distancia física al servidor (bloque 4), no de procesamiento? Un `mtr`/`traceroute` (bloque 11) lo revela.
- ¿Está el cuello de botella en el **handshake TLS** o en la resolución DNS repetida en cada petición, en vez de en la petición en sí? Herramientas como la pestaña Network del navegador, o `curl -v` con timing detallado, lo muestran desglosado por fases.
- ¿Se está reabriendo una conexión TCP/TLS nueva en cada petición en vez de reutilizar una conexión persistente (keep-alive, o HTTP/2 multiplexado, bloque 11)? Esto añade latencia fija en cada petición que es completamente evitable.

La actitud útil: cuando algo "va lento" en producción, la teoría de los bloques 1, 4, 5 y 11 (latencia física, multiplexación, TCP vs UDP, HTTP/2 vs HTTP/3) no es trivia — es literalmente la lista de sospechosos habituales antes de ponerte a optimizar código que quizá no era el problema.
