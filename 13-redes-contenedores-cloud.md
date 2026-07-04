# Bloque 13 — Redes en contenedores y cloud

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
