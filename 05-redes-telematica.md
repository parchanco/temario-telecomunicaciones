# Bloque 5 — Redes y telemática

## El modelo de capas: OSI y TCP/IP

Ya se introdujo la idea en el bloque 0: dividir un problema complejo en capas independientes, donde cada capa resuelve un problema concreto y oculta esa complejidad a la capa de arriba. El **modelo OSI** (7 capas) y el **modelo TCP/IP** (4-5 capas, el que realmente se usa en internet) son formalizaciones de esa idea.

No merece la pena memorizar las 7 capas de OSI de carrerilla — lo que sí merece la pena es entender el reparto de responsabilidades, de abajo a arriba:

- **Física**: bits en el cable, la fibra o el aire (voltajes, luz, ondas de radio).
- **Enlace**: cómo se comunican dos dispositivos conectados directamente (misma red local) — direcciones MAC, Ethernet, WiFi.
- **Red**: cómo se llevan los datos de una red a otra, potencialmente a través de muchos saltos intermedios — aquí vive IP y el enrutamiento.
- **Transporte**: cómo se garantiza (o no) que los datos lleguen completos y en orden entre dos programas — aquí viven TCP y UDP.
- **Aplicación**: el protocolo específico que entiende tu programa — HTTP, DNS, correo, etc.

La razón de ser de esta separación: un ingeniero que diseña un router no necesita saber nada de HTTP, y un desarrollador que programa una app web no necesita saber nada de cómo viajan los bits por la fibra. Cada capa "habla" solo con la capa inmediatamente superior e inferior, mediante una interfaz bien definida.

## Conmutación de circuitos vs conmutación de paquetes

Antes de internet, la telefonía clásica funcionaba por **conmutación de circuitos**: al hacer una llamada, se reservaba un camino físico dedicado (o una porción fija de un cable) entre los dos extremos, durante toda la duración de la llamada, aunque nadie estuviera hablando en ese instante. Es como reservar un carril entero de autopista solo para tu coche, de punto a punto, durante todo el viaje.

Internet eligió un modelo distinto: la **conmutación de paquetes**. Los datos se trocean en paquetes pequeños, cada uno con una dirección de destino, y viajan de forma independiente por la red, compartiendo dinámicamente los enlaces con paquetes de muchísimos otros usuarios a la vez — pueden incluso llegar por caminos distintos y reordenarse al final.

¿Por qué ganó la conmutación de paquetes? Porque es mucho más eficiente cuando el tráfico es "a ráfagas" (como navegar por internet: pides una página, hay silencio, pides otra) en vez de continuo (como una llamada de voz clásica): en vez de reservar recursos todo el tiempo "por si acaso", el enlace se reparte dinámicamente entre quien realmente tiene datos que enviar en cada instante. El precio a pagar es que ya no hay garantía de un camino fijo ni de un tiempo de llegada constante — de ahí surge la necesidad de conceptos como el orden de paquetes, la latencia variable (jitter) y los protocolos de fiabilidad.

## Direccionamiento IP y enrutamiento

Cada dispositivo conectado a una red necesita una **dirección IP**, única (al menos dentro del ámbito relevante), que funciona de forma parecida a una dirección postal: identifica de forma unívoca dónde debe llegar cada paquete.

El **enrutamiento (routing)** es el proceso por el cual un paquete, que puede tener que atravesar decenas de redes intermedias para llegar a su destino, va encontrando el camino salto a salto. No hay ningún dispositivo que conozca el camino completo de antemano: cada **router** simplemente mira la dirección de destino del paquete, consulta su propia tabla de rutas, y decide "¿a cuál de mis vecinos directos le paso esto para que se acerque al destino?" — de forma parecida a cómo, si te pierdes en una ciudad desconocida y preguntas a la gente, cada persona no sabe la dirección exacta pero sí sabe indicarte "sigue por esa calle, ese barrio queda por ahí". El paquete va avanzando salto a salto hasta llegar.

Las tablas de rutas se construyen y actualizan automáticamente mediante **protocolos de enrutamiento** que hacen que los routers se intercambien información sobre qué redes conocen y a qué distancia — de forma que, si un enlace se cae, la red pueda recalcular rutas alternativas sin intervención humana.

## TCP vs UDP: fiabilidad vs velocidad

Una vez que los paquetes ya saben viajar (capa de red), hace falta decidir **qué garantías** se le dan a la aplicación sobre esos datos. Esa es la capa de transporte, y hay dos protagonistas:

- **TCP (Transmission Control Protocol)**: garantiza que los datos lleguen **completos, en orden y sin duplicados** — si un paquete se pierde, TCP detecta el hueco y lo retransmite automáticamente; si llegan desordenados, los reordena antes de entregárselos a la aplicación. A cambio de estas garantías, introduce cierta latencia (hay que esperar confirmaciones, retransmitir lo perdido).
- **UDP (User Datagram Protocol)**: manda los paquetes "y ya está" — no garantiza que lleguen, ni en qué orden, ni si se duplican. A cambio, es mucho más rápido y ligero porque no tiene toda esa maquinaria de control.

¿Cuándo usar cada uno? Depende de qué te duele más, si perder datos o llegar tarde:

- Descargar un archivo, cargar una web, mandar un email: necesitas que **todos los bytes lleguen correctos** — un archivo con un byte corrupto puede ser inservible. Aquí gana TCP.
- Una videollamada o una partida online en tiempo real: es mucho peor **esperar** a que se retransmita un paquete perdido (introduciría un corte notable) que simplemente **perder ese paquete y seguir** (un frame de vídeo perdido apenas se nota, y el jugador prefiere continuidad a perfección). Aquí gana UDP.

## DNS y HTTP/HTTPS, a alto nivel

- **DNS (Domain Name System)**: es la "agenda de contactos" de internet. Los humanos escribimos nombres (`google.com`), pero las redes funcionan con direcciones IP numéricas. DNS resuelve ese nombre a la IP correspondiente antes de que tu navegador pueda siquiera empezar a conectarse. Es un sistema distribuido jerárquico — nadie tiene "toda" la lista, sino que la consulta va subiendo y bajando por servidores especializados hasta encontrar la respuesta.
- **HTTP**: el protocolo con el que un navegador (u otra app) pide contenido a un servidor web y este responde — es el idioma en el que "hablan" cliente y servidor en la capa de aplicación.
- **HTTPS**: es HTTP, pero envuelto en una capa de cifrado (TLS) que garantiza que nadie en el camino pueda leer o alterar los datos sin que se note — de esto se habla más en el bloque de seguridad.

## Redes de acceso: fibra, DSL, cable, móvil

La **red de acceso** es el "último tramo" que conecta tu casa o tu móvil con la red del operador — el resto de internet, más allá de ese primer tramo, es prácticamente el mismo para todos.

- **DSL**: reutiliza el cable de cobre del teléfono tradicional, ya instalado en casi todas las casas, para transmitir datos además de voz — limitado en velocidad y muy dependiente de la distancia a la central.
- **Cable (HFC)**: reutiliza la infraestructura de televisión por cable (coaxial), generalmente combinada con fibra en el tramo troncal — mejor velocidad que DSL.
- **Fibra óptica (FTTH)**: lleva la fibra hasta la propia vivienda, sin ningún tramo de cobre — velocidades muchísimo mayores y más simétricas (subida casi igual de rápida que bajada), porque la luz por fibra tiene un ancho de banda potencial enorme (bloque 7).
- **Redes móviles**: en vez de un cable hasta tu casa, el "último tramo" es un enlace de radio hasta la antena más cercana (bloque 6) — con la ventaja de la movilidad, pero compartiendo el medio radio con todos los usuarios cercanos, a diferencia de un cable dedicado.

La diferencia importante a nivel conceptual no es tanto la tecnología concreta, sino entender que **cuanto más "dedicado" y de mayor capacidad es el medio físico del último tramo, mejor y más estable es el servicio** — y que la fibra ganó la carrera a largo plazo precisamente porque su capacidad física es órdenes de magnitud mayor que la del cobre.

## QoS: latencia, throughput y jitter

Tres métricas que se confunden a menudo pero miden cosas distintas:

- **Throughput (o ancho de banda efectivo)**: cuántos datos por segundo consigues transmitir — la métrica que casi todo el mundo mira ("tengo 500 Mb de fibra").
- **Latencia**: cuánto tiempo tarda un dato en llegar de un punto a otro (ida, o ida y vuelta — "ping"). No tiene por qué estar relacionada con el throughput: puedes tener una conexión muy rápida (mucho throughput) pero con latencia alta (por ejemplo, un enlace satelital geoestacionario, bloque 10), o al revés.
- **Jitter**: la variación de la latencia de un paquete a otro — no es que todo llegue tarde, es que el retraso es inconsistente (unas veces 20 ms, otras 200 ms).

Por qué importa distinguirlas: para **descargar un archivo grande**, lo único que importa de verdad es el throughput — puedes esperar unos segundos más y da igual. Para una **videollamada o una partida online**, el throughput necesario es relativamente bajo, pero la latencia y sobre todo el **jitter** son críticos — un audio que llega "a trompicones" (jitter alto) es mucho más molesto que un audio algo más lento pero constante. **QoS (Quality of Service)** es precisamente el conjunto de técnicas para priorizar tráfico según qué métrica le importa más a cada aplicación (por ejemplo, dando prioridad a los paquetes de voz sobre los de una descarga en segundo plano dentro de la misma red).

---

## Profundización

### NAT: el parche que sostiene internet

Las direcciones IPv4 son números de 32 bits: unos 4.300 millones posibles, muchos menos que dispositivos conectados existen. **NAT (Network Address Translation)** es el parche que lo hace funcionar: tu router tiene **una** IP pública, y todos los dispositivos de tu casa usan IPs privadas (los rangos `192.168.x.x`, `10.x.x.x`) que solo tienen sentido dentro de tu red. Cuando tu portátil sale a internet, el router **reescribe** cada paquete — sustituye la IP privada de origen por su IP pública y apunta en una tabla "esta conversación era del portátil" para saber a quién entregar las respuestas.

Dos consecuencias que ya te has cruzado: (1) desde fuera **nadie puede iniciar una conexión hacia tu portátil** — el router no sabría a quién entregarla; es una seguridad accidental, pero es la razón de que "abrir puertos" exista y de todo el circo STUN/TURN de WebRTC (bloque 11); (2) **IPv6** existe precisamente para eliminar esta escasez (direcciones de 128 bits: prácticamente infinitas, cada dispositivo puede tener IP pública propia) — y su adopción lleva 25 años siendo lenta porque NAT hizo el problema lo bastante soportable como para que nadie tuviera urgencia. Una lección de ingeniería en sí misma: un buen parche puede retrasar décadas la solución correcta.

### DHCP y ARP: los dos protocolos invisibles que usas cada día

Cuando tu portátil se conecta a un WiFi, antes de poder hacer nada ocurren dos conversaciones automáticas que nadie ve:

- **DHCP**: el portátil grita a la red "¿alguien me da una IP?" y el router le asigna una, junto con la máscara de red, la puerta de enlace y el servidor DNS a usar. Es la razón de que conectarse a una red sea "automático" — en los 90 se configuraba todo eso a mano.
- **ARP**: dentro de una red local, los paquetes se entregan realmente por dirección MAC (capa de enlace), no por IP. ARP es el protocolo con el que un dispositivo pregunta "¿quién tiene la IP 192.168.1.1? dime tu MAC" para poder entregarle tramas físicamente. Casi ningún desarrollador lo conoce hasta que un ataque de *ARP spoofing* (alguien respondiendo "yo soy el router" para interceptar tráfico, bloque 9) se lo presenta.

### El handshake TCP y por qué la latencia se paga varias veces

Antes de que TCP transporte un solo byte útil, cliente y servidor negocian en tres pasos (SYN → SYN-ACK → ACK): un viaje de ida y vuelta completo (1 RTT) "perdido" en saludos. Si encima hay TLS, la negociación criptográfica añade más viajes. Consecuencia práctica que conecta con los bloques 11 y 14: **en conexiones nuevas, la latencia se paga multiplicada** — con 100 ms de RTT, el primer byte útil de una petición HTTPS nueva puede tardar 300-400 ms aunque el servidor responda instantáneamente. Por eso existen keep-alive, la reutilización de conexiones, y por eso QUIC (HTTP/3) fusionó el handshake de transporte con el de cifrado en un solo viaje: no es optimización esotérica, es atacar directamente esta multiplicación.

## Ejercicio práctico

Tres comandos desde tu terminal, mirando lo que ya usas a diario:

1. `traceroute google.com` (o `mtr google.com`): cuenta los saltos. Los primeros serán tu router (IP privada — ahí está NAT) y la red de tu operador; observa cómo crece la latencia por tramos.
2. `dig google.com` y luego `dig google.com @1.1.1.1`: la misma pregunta a tu DNS por defecto y al de Cloudflare. Compara tiempos de respuesta y fíjate en el TTL — cuánto tiempo se puede cachear esa respuesta.
3. `curl -v https://google.com 2>&1 | head -30`: identifica en la salida las fases que ahora conoces — resolución DNS, conexión TCP, negociación TLS, y solo al final la petición HTTP en sí.

## Autoevaluación

1. ¿Por qué internet eligió paquetes en vez de circuitos? ¿Qué se sacrificó a cambio?
2. Explica por qué detrás de un NAT puedes navegar sin problema pero no puedes alojar un servidor sin "abrir puertos".
3. Una app de videollamada va mal con jitter alto pero una descarga va perfecta en la misma red. ¿Por qué?
4. ¿Cuántos viajes de ida y vuelta paga la primera petición HTTPS a un dominio nuevo, y qué tres mecanismos existen para no volver a pagarlos?

---

**Navegación:** [⬅️ Bloque 4 — Antenas y propagación](04-antenas-propagacion.md) · [🏠 Índice](README.md) · [Bloque 6 — Redes móviles y sistemas celulares ➡️](06-redes-moviles.md)
