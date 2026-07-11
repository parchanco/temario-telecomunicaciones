# Bloque 13 — Redes en contenedores y cloud

[← 20. Tema anterior](12-sistemas-distribuidos.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [Mi progreso](PROGRESO.md) · [22. Tema siguiente →](14-observabilidad-rendimiento.md)

> **Objetivo del bloque:** seguir el tráfico por redes virtuales, descubrimiento, balanceo y políticas dentro de contenedores y Kubernetes.

> Docker y Kubernetes se explican habitualmente desde el ángulo de "empaquetar aplicaciones", pero por debajo son, en gran parte, **problemas de redes** — los mismos conceptos de los bloques 5 y 6, aplicados dentro de una máquina o un clúster en vez de entre continentes.

## Redes de contenedores: cada contenedor es "otra máquina" en miniatura

Cuando arrancas un contenedor Docker, por debajo se crea una **interfaz de red virtual** y se le asigna una IP propia dentro de una red virtual privada que Docker gestiona en el host — de forma muy parecida a cómo tu router doméstico asigna IPs privadas a cada dispositivo de tu casa (bloque 5). Dos contenedores en la misma red virtual pueden "verse" entre sí por su IP interna (o por su nombre, resuelto mediante un DNS interno que Docker gestiona automáticamente), igual que dos ordenadores en tu wifi de casa.

El motivo de aislar así cada contenedor: cada uno cree que tiene su propia pila de red completa (su propia interfaz, su propia tabla de rutas), aunque en realidad varios contenedores están compartiendo el mismo hardware físico y el mismo kernel — es una capa de abstracción más, siguiendo exactamente el mismo patrón del bloque 0: ocultar la complejidad real (todos comparten la misma máquina) detrás de una interfaz simple (cada uno "tiene su propia red").

Para que el mundo exterior pueda hablar con un contenedor, hace falta **mapear puertos** del host hacia el contenedor (por ejemplo, `-p 8080:80`) — el equivalente conceptual a la traducción de direcciones (NAT) que ya se mencionó en el bloque 11 al hablar de WebRTC: una capa externa reescribe direcciones/puertos para que el tráfico llegue al destino interno correcto.

## Kubernetes: DNS interno, Services e Ingress

Un clúster de Kubernetes agrava el problema anterior: los contenedores (agrupados en *pods*) se crean y destruyen constantemente (al escalar, al desplegar una nueva versión, al recuperarse de un fallo), así que sus IPs internas cambian todo el rato. No puedes permitirte que un servicio tenga que "acordarse" de la IP concreta de otro.

La solución es, otra vez, el mismo patrón del DNS del bloque 5: Kubernetes da a cada **Service** (una agrupación estable de pods que hacen lo mismo) un **nombre DNS interno fijo**, y resuelve ese nombre dinámicamente hacia la IP del pod disponible en cada momento — el equivalente en miniatura a cómo DNS resuelve `google.com` a la IP del servidor correcto sin que tengas que conocerla de antemano. Por debajo, el Service además hace **balanceo de carga**: si hay varios pods idénticos corriendo, reparte las peticiones entrantes entre ellos.

El **Ingress** es la pieza que gestiona la entrada de tráfico externo hacia el clúster — funciona como un router/reverse-proxy de entrada que, según el dominio o la ruta de la petición HTTP, decide a qué Service interno enviarla. Es, conceptualmente, el mismo rol que cumple un router de borde en una red de telecomunicaciones (bloque 5): la puerta de entrada que decide cómo encaminar el tráfico hacia el interior de una red que, desde fuera, se ve como una única entidad.

## Service mesh: cuando la propia red se vuelve "programable"

En clústeres grandes, con muchos microservicios (bloque 12) hablando entre sí, gestionar manualmente cosas como reintentos, cifrado entre servicios, límites de tasa o trazabilidad de cada llamada se vuelve inviable si cada equipo tiene que implementarlo dentro de su propio código.

Un **service mesh** (Istio, Linkerd) resuelve esto insertando un pequeño proxy (llamado *sidecar*) junto a cada servicio, de forma que **todo** el tráfico entre servicios pasa a través de esos proxies de forma transparente a la aplicación — la app ni se entera. Esos proxies, coordinados centralmente, aplican de forma uniforme cifrado (TLS automático entre servicios, bloque 9), reintentos, *circuit breakers* (cortar llamadas hacia un servicio que está fallando repetidamente, para no empeorar la situación) y recogida de métricas.

Es exactamente la misma idea de **SDN** del bloque 10 (separar el "plano de control" del "plano de datos"), pero aplicada a la comunicación entre microservicios en vez de a routers físicos: en lugar de que cada servicio implemente su propia lógica de red, hay una capa de control centralizada que programa el comportamiento de todos los proxies distribuidos.

## Load balancers: repartir tráfico con criterio

Un **balanceador de carga** reparte las peticiones entrantes entre varias instancias de un mismo servicio, para que ninguna se sature mientras otras están ociosas. Los algoritmos más comunes, a nivel de idea:

- **Round robin**: reparte por turnos, uno a cada instancia sucesivamente — simple, pero no tiene en cuenta si una instancia está más ocupada que otra en ese momento.
- **Menor número de conexiones activas**: manda la siguiente petición a quien tenga menos trabajo pendiente en ese instante — más justo bajo carga desigual.
- **Basado en latencia/salud**: monitoriza activamente qué instancias responden más rápido o han dejado de responder (*health checks*), y ajusta el reparto o retira instancias defectuosas de la rotación automáticamente.

La conexión con telecom: un balanceador de carga resuelve, a nivel de aplicación, el mismo problema que resolvía la reutilización de células del bloque 6 (repartir usuarios entre estaciones base cercanas para que ninguna se congestione) o el enrutamiento del bloque 5 (elegir el mejor camino disponible en cada momento) — es el mismo problema de fondo, "repartir demanda sobre capacidad limitada", resuelto en una capa distinta de la pila.

---

## Profundización

### Namespaces: qué virtualiza realmente el kernel

Desmontar la magia de Docker ayuda a depurarlo: un contenedor **no es una máquina virtual pequeña** — no hay otro sistema operativo dentro. Es un proceso normal de Linux al que el kernel, mediante **namespaces**, le sirve una vista privada de ciertos recursos: su propio árbol de procesos (PID namespace — dentro, tu app es el PID 1), su propio sistema de archivos (mount), y — el que importa aquí — su propia **pila de red completa** (network namespace): interfaces, tabla de rutas, reglas de firewall e IPs propias, virtualizadas por el kernel del host. Los **cgroups** completan el cuadro limitando cuánta CPU y memoria puede consumir.

Consecuencias prácticas de entender esto: los contenedores arrancan en milisegundos (es lanzar un proceso, no botar un SO), todos los contenedores comparten el kernel del host (por eso una imagen de Linux no corre "nativa" en macOS — Docker Desktop lleva una VM de Linux escondida, y por eso el rendimiento de red/disco difiere entre tu portátil y producción), y el aislamiento es de kernel, no de hardware — más ligero pero menos hermético que una VM, lo que explica por qué los proveedores cloud no mezclan contenedores de clientes distintos sin una capa extra de aislamiento.

### El viaje completo de un paquete hasta el pod

El ejercicio mental que une los bloques 5, 11 y este: una petición HTTPS de un usuario hasta tu código en Kubernetes, salto a salto —

1. **DNS público** resuelve `api.tuapp.com` a la IP del balanceador cloud (bloque 5).
2. El **load balancer del proveedor** (que ya hace *health checks* y quizá termina TLS — bloque 9) reenvía a un nodo del clúster.
3. El **Ingress controller** (un proxy tipo nginx corriendo como pod) mira host y ruta HTTP y elige el **Service** interno.
4. El Service no es un proceso: son **reglas de reescritura de destino programadas en el kernel de cada nodo** (iptables/IPVS — NAT del bloque 5, otra vez) que sustituyen la IP virtual del Service por la IP real de un pod concreto, elegido entre los sanos.
5. La red del clúster (**CNI**) entrega el paquete al pod, esté en el nodo que esté; si hay **service mesh**, el sidecar lo intercepta antes (mTLS, métricas) y lo pasa, por fin, a tu proceso.

Cada tecnología de moda de esta lista es un concepto viejo de telecom con uniforme nuevo: DNS, NAT, proxies, health checks, enrutamiento. Cuando algo falla "en la red de Kubernetes", esta lista es literalmente tu checklist de depuración: ¿en cuál de los cinco saltos muere el paquete?

### Network policies: el firewall vuelve, declarativo

Por defecto, en Kubernetes **todo pod puede hablar con todo pod** — una red plana. Cómodo para empezar, incómodo cuando piensas en el bloque 9: un atacante que compromete el pod del frontend puede intentar conectarse directamente a la base de datos. Las **NetworkPolicies** son el firewall de este mundo, con dos giros: son **declarativas** ("los pods con etiqueta `app=db` solo aceptan tráfico de pods `app=api` al puerto 5432") y siguen a los pods automáticamente aunque cambien de IP o de nodo — reglas sobre *identidades lógicas*, no sobre direcciones, porque en este entorno las direcciones son efímeras por diseño. El principio subyacente es el mínimo privilegio del bloque 15, aplicado al tráfico: la pregunta de diseño no es "¿qué bloqueo?" sino "¿qué conversaciones tienen motivo legítimo para existir?".

## Ejercicio práctico

Con Docker en tu máquina, 10 minutos de ver-para-creer:

1. `docker network create demo` y arranca dos contenedores en ella: `docker run -d --name web --network demo nginx` y `docker run -it --rm --network demo alpine sh`.
2. Desde el alpine: `ping web` — funciona **por nombre**: acabas de usar el DNS interno de Docker (el análogo en miniatura de los Services de Kubernetes). Luego `wget -qO- http://web` — servido por nginx, de contenedor a contenedor, sin haber publicado ningún puerto al exterior.
3. `docker network inspect demo`: ahí está la subred privada y la IP asignada a cada contenedor — la VPC de juguete de tu portátil (bloque 15).
4. Remate conceptual: `docker exec web ip addr` y compara con `ip addr` (o `ifconfig`) en tu host — dos vistas de red completamente distintas sobre el mismo kernel. Eso son los network namespaces.

## Autoevaluación

1. "Un contenedor es una VM ligera" — desmonta la frase: ¿qué comparte con el host que una VM no comparte, y qué dos mecanismos del kernel lo hacen posible?
2. Recorre de memoria los cinco saltos de una petición externa hasta un pod. ¿En cuál actúa NAT? ¿En cuál se decide el balanceo?
3. ¿Por qué un Service de Kubernetes puede repartir tráfico sin ser un proceso por el que pasen los paquetes?
4. ¿Por qué las NetworkPolicies se definen sobre etiquetas y no sobre IPs, y qué principio del bloque 15 implementan?

---

## Continúa el recorrido

Antes de avanzar, intenta responder la autoevaluación sin mirar y registra la práctica en [PROGRESO.md](PROGRESO.md). Si puedes explicar la idea central, dibujarla y conectarla con un bloque anterior, continúa.

[← 20. Tema anterior](12-sistemas-distribuidos.md) · [Orden del curso](ORDEN-DE-ESTUDIO.md) · [22. Tema siguiente →](14-observabilidad-rendimiento.md)
