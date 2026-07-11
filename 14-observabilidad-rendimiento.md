# Bloque 14 — Observabilidad y rendimiento

[← 21. Tema anterior](13-redes-contenedores-cloud.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [Mi progreso](PROGRESO.md) · [23. Tema siguiente →](15-cloud-fundamentos.md)

> **Objetivo del bloque:** formular hipótesis y localizar problemas usando métricas, logs, trazas, percentiles y objetivos de servicio.

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

---

## Profundización

### SLI, SLO y presupuesto de error: convertir "fiabilidad" en un número operativo

El vocabulario que ordena todo lo anterior, popularizado por el SRE de Google:

- **SLI** (*indicator*): la métrica que mide lo que le importa al usuario — "porcentaje de peticiones respondidas bien en menos de 300 ms".
- **SLO** (*objective*): el objetivo interno sobre ese SLI — "99,9% en ventana de 30 días". (Un **SLA** es lo mismo pero prometido por contrato, con penalizaciones.)
- **Presupuesto de error**: la resta genial — si prometes 99,9%, tienes **derecho a fallar el 0,1%**: unos 43 minutos al mes. Ese margen es un presupuesto que se *gasta*: mientras quede, se despliega con agilidad y se asumen riesgos; si se agota, se congelan lanzamientos y se invierte en estabilidad.

Lo transformador no es la métrica sino el efecto organizativo: convierte la eterna pelea "queremos lanzar rápido" (desarrollo) contra "queremos que nada se rompa" (operaciones) en una decisión objetiva con un número compartido. Y obliga a una conversación sanísima: ¿cuánta fiabilidad necesita *de verdad* este servicio? Porque cada nueve extra multiplica el coste — 99,99% son solo 4 minutos de fallo al mes, un listón que exige redundancia multi-AZ (bloque 15) y guardias — y quizá tu panel interno de administración no lo necesita.

### Alertar sobre síntomas, no sobre causas

El error clásico al configurar alertas: alertar de causas posibles ("CPU al 90%", "cola creciendo", "disco al 80%") en vez de síntomas reales ("el SLO de latencia se está incumpliendo", "la tasa de errores supera el presupuesto"). El problema es doble: las causas sin síntoma generan **falsas alarmas** (la CPU al 90% puede ser un batch nocturno funcionando exactamente como debe), y los síntomas sin causa prevista se te escapan (el servicio puede estar caído con la CPU al 5%). La regla: **se despierta a un humano solo por síntomas que afectan a usuarios; las causas se registran como contexto para diagnosticar**, no como despertadores. La consecuencia de ignorarla tiene nombre — *alert fatigue*: un equipo entrenado a ignorar alertas porque casi nunca significan nada acaba ignorando la que sí.

### Cardinalidad: por qué tu sistema de métricas explota

La trampa técnica número uno al instrumentar. Las métricas se almacenan como series temporales, **una por cada combinación de etiquetas**: si a `http_requests` le pones etiquetas `endpoint` (50 valores) × `status` (10) × `región` (5), son 2.500 series — bien. Si a alguien se le ocurre añadir `user_id` (1 millón de valores), son **2.500 millones de series**: la factura de Datadog explota o Prometheus se arrodilla, y es un incidente sorprendentemente común en el mundo real.

La regla: etiquetas de métricas solo con valores **acotados y de baja cardinalidad** (endpoint, código de estado, región — no user ID, no session ID, no URL completa con parámetros). Para preguntas sobre usuarios individuales están los logs y los traces, que son eventos discretos y no sufren este problema. Es, en el fondo, la distinción métricas/logs/traces de la sección anterior convertida en regla de ingeniería con consecuencias económicas directas.

### RUM vs sintético: medir usuarios reales o robots

Dos formas complementarias de medir la experiencia: el monitoreo **sintético** (robots que ejecutan flujos clave cada pocos minutos desde varias regiones) da una línea base controlada y detecta caídas aunque no haya tráfico — a las 4 de la mañana o en el país donde aún no tienes usuarios. El **RUM** (*Real User Monitoring*: telemetría desde el navegador de cada usuario real) mide la verdad completa — incluyendo el móvil de gama baja con 3G que tu sintético, corriendo en un centro de datos con fibra, jamás verá. La conexión con el bloque 5 es directa: entre tu servidor y el usuario real hay toda una red de acceso (DSL, 4G congestionado, WiFi con paredes — bloque 4) que solo el RUM captura. Sintético para saber *si funciona*; RUM para saber *cómo se vive*. Con uno solo, tienes un punto ciego u otro.

## Ejercicio práctico

Mide percentiles de verdad contra un endpoint tuyo (o cualquier API pública), sin instalar nada:

```bash
for i in $(seq 1 50); do
  curl -o /dev/null -s -w "%{time_total}\n" https://api.github.com/zen
done | sort -n | awk '{a[NR]=$1} END {print "p50:", a[int(NR*0.5)], "\np95:", a[int(NR*0.95)], "\nmax:", a[NR]}'
```

Observa la diferencia entre p50 y máximo — frecuentemente un orden de magnitud, y acabas de comprobar con datos propios por qué la media miente. Repite desde otra red (móvil compartiendo datos) y compara: eso que acaba de cambiar es exactamente lo que el RUM mide y el sintético no. Si quieres subir un nivel: `k6` o `vegeta` hacen esto mismo con carga concurrente y percentiles de serie.

## Autoevaluación

1. Define SLI, SLO y presupuesto de error con un ejemplo inventado, y explica qué decisión organizativa desbloquea el presupuesto.
2. ¿Por qué "CPU al 90%" es una mala alerta y "p99 sobre el SLO durante 5 minutos" una buena? ¿Qué es la alert fatigue?
3. Un compañero añade `user_id` como etiqueta a una métrica. Explícale qué va a pasar y adónde debería ir esa información.
4. Tu sintético está verde y los usuarios de móvil se quejan. ¿Qué está pasando y qué te falta por medir?

---

## Continúa el recorrido

Antes de avanzar, intenta responder la autoevaluación sin mirar y registra la práctica en [PROGRESO.md](PROGRESO.md). Si puedes explicar la idea central, dibujarla y conectarla con un bloque anterior, continúa.

[← 21. Tema anterior](13-redes-contenedores-cloud.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [23. Tema siguiente →](15-cloud-fundamentos.md)
