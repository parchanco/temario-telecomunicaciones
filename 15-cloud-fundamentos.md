# Bloque 15 — Cloud computing (fundamentos conceptuales)

> Este bloque es deliberadamente **agnóstico de proveedor**: se centra en el "por qué" de los conceptos que AWS, Azure o GCP dan por sentados. Si ya estás haciendo un curso de AWS, este bloque es el mapa conceptual sobre el que colocar cada servicio concreto que te enseñen allí.

## Qué es realmente "la nube"

Despojado de marketing, el cloud es esto: **centros de datos gigantes de otra empresa, alquilados por horas (o segundos), con una API para pedir y soltar recursos al instante**. Los tres cambios de fondo respecto a tener tus propios servidores:

- **De inversión a gasto**: en vez de comprar servidores (que hay que dimensionar para el pico de demanda y que se quedan ociosos el resto del tiempo), pagas solo por lo que usas, cuando lo usas.
- **Elasticidad**: puedes pasar de 2 a 200 servidores en minutos y volver a 2 después — algo físicamente imposible con hardware propio. Es la misma lógica de la conmutación de paquetes frente a circuitos (bloque 5): compartir dinámicamente un recurso enorme entre muchos usuarios cuyos picos no coinciden, en vez de que cada uno reserve capacidad fija "por si acaso".
- **Servicios gestionados**: no solo alquilas máquinas — alquilas bases de datos, colas, balanceadores (bloque 13) donde el proveedor se encarga de parches, réplicas y copias de seguridad. Estás comprando la operación, no solo el hardware.

## IaaS, PaaS, SaaS: cuánta responsabilidad delegas

Los tres modelos de servicio se distinguen por dónde está la frontera entre lo que gestionas tú y lo que gestiona el proveedor:

- **IaaS (Infrastructure as a Service)**: alquilas máquinas virtuales, redes y discos "en crudo" (EC2, por ejemplo). Tú gestionas el sistema operativo, los parches y todo lo que corre encima. Máxima flexibilidad, máxima responsabilidad.
- **PaaS (Platform as a Service)**: alquilas una plataforma donde solo subes tu código (Heroku, App Runner, Cloud Run). El proveedor gestiona el sistema operativo, el escalado y el despliegue. Menos control, mucho menos trabajo operativo.
- **SaaS (Software as a Service)**: alquilas la aplicación terminada (Gmail, Notion). No gestionas nada técnico.

La idea que conviene interiorizar es que no es una escalera de "mejor a peor", sino el mismo patrón de capas de siempre (bloque 0): **cada modelo oculta más complejidad a cambio de menos control**. Elegir modelo es decidir qué parte del problema quieres que sea tuya. Y el famoso **"modelo de responsabilidad compartida"** en seguridad es la consecuencia directa: el proveedor asegura la infraestructura, pero lo que tú configures encima (permisos, datos expuestos) sigue siendo problema tuyo — la mayoría de brechas en cloud son errores de configuración del cliente, no fallos del proveedor.

## Regiones y zonas de disponibilidad: la geografía importa

Un proveedor cloud organiza sus centros de datos en:

- **Regiones**: agrupaciones geográficas independientes (Irlanda, Virginia, São Paulo...). Eliges región por **latencia hacia tus usuarios** (la distancia física impone un mínimo de latencia, como se vio en los bloques 4 y 14 — la luz no viaja más rápido porque pagues más) y por **requisitos legales** (dónde pueden residir ciertos datos, como exige el RGPD europeo).
- **Zonas de disponibilidad (AZ)**: dentro de una región, varios centros de datos físicamente separados (edificios distintos, alimentación eléctrica distinta) pero conectados entre sí con fibra de bajísima latencia (bloque 7). Desplegar en varias AZ protege frente al fallo de un edificio entero sin pagar la latencia de cruzar a otra región.

Esto conecta directamente con el teorema CAP del bloque 12: replicar datos entre regiones lejanas te obliga a elegir entre esperar la confirmación remota en cada escritura (consistencia, con latencia alta) o aceptar réplicas temporalmente desincronizadas (disponibilidad, con consistencia eventual). La geografía no es un detalle de infraestructura — condiciona la arquitectura de tu aplicación.

## Redes en la nube: la VPC

Una **VPC (Virtual Private Cloud)** es tu propia red privada virtual dentro del proveedor: tú defines los rangos de IPs, las subredes y las reglas de qué puede hablar con qué. Es exactamente el mismo concepto de red privada del bloque 5 (y de las redes de contenedores del bloque 13), una capa más arriba: el proveedor virtualiza sobre su red física miles de redes de clientes, aisladas entre sí, igual que Docker virtualiza redes dentro de una máquina.

Los conceptos que se repiten en cualquier proveedor, y su equivalente de los bloques anteriores:

- **Subredes públicas vs privadas**: qué máquinas son alcanzables desde internet y cuáles solo desde dentro — la misma distinción que hace tu router doméstico con NAT (bloques 5 y 11).
- **Security groups / reglas de firewall**: el firewall del bloque 9, como servicio configurable.
- **Gateways y peering**: los routers de borde del bloque 5, conectando tu VPC con internet, con otras VPCs o con tu oficina mediante VPN (bloque 9).

Si entendiste los bloques 5, 9 y 13, la red de AWS no tiene conceptos nuevos — solo nombres nuevos para las mismas piezas.

## Escalado: vertical vs horizontal, y el autoescalado

- **Escalado vertical**: hacer la máquina más grande (más CPU, más RAM). Simple, pero tiene techo físico y suele requerir reinicio.
- **Escalado horizontal**: añadir más máquinas iguales detrás de un balanceador de carga (bloque 13). No tiene techo práctico, pero exige que tu aplicación lo tolere — la restricción clave es que las instancias sean **sin estado** (stateless): si una instancia guarda datos en su memoria o disco local (sesiones de usuario, ficheros subidos), matarla o añadir otra rompe cosas. Por eso el estado se saca fuera, a servicios compartidos (base de datos, Redis, almacenamiento de objetos).
- **Autoescalado**: el proveedor añade o quita instancias automáticamente según métricas (CPU, peticiones por segundo — las métricas del bloque 14). Es la elasticidad hecha política: defines las reglas y el sistema se dimensiona solo.

## Serverless: llevar el modelo al extremo

En el modelo **serverless** (AWS Lambda y equivalentes) ni siquiera alquilas máquinas: subes una función, y el proveedor la ejecuta solo cuando llega un evento (una petición HTTP, un mensaje en una cola del bloque 12, un archivo subido), cobrándote por milisegundos de ejecución real. Escala de cero a miles de ejecuciones paralelas sin configurar nada.

Las contrapartidas que hay que conocer: el **arranque en frío** (la primera ejecución tras un periodo de inactividad tarda más, porque hay que levantar el entorno — relevante para los percentiles p99 del bloque 14), límites de duración por ejecución, y la imposibilidad de mantener estado o conexiones persistentes entre invocaciones (un WebSocket del bloque 11 no encaja de forma natural en una función efímera). Serverless no es "mejor" que los contenedores del bloque 13 — es otro punto en el mismo espectro de compromisos entre control y comodidad.

## Almacenamiento: objetos, bloques y bases de datos gestionadas

Tres tipos que cubren casi todo:

- **Almacenamiento de objetos** (S3 y equivalentes): guardas archivos completos identificados por una clave, accesibles por HTTP. Barato, prácticamente infinito, sin jerarquía real de directorios. Es donde viven los assets estáticos que luego sirve un CDN (bloque 11).
- **Almacenamiento de bloques** (discos virtuales): un "disco duro" que se conecta a una máquina virtual concreta. Para lo que necesite un sistema de archivos de verdad (la base de datos que gestionas tú, por ejemplo).
- **Bases de datos gestionadas** (RDS, DynamoDB...): el proveedor opera la base de datos por ti — réplicas, copias de seguridad, failover automático (aplicando por debajo el consenso y la replicación del bloque 12).

## IAM: identidad y permisos como cimiento

**IAM (Identity and Access Management)** define **quién** (usuarios, pero sobre todo servicios y máquinas) puede hacer **qué** sobre **qué recurso**. Dos ideas conceptuales que importan más que cualquier detalle de sintaxis:

- **Mínimo privilegio**: cada componente debe tener solo los permisos que necesita, no "admin para que funcione". La mayoría de incidentes graves en cloud son credenciales con permisos excesivos filtradas o mal usadas — es el equivalente en cloud a los vectores de ataque del bloque 9.
- **Las máquinas también tienen identidad**: en cloud, un servidor o una función serverless asume un **rol** con permisos temporales, en vez de llevar contraseñas incrustadas en el código. Esto elimina la categoría entera de "credenciales hardcodeadas en el repositorio".

## El modelo de costes: la factura como métrica de arquitectura

En cloud, cada decisión de arquitectura es también una decisión económica, y hay un coste que sorprende a todo el mundo la primera vez: **la salida de datos (egress)**. Meter datos en la nube es gratis o casi; **sacarlos** (hacia internet, o entre regiones) se cobra por gigabyte. Esto tiene una consecuencia directa de diseño: las arquitecturas tienden a procesar los datos donde ya están, en vez de moverlos — la misma lógica del edge computing del bloque 10, esta vez impuesta por la factura en vez de por la latencia. Una arquitectura que en el diagrama parece limpia puede ser económicamente inviable si mueve terabytes entre regiones cada día.

---

## Profundización

### Infraestructura como código: la red que vive en un repositorio

El cambio de práctica más importante del cloud no es ningún servicio, sino cómo se crea la infraestructura: en vez de hacer clics en una consola web, se **declara en ficheros de texto** (Terraform, CloudFormation, Pulumi) lo que debe existir — "una VPC con estas subredes, este balanceador, estas reglas" — y una herramienta compara ese estado deseado con la realidad y aplica la diferencia.

Las consecuencias, que como desarrollador reconocerás al vuelo porque son las de git aplicadas a infraestructura: entornos **reproducibles** (levantar un clon de producción para pruebas es ejecutar el mismo código con otras variables), cambios **revisables** (una regla de firewall nueva pasa por pull request — el bloque 9 encontrándose con el code review), historial y rollback, y el fin de la infraestructura-misterio que solo entendía la persona que ya no está en la empresa. El modelo mental profundo es el **declarativo vs imperativo**: no describes los pasos, describes el destino, y la herramienta calcula el camino — exactamente el mismo principio de Kubernetes (bloque 13) y del SDN (bloque 10): un plano de control que reconcilia realidad con intención.

### Vendor lock-in: el precio real de la comodidad

Cada servicio gestionado que adoptas es un intercambio silencioso: menos operación hoy, más coste de salida mañana. No todo ata igual, y conviene distinguirlo: cómputo y contenedores son bastante portables (un contenedor corre en cualquier parte — bloque 13); las bases de datos atan por gravedad de los datos (mover terabytes cuesta egress, tiempo y riesgo); y lo que más ata son los **servicios "sin equivalente"** — si tu arquitectura entera está expresada en Lambda + DynamoDB + colas propietarias, migrar no es mover código sino rediseñar.

La postura madura no es "evita el lock-in" (los servicios gestionados son precisamente el valor del cloud — rechazarlos por principio es pagar el cloud sin cobrar su beneficio) sino **decidir conscientemente qué te ata y a cambio de qué**. El multi-cloud como estrategia defensiva suena bien en las presentaciones y en la práctica suele significar pagar la complejidad de dos proveedores para usar el mínimo común de ambos; la redundancia real que casi todos necesitan es multi-región dentro de un proveedor, no multi-proveedor.

### Los pilares de una buena arquitectura (el resumen del resumen)

Todos los proveedores publican un marco de "well-architected" con los mismos cinco o seis pilares; como mapa mental, valen para revisar cualquier diseño, cloud o no: **seguridad** (mínimo privilegio, cifrado — bloques 9 y este), **fiabilidad** (asumir que todo falla y diseñar para ello — bloques 12 y 14: multi-AZ, reintentos idempotentes, presupuestos de error), **rendimiento** (medir antes de optimizar — bloque 14), **coste** (el egress, el autoescalado bien dimensionado, apagar lo que no se usa), y **excelencia operativa** (infraestructura como código, observabilidad, automatización). La utilidad práctica: ante cualquier decisión de arquitectura, pasar la lista de cinco preguntas detecta el pilar que estás descuidando — casi siempre hay uno, y casi siempre es coste u operación.

## Ejercicio práctico

Dimensiona y presupuesta, en papel, una app real — el mejor antídoto contra el "en cloud todo es barato/carísimo" sin números:

1. Toma una app tipo: API + base de datos + frontend estático, 10.000 usuarios/día, ~1 MB de transferencia media por visita.
2. Con la calculadora de precios de AWS (calculator.aws), estima tres arquitecturas: (a) una VM mediana con todo dentro, (b) contenedores gestionados + base de datos gestionada multi-AZ, (c) serverless + DynamoDB + S3/CloudFront.
3. Anota tres cifras por opción: coste mensual, coste si el tráfico se multiplica por 10 de golpe, y horas/mes de operación humana que estimas. Observa cómo (a) gana en precio base, (c) gana en elasticidad y operación, y (b) queda en medio — las cinco disyuntivas del bloque 0, con euros.
4. Extra: localiza en tu estimación cuánto pesa el egress. Suele ser la sorpresa.

## Autoevaluación

1. ¿Qué significa "declarativo" en infraestructura como código y qué otras dos tecnologías de este temario usan el mismo principio de reconciliación?
2. ¿Por qué "multi-cloud por si acaso" suele ser mala estrategia y qué redundancia resuelve de verdad el caso común?
3. Ordena de menos a más lock-in: contenedores, funciones serverless con servicios propietarios, base de datos gestionada compatible con PostgreSQL. Justifica.
4. Tu app funciona perfecta en una AZ, en una región, sin copias de seguridad probadas. ¿Qué pilares estás violando y cuál es el fallo más barato de corregir hoy?

---

**Navegación:** [⬅️ Bloque 14 — Observabilidad y rendimiento](14-observabilidad-rendimiento.md) · [🏠 Índice](README.md) · [Bloque 16 — Sistemas de telecomunicación del mundo real: VoIP, GPS y radiodifusión ➡️](16-sistemas-mundo-real.md)
