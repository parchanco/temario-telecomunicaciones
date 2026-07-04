# Temario de estudio — Ingeniería de Sistemas de Telecomunicación

> Enfoque: entender conceptos y el "por qué" de las cosas, no resolver problemas matemáticos exhaustivos. Cada bloque tiene un archivo desarrollado con explicaciones e intuición, más una checklist para marcar progreso.

Cómo usar esto: marca cada punto con `- [x]` cuando sientas que puedes **explicárselo a otra persona sin mirar apuntes**. Ese es el criterio de "terminado", no haber leído el capítulo.

---

## [Bloque 0 — Mapa mental antes de empezar](00-mapa-mental.md)

- [ ] Qué hace un ingeniero de telecomunicación en la práctica (roles: RF, redes, sistemas, I+D, telco vs. no-telco)
- [ ] Panorama general: cómo un bit de información viaja desde tu móvil hasta un servidor y vuelve (capas físicas, redes, protocolos) — esto te da el "esqueleto" en el que encajará todo lo demás

**Recurso:** busca en YouTube "how does the internet work" (charlas tipo Vsauce/Branch Education) antes de meterte en teoría — da contexto visual.

---

## [Bloque 1 — Señales y sistemas (lo mínimo conceptual)](01-senales-y-sistemas.md)

- [ ] Qué es una señal analógica vs digital, y por qué digitalizamos (ruido, regeneración, procesado)
- [ ] Dominio del tiempo vs dominio de la frecuencia — la idea de Fourier sin la matemática: "toda señal es una suma de senos"
- [ ] Ancho de banda: qué es y por qué limita cuánta información puedes mandar
- [ ] Muestreo y Nyquist: por qué hay que muestrear al doble de la frecuencia máxima (idea intuitiva, no la demostración)
- [ ] Ruido: qué es SNR y por qué es la métrica que determina casi todo en comunicaciones

**Recurso:** 3Blue1Brown — vídeo "But what is the Fourier Transform" (YouTube). Es la mejor explicación visual que existe, sin fórmulas pesadas.

---

## [Bloque 2 — Teoría de la comunicación](02-teoria-comunicacion.md)

- [ ] Modelo básico: fuente → codificador → canal (con ruido) → decodificador → destino (Shannon)
- [ ] Capacidad de canal: idea de que hay un límite teórico de información que cabe en un canal (sin derivar la fórmula)
- [ ] Modulación analógica: qué es AM, FM, PM y por qué existen distintas (a nivel de "qué característica de la onda cambio")
- [ ] Modulación digital: ASK, FSK, PSK, QAM — idea de constelaciones y por qué más bits por símbolo = más rápido pero más sensible a ruido
- [ ] Multiplexación: FDM, TDM, CDM — cómo compartir un mismo medio entre varios usuarios
- [ ] Codificación de canal (FEC): idea de añadir redundancia para detectar/corregir errores sin retransmitir

**Recurso:** canal de YouTube "iain explains signals systems and digital comms" — es específicamente conceptual, poco matemático.

---

## [Bloque 3 — Electrónica de comunicaciones (nivel de sistema, no de circuito)](03-electronica-comunicaciones.md)

- [ ] Qué hace un transceptor de radio a bloques: antena → LNA → mezclador → filtro → ADC/DAC
- [ ] Idea de frecuencia intermedia (heterodino) y por qué se usa
- [ ] Amplificadores: qué es ganancia, saturación, distorsión, punto de compresión (concepto, no diseño de circuito)
- [ ] Osciladores y PLLs: para qué sirven (generar/sincronizar frecuencias) sin el diseño interno

---

## [Bloque 4 — Antenas y propagación](04-antenas-propagacion.md)

- [ ] Qué es una antena conceptualmente (transductor entre corriente eléctrica y onda EM)
- [ ] Patrón de radiación, ganancia, polarización — qué significan e implican en la práctica
- [ ] Propagación: línea de vista, reflexión, difracción, multitrayecto (multipath) y por qué importan en ciudades/interiores
- [ ] Atenuación con la distancia y frecuencia — por qué el 5G de ondas altas tiene menos alcance
- [ ] Espectro radioeléctrico: por qué es un recurso regulado y escaso (bandas, licencias)

---

## [Bloque 5 — Redes y telemática](05-redes-telematica.md)

- [ ] Modelo OSI y modelo TCP/IP: para qué sirve dividir en capas (no memorizar las 7 capas, entender el porqué de la abstracción)
- [ ] Conmutación de circuitos vs conmutación de paquetes — por qué internet eligió paquetes
- [ ] Direccionamiento IP, y la idea de enrutamiento (routing): cómo un paquete "sabe" el camino
- [ ] TCP vs UDP: fiabilidad vs velocidad, y cuándo se usa cada uno
- [ ] DNS, HTTP/HTTPS: qué resuelven a alto nivel
- [ ] Redes de acceso: qué diferencia fibra, DSL, cable, y redes móviles (2G→5G) en términos de arquitectura, no de física profunda
- [ ] Conceptos de QoS, latencia vs throughput vs jitter — por qué importan distinto según la aplicación (voz vs streaming vs datos)

**Recurso:** "Computer Networking" de Kurose & Ross tiene capítulos muy bien explicados a nivel conceptual antes de entrar en detalle. También el canal de YouTube "PowerCert Animated Videos" para visualizar protocolos.

---

## [Bloque 6 — Redes móviles y sistemas celulares](06-redes-moviles.md)

- [ ] Idea de célula (cell) y reutilización de frecuencias — por qué las redes móviles escalan geográficamente
- [ ] Evolución 2G → 3G → 4G → 5G: qué problema resolvió cada generación (voz → datos → banda ancha móvil → baja latencia/IoT masivo)
- [ ] Handover/traspaso: cómo no se corta la llamada al moverte entre celdas
- [ ] Arquitectura básica: terminal, estación base, core de red — quién hace qué

---

## [Bloque 7 — Comunicaciones ópticas](07-comunicaciones-opticas.md)

- [ ] Por qué la fibra óptica permite tanto ancho de banda (luz vs electricidad como portador)
- [ ] Fibra monomodo vs multimodo — diferencia conceptual de uso (largas vs cortas distancias)
- [ ] WDM (multiplexación por longitud de onda): idea de mandar "colores" distintos de luz simultáneamente

---

## [Bloque 8 — Procesado digital de señal (a nivel de idea)](08-procesado-senal.md)

- [ ] Diferencia entre procesar en analógico vs digital (por qué casi todo se hizo digital)
- [ ] Filtros digitales: qué hace un filtro (dejar pasar/bloquear frecuencias) sin diseñar el filtro
- [ ] Compresión: idea general de por qué el audio/vídeo se puede comprimir tanto (redundancia, percepción humana) — contexto de MP3, JPEG, códecs de vídeo

---

## [Bloque 9 — Seguridad en comunicaciones](09-seguridad.md)

- [ ] Conceptos de cifrado simétrico vs asimétrico (qué problema resuelve cada uno, sin las matemáticas de RSA)
- [ ] Qué es una VPN, un firewall, un certificado TLS — a nivel de qué protegen y de qué
- [ ] Vectores de ataque típicos en redes (sniffing, spoofing, DDoS) — a nivel conceptual

---

## [Bloque 10 — Tendencias actuales (para conectar con la industria de hoy)](10-tendencias.md)

- [ ] 5G/6G: network slicing, edge computing, qué prometen realmente vs marketing
- [ ] IoT: por qué necesita protocolos distintos (bajo consumo, bajo ancho de banda) — LoRa, NB-IoT, Zigbee a nivel de "para qué sirve cada uno"
- [ ] SDN/NFV: la idea de "virtualizar" la red (separar hardware de la lógica de control)
- [ ] Satélites de baja órbita (Starlink y similares): qué cambia respecto a satélites geoestacionarios

---

## Orden recomendado

1. Bloque 0 → 1 → 2 (la base conceptual de "cómo se comunica algo")
2. Bloque 5 (redes) — es el más aplicado y motivador, ayuda a no perder el hilo
3. Bloques 3, 4, 7 (la parte física/hardware) — ahora con contexto ya tienen más sentido
4. Bloque 6 (móviles) — junta redes + radio
5. Bloques 8, 9, 10 — cierre con temas más actuales y transversales

## Notas de progreso

- Fecha de inicio:
- Ritmo objetivo (ej. 1 bloque cada 2 semanas):
