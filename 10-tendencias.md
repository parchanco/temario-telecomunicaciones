# Bloque 10 — Tendencias actuales

## 5G/6G: network slicing, edge computing, y marketing vs realidad

El 5G no es "una sola red más rápida" — su rasgo más distintivo es que puede comportarse como **varias redes virtuales distintas sobre la misma infraestructura física**, cada una optimizada para un tipo de uso diferente. Esto se llama **network slicing** (troceado de red): un operador puede tener, sobre el mismo hardware, una "porción" de red configurada para banda ancha móvil normal (tu móvil viendo vídeos), otra porción configurada para baja latencia ultra-fiable (comunicación entre coches, cirugía remota, control industrial), y otra para millones de sensores IoT de bajísimo consumo — cada una con garantías de rendimiento distintas, sin que se interfieran entre sí ni requieran redes físicas separadas.

**Edge computing** (computación en el borde) es la idea de acercar el procesamiento de datos al lugar donde se generan, en vez de mandarlo todo a un centro de datos centralizado que puede estar a cientos de kilómetros. Si una aplicación necesita respuestas casi instantáneas (un coche autónomo procesando lo que ve su cámara, o una fábrica con control en tiempo real), el tiempo que tarda la señal en viajar de ida y vuelta hasta un centro de datos lejano puede ser inaceptable — así que se procesa en servidores mucho más cercanos, a veces integrados en la propia estación base 5G.

Sobre marketing vs realidad: buena parte de las promesas iniciales de 5G (velocidades multi-gigabit constantes, latencias de 1 milisegundo en todas partes) requieren condiciones específicas (frecuencias altas mmWave, cobertura densa, slicing bien configurado) que en el despliegue real solo se dan en escenarios concretos, no de forma generalizada para el usuario medio. Lo que sí es real y ya en uso: mejoras notables de capacidad en zonas densas, y las bases técnicas (slicing, edge, baja latencia) que están habilitando casos de uso industriales que antes no eran viables con 4G. El 6G, todavía en fase de investigación, apunta a llevar estas ideas más lejos (integración con satélites, frecuencias aún más altas, IA integrada en la gestión de red) más que a representar un cambio de paradigma tan radical como el salto de 4G a 5G.

## IoT: por qué necesita protocolos distintos

El **Internet de las Cosas (IoT)** conecta dispositivos muy distintos a un móvil o un portátil: sensores de temperatura, medidores de agua, etiquetas de seguimiento, cerraduras inteligentes — dispositivos que a menudo funcionan con batería durante años, mandan muy poca información (unos pocos bytes de vez en cuando) y no pueden permitirse el consumo energético ni la complejidad de un módem 4G/5G normal.

Esto ha generado protocolos de radio pensados específicamente para ese perfil de uso, con una idea común: **sacrificar velocidad y a veces alcance a cambio de consumo energético mínimo y bajo coste**:

- **LoRa**: diseñado para **largo alcance** (kilómetros) con consumo mínimo, a costa de velocidades muy bajas (unos pocos kbps) — ideal para sensores rurales o agrícolas dispersos en un área amplia.
- **NB-IoT (Narrowband IoT)**: usa la infraestructura de las redes móviles existentes (una "rebanada" muy estrecha de espectro dentro de la red 4G/5G del operador), pensado para sensores con conectividad garantizada por el propio operador, con buena penetración en interiores (sótanos, contadores de gas) y consumo muy bajo.
- **Zigbee**: pensado para **redes locales de corto alcance** dentro de un hogar o edificio (domótica: bombillas, sensores de puerta), con la particularidad de que los propios dispositivos pueden retransmitir la señal de unos a otros formando una red en malla, sin depender de una única antena central.

La lección conceptual: no existe "el mejor protocolo IoT" en abstracto — cada uno resuelve una combinación distinta de alcance, consumo, velocidad y coste, y elegir el adecuado depende enteramente del caso de uso, igual que en el bloque 4 se vio que no hay una única frecuencia "mejor" sino disyuntivas entre alcance y velocidad.

## SDN/NFV: virtualizar la red

Tradicionalmente, cada función de una red (un router, un firewall, una pasarela de voz) era una **caja de hardware específica y propietaria**, fabricada por un proveedor concreto, cara de sustituir o actualizar. Dos ideas relacionadas han cambiado esto:

- **SDN (Software-Defined Networking)**: separa el "plano de control" (las decisiones de qué camino debe seguir el tráfico) del "plano de datos" (el hardware que efectivamente mueve los bits). En vez de que cada router tome decisiones de forma aislada, un controlador central en software tiene visión global de la red y programa el comportamiento de todos los dispositivos — es parecido a pasar de tener muchos conductores tomando decisiones individuales en un cruce, a tener una torre de control que ve todo el tráfico y coordina las señales de forma centralizada.
- **NFV (Network Function Virtualization)**: en vez de que cada función de red (firewall, balanceador de carga, pasarela de voz) sea una caja física dedicada, se ejecuta como **software** sobre servidores genéricos, igual que cualquier otra aplicación. Esto permite desplegar, escalar o sustituir funciones de red mucho más rápido y barato que cambiando hardware físico.

Ambas ideas son la razón técnica por la que el core de las redes 5G modernas puede ejecutarse mayormente como software en centros de datos convencionales, en vez de en hardware especializado propietario como en generaciones anteriores — lo cual conecta directamente con el network slicing mencionado arriba: es mucho más fácil crear "redes virtuales" distintas cuando la red ya es, en gran parte, software configurable.

## Satélites de baja órbita (LEO) frente a geoestacionarios

Los satélites de comunicaciones clásicos son **geoestacionarios (GEO)**: orbitan a unos 36.000 km de altura, a una velocidad tal que permanecen siempre sobre el mismo punto de la Tierra — lo cual es muy cómodo (una antena fija en tierra siempre lo ve en el mismo sitio del cielo), pero a costa de una **latencia alta**: la señal tiene que recorrer esa distancia enorme de ida y de vuelta, lo que introduce varios cientos de milisegundos de retardo, notorio en aplicaciones interactivas.

Las constelaciones de **satélites de órbita baja (LEO, Low Earth Orbit)**, como Starlink, orbitan a unos pocos cientos de kilómetros de altura — mucho más cerca, lo que reduce la latencia a niveles comparables con una conexión terrestre normal. El precio a pagar: a esa altura, un solo satélite no puede "quedarse quieto" sobre un punto (se mueve rápido respecto a la superficie), así que hace falta una **constelación de muchísimos satélites** trabajando en conjunto, de forma que siempre haya alguno visible sobre cualquier punto de la Tierra, con el sistema en tierra "entregando" la conexión de un satélite al siguiente a medida que se mueven — un concepto que recuerda directamente al handover entre células del bloque 6, aplicado esta vez entre satélites en vez de entre antenas terrestres.

Esto es lo que ha hecho viable, por primera vez, ofrecer internet satelital con una experiencia razonablemente parecida a la terrestre en zonas rurales o remotas sin cobertura de fibra o móvil — a costa de una infraestructura mucho más compleja (miles de satélites en vez de unos pocos) que las constelaciones geoestacionarias tradicionales.
