# Telecomunicaciones, de cero a una visión de sistema

Un curso abierto para entender **cómo viaja, se transforma y se protege la información**, desde una onda electromagnética hasta una aplicación distribuida.

No pretende sustituir un grado ni una colección de problemas matemáticos. Su objetivo es construir un mapa mental sólido: entender el porqué, relacionar asignaturas que suelen estudiarse por separado y comprobar cada idea con ejemplos o herramientas accesibles desde un portátil.

## Qué vas a conseguir

Al terminar deberías poder:

- narrar el viaje completo de un dato desde un sensor o móvil hasta un servicio en internet;
- explicar los compromisos entre ancho de banda, ruido, alcance, latencia, coste y fiabilidad;
- reconocer las piezas principales de un sistema de radio, fibra, red IP y plataforma cloud;
- usar herramientas básicas para observar señales, protocolos y rendimiento;
- elegir un área en la que profundizar con una base común bien conectada.

## Cómo seguir el curso

Cada bloque está diseñado como una unidad de 2 a 4 sesiones. No marques un concepto por haberlo leído: márcalo cuando puedas **explicarlo sin apuntes, poner un ejemplo y relacionarlo con otro bloque**.

Usa este ciclo:

1. **Orientación (10 min):** lee objetivos y preguntas de autoevaluación antes del contenido.
2. **Comprensión (30–45 min):** estudia la parte principal buscando la idea, no la frase exacta.
3. **Recuperación (10 min):** cierra el archivo y explica tres conceptos de memoria.
4. **Práctica (30–60 min):** realiza el ejercicio y conserva una evidencia breve.
5. **Repaso:** vuelve al bloque a los 2 días, 1 semana y 1 mes.

La hoja [PROGRESO.md](PROGRESO.md) reúne la checklist, los repasos y un pequeño diario de evidencias.

> **Primera vez:** abre el [orden de estudio paso a paso](ORDEN-DE-ESTUDIO.md). Al final de cada capítulo encontrarás enlaces al tema anterior, al índice y al siguiente.

## Rutas recomendadas

No hay un único orden correcto. Elige una ruta y usa los demás bloques como optativos.

### Ruta esencial — entender telecomunicaciones

`0 → 17 → 1 → 2 → 5 → 3 → 4 → 6 → 7 → 8 → 9 → 16`

Es la ruta más parecida al hilo conductor de una carrera, pero manteniendo el enfoque conceptual. Añade los bloques 18 y 19 cuando quieras comprender mejor el soporte físico.

### Ruta software, redes y cloud

`0 → 5 → 11 → 9 → 12 → 13 → 14 → 15 → 16`

Recomendada para perfiles de desarrollo. Vuelve a los bloques 1 y 2 cuando aparezcan conceptos como ancho de banda, codificación o ruido.

### Ruta radio, electrónica y señal

`0 → 17 → 18 → 19 → 1 → 2 → 3 → 4 → 8 → 6 → 10`

Da prioridad a la cadena física: del campo electromagnético a los bits.

### Ruta de grado ampliada

Completa las cuatro fases siguientes en orden. Incluye fundamentos y materias habituales del grado que no son exclusivamente de comunicaciones.

## Guion completo

### Fase 0 — Orientación y lenguaje común

- [ ] [0. Mapa mental](00-mapa-mental.md) — el viaje de un bit y los compromisos que se repiten.
- [ ] [17. Matemáticas para telecom](17-matematicas-para-telecom.md) — modelos, complejos, derivadas, probabilidad y Fourier.
- [ ] [20. Programación y computadores](20-programacion-computadores.md) — del algoritmo al hardware y al dato en red.

**Hito:** dibuja un sistema de comunicación extremo a extremo y señala dónde aparecen software, matemáticas, electrónica y red.

### Fase 1 — De la realidad física a la señal

- [ ] [18. Circuitos y electrónica](18-circuitos-electronica.md) — tensión, corriente, impedancia y componentes activos.
- [ ] [19. Electromagnetismo](19-electromagnetismo.md) — campos, ondas, potencia y líneas de transmisión.
- [ ] [1. Señales y sistemas](01-senales-y-sistemas.md) — tiempo, frecuencia, muestreo, ancho de banda y ruido.
- [ ] [8. Procesado digital de señal](08-procesado-senal.md) — filtros, FFT, correlación y compresión.
- [ ] [21. Automática y control](21-automatica-control.md) — realimentación, estabilidad y respuesta dinámica.

**Hito:** mide o genera una señal, obsérvala en tiempo y frecuencia y explica qué limita su medida.

### Fase 2 — Construir un enlace

- [ ] [2. Teoría de la comunicación](02-teoria-comunicacion.md) — modulación, capacidad, multiplexación y FEC.
- [ ] [3. Electrónica de comunicaciones](03-electronica-comunicaciones.md) — transceptor, mezclador, amplificación y sincronía.
- [ ] [4. Antenas y propagación](04-antenas-propagacion.md) — radiación, ganancia, multitrayecto y presupuesto de enlace.
- [ ] [7. Comunicaciones ópticas](07-comunicaciones-opticas.md) — fibra, WDM, amplificación y acceso.
- [ ] [22. Audio, imagen y multimedia](22-audio-imagen-multimedia.md) — percepción, digitalización, compresión y calidad.

**Hito:** compara un radioenlace, un enlace de fibra y un sistema audiovisual usando las mismas métricas básicas.

### Fase 3 — Construir y operar una red

- [ ] [5. Redes y telemática](05-redes-telematica.md) — capas, IP, transporte, DNS, HTTP y QoS.
- [ ] [6. Redes móviles](06-redes-moviles.md) — células, generaciones, handover y core.
- [ ] [9. Seguridad](09-seguridad.md) — cifrado, identidad, TLS, VPN y amenazas.
- [ ] [10. Tendencias](10-tendencias.md) — 5G/6G, IoT, SDN/NFV y satélites LEO.
- [ ] [16. Sistemas del mundo real](16-sistemas-mundo-real.md) — VoIP, GPS, radiodifusión y streaming.

**Hito:** explica una videollamada móvil desde el micrófono hasta el altavoz remoto, incluyendo seguridad y posibles fallos.

### Fase 4 — Conectar telecom con sistemas software

- [ ] [11. Telecom para fullstack](11-telecom-para-fullstack.md) — WebRTC, WebSockets, QUIC, CDN y diagnóstico.
- [ ] [12. Sistemas distribuidos](12-sistemas-distribuidos.md) — CAP, consenso, mensajería y consistencia.
- [ ] [13. Redes en contenedores y cloud](13-redes-contenedores-cloud.md) — Docker, Kubernetes, mesh y balanceo.
- [ ] [14. Observabilidad y rendimiento](14-observabilidad-rendimiento.md) — percentiles, métricas, logs y trazas.
- [ ] [15. Cloud computing](15-cloud-fundamentos.md) — modelos de servicio, VPC, escalado, IAM y coste.

**Hito final:** documenta el viaje de una petición en una aplicación real, mide su latencia y propone una mejora justificando sus contrapartidas.

## Criterio de finalización

Un bloque está terminado cuando cumples al menos cuatro de estos cinco puntos:

- [ ] Puedo resumirlo en menos de cinco minutos sin consultar el texto.
- [ ] Puedo dibujar sus componentes y el flujo principal.
- [ ] Puedo dar un ejemplo real y un contraejemplo.
- [ ] He completado el ejercicio y guardado una evidencia.
- [ ] He respondido correctamente la autoevaluación una semana después.

Si fallas el último punto, el bloque no está “suspendido”: vuelve a comprensión o práctica y programa un nuevo repaso.

## Proyecto integrador

Elige un sistema cotidiano —una videollamada, un GPS, una red doméstica, un sensor IoT o una plataforma de streaming— y crea una ficha que incluya:

1. diagrama de extremo a extremo;
2. representación física y digital de la información;
3. protocolos y equipos implicados;
4. tres límites o fuentes de fallo;
5. una medida observable y una mejora con sus contrapartidas.

Haz una primera versión tras la fase 0 y actualízala después de cada fase. El cambio entre versiones será la mejor evidencia de aprendizaje del curso.

## Alcance y contribuciones

Los bloques nuevos deben conservar la misma tónica: intuición antes que formalismo, conexiones con sistemas reales, un ejercicio realizable y preguntas que obliguen a explicar. Las fórmulas son bienvenidas cuando aclaran una relación; no cuando sustituyen la comprensión.

Este material es una guía de aprendizaje y no un plan docente oficial. Cada universidad distribuye y nombra las asignaturas de forma distinta.
