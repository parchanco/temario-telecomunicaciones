# Bloque 9 — Seguridad en comunicaciones

## Cifrado simétrico vs asimétrico

El **cifrado** transforma un mensaje legible en algo ininteligible para quien no tenga la clave adecuada, y permite revertir esa transformación solo a quien sí la tenga. Hay dos grandes familias, que resuelven problemas distintos:

- **Cifrado simétrico**: se usa **la misma clave** para cifrar y para descifrar. Es rápido y eficiente computacionalmente, ideal para cifrar grandes cantidades de datos (por ejemplo, todo el tráfico de una videollamada). El problema que no resuelve por sí solo: ¿cómo se ponen de acuerdo emisor y receptor en esa clave compartida sin que alguien la intercepte por el camino, si nunca se han visto en persona? Si mandas la clave por el mismo canal inseguro que quieres proteger, cualquiera que esté escuchando la captura también.
- **Cifrado asimétrico** (de clave pública): usa **dos claves distintas pero matemáticamente relacionadas** — una **pública**, que puedes compartir libremente con cualquiera, y una **privada**, que nunca sale de tu poder. Lo que se cifra con la pública solo se puede descifrar con la privada correspondiente (y en ciertos usos, al revés, lo que permite firmar digitalmente). Esto resuelve el problema anterior: cualquiera puede mandarte un mensaje cifrado usando tu clave pública, sin haber acordado nada en secreto de antemano, y solo tú (con tu clave privada) puedes leerlo. El coste es que computacionalmente es mucho más pesado que el cifrado simétrico.

En la práctica, casi ningún sistema usa uno solo de forma pura: se combinan. Por ejemplo, HTTPS usa cifrado asimétrico únicamente al principio de la conexión, para que las dos partes se pongan de acuerdo de forma segura en una clave simétrica temporal (de sesión) — y a partir de ahí, todo el tráfico real (que es mucho volumen) se cifra con esa clave simétrica, mucho más rápida. Es lo mejor de los dos mundos: la seguridad del intercambio inicial del asimétrico, y la velocidad del simétrico para el grueso de los datos.

No hace falta saber las matemáticas detrás de RSA o de curvas elípticas — lo importante es tener clarísima esta distinción de propósito: **simétrico = rápido pero necesita un secreto ya compartido; asimétrico = resuelve cómo compartir ese secreto de forma segura sin haberse visto antes, a costa de velocidad**.

## VPN, firewall y certificados TLS: qué protege cada uno

Tres piezas que se mencionan constantemente y que protegen cosas distintas:

- **VPN (Virtual Private Network)**: crea un **túnel cifrado** entre tu dispositivo y un punto de salida (un servidor VPN), de forma que todo tu tráfico viaja protegido dentro de ese túnel mientras atraviesa redes que no son de confianza (por ejemplo, el wifi de un aeropuerto). Protege **la confidencialidad del tráfico frente a quien esté en el camino** (tu operador de red local, alguien en la misma red wifi), pero no te hace anónimo frente al destino final ni te protege si el propio servidor VPN es malicioso o si el sitio al que te conectas ya sabe quién eres.
- **Firewall (cortafuegos)**: es un filtro que decide **qué tráfico entra y sale** de una red o de un dispositivo, según reglas (por ejemplo, "bloquear cualquier conexión entrante que no sea a los puertos 80 y 443"). Protege frente a conexiones no deseadas o no autorizadas, pero no cifra nada ni protege el contenido de las comunicaciones permitidas.
- **Certificado TLS**: es un documento digital, emitido por una entidad de confianza (una Autoridad de Certificación), que vincula una clave pública con la identidad de un dominio o servidor concreto. Cuando tu navegador se conecta a un sitio con HTTPS, comprueba ese certificado para asegurarse de **que realmente está hablando con quien dice ser** (autenticidad) antes de establecer el cifrado — sin esto, sería trivial para alguien hacerse pasar por tu banco interceptando la conexión (ataque de intermediario o "man-in-the-middle").

La distinción que conviene interiorizar: **VPN protege el "camino" del tráfico; firewall decide "quién puede entrar/salir"; TLS/certificados garantizan "con quién realmente estás hablando" y cifran ese contenido específico**. Un sistema seguro real normalmente combina piezas de los tres tipos, cada una cubriendo una parte distinta del problema.

## Vectores de ataque típicos en redes

Algunos de los ataques más comunes, entendidos a nivel conceptual:

- **Sniffing (escucha)**: alguien con acceso a un segmento de la red captura el tráfico que pasa por ella, buscando información sensible. Es especialmente peligroso en tráfico no cifrado (de ahí la importancia de HTTPS/TLS frente a HTTP a secas) — es literalmente "escuchar una conversación ajena" si esa conversación no va protegida.
- **Spoofing (suplantación)**: un atacante falsifica el origen de un mensaje o conexión para hacerse pasar por otra entidad (otra dirección IP, otro dispositivo, otro remitente de correo) — el equivalente a falsificar el remitente de una carta para que el destinatario confíe en ella sin verificar.
- **DDoS (Distributed Denial of Service, denegación de servicio distribuida)**: en vez de intentar robar información, el objetivo es **saturar** un servicio con tantas peticiones (a menudo desde miles de dispositivos comprometidos a la vez) que deja de poder atender a los usuarios legítimos — es como bloquear la entrada de una tienda con una multitud de gente que no compra nada, para que los clientes reales no puedan entrar.

La idea de fondo que une a los tres: en redes, casi ningún atacante necesita "romper" la criptografía matemáticamente — es mucho más habitual explotar la ausencia de cifrado (sniffing), la ausencia de verificación de identidad (spoofing), o simplemente la limitación física de capacidad de un sistema (DDoS). Por eso las defensas prácticas (cifrado por defecto, autenticación mutua, límites de tasa y filtrado) atacan precisamente esos tres puntos.

---

## Profundización

### Hashing: la tercera pata que faltaba

Junto al cifrado simétrico y asimétrico hay una tercera primitiva, distinta de ambas: el **hash criptográfico** (SHA-256 y familia). Un hash convierte cualquier entrada en una "huella" de tamaño fijo, con dos propiedades clave: es **irreversible** (de la huella no se puede reconstruir la entrada — no es cifrado, no hay clave ni "descifrado" posible) y **cualquier cambio mínimo en la entrada produce una huella completamente distinta**.

Para qué sirve cada primitiva, en una línea: **cifrar = confidencialidad** (que no lo lean), **hash = integridad** (detectar si algo cambió), **firmar = autenticidad** (probar quién lo emitió). Los usos del hash que te cruzas a diario: verificar que una descarga no se corrompió, los identificadores de commit de git (cada commit *es* el hash de su contenido — por eso no se puede reescribir historia sin que se note), y el almacenamiento de contraseñas: un servicio bien hecho **nunca guarda tu contraseña**, guarda su hash (reforzado con *salt* — un valor aleatorio por usuario que impide atacar todas las cuentas a la vez con tablas precalculadas — y con funciones deliberadamente lentas como bcrypt para encarecer la fuerza bruta). Cuando una web "te recuerda tu contraseña" en texto, ya sabes que la guarda mal.

### Firmas digitales y la cadena de confianza completa

Con hash + cifrado asimétrico se construye la **firma digital**: se calcula el hash del documento y se cifra **con la clave privada** (al revés del uso normal). Cualquiera puede descifrar con la clave pública y comparar hashes: si coinciden, el documento no cambió **y** solo el dueño de la privada pudo firmarlo. Autenticidad e integridad de un golpe.

Esto cierra el hueco que quedaba en la explicación de los certificados TLS: un certificado es la clave pública de un sitio **firmada por una Autoridad de Certificación (CA)**, cuya clave pública viene a su vez firmada en una cadena que termina en un pequeño conjunto de **certificados raíz preinstalados** en tu sistema operativo y navegador. Toda la seguridad de la web descansa, en última instancia, en esa lista de raíces y en que las CAs solo firmen a quien demuestre controlar el dominio — por eso el compromiso de una CA es un incidente global, y por eso existe *certificate transparency* (registros públicos y auditables de cada certificado emitido, donde un dominio puede detectar que alguien emitió certificados suyos sin permiso).

### Cifrado de extremo a extremo: dónde termina TLS

Distinción importante que TLS no cubre: TLS protege el tramo **cliente-servidor**, pero el servidor ve el contenido en claro. En WhatsApp o Signal, el **cifrado de extremo a extremo (E2E)** va más allá: las claves se generan y viven **solo en los dispositivos de los extremos**, y el servidor transporta únicamente material cifrado que no puede leer. La consecuencia estructural: el proveedor *no puede* entregar el contenido de las conversaciones ni aunque quiera o le obliguen — no por política, sino por diseño (aunque los **metadatos** — quién habla con quién, cuándo, cuánto — normalmente siguen siendo visibles, y son más reveladores de lo que parece). Todo el debate político recurrente sobre "puertas traseras" en mensajería es exactamente sobre esto: no se puede debilitar E2E "solo para los malos", porque la matemática es la misma para todos.

## Ejercicio práctico

1. **Inspecciona una cadena de confianza real**: en tu navegador, haz clic en el candado de cualquier web HTTPS → ver certificado. Recorre la cadena completa (sitio → CA intermedia → raíz), mira las fechas de validez y localiza esa raíz en el almacén de confianza de tu sistema. O desde terminal: `openssl s_client -connect github.com:443 -showcerts | head -40`.
2. **Toca un hash**: `echo "hola" | shasum -a 256` y luego `echo "holA" | shasum -a 256` — un bit de diferencia en la entrada, huella irreconociblemente distinta. Es la propiedad sobre la que se apoya todo lo anterior (y git, que usas a diario).
3. **Ve el sniffing con tus ojos**: con Wireshark capturando, visita una web HTTP plana (queda alguna, p. ej. `http://neverssl.com`) y busca el contenido legible en los paquetes; repite con una HTTPS y compara. Cinco minutos que valen por todo el argumento de "cifrado por defecto".

## Autoevaluación

1. Completa el mapa: confidencialidad, integridad, autenticidad — ¿qué primitiva resuelve cada una y por qué no son intercambiables?
2. ¿Por qué un servicio nunca debe poder decirte tu contraseña? ¿Qué son el salt y la lentitud deliberada, y qué ataque frustra cada uno?
3. Traza la cadena completa de por qué tu navegador confía en tu banco, empezando por "hay una lista preinstalada en mi sistema operativo".
4. ¿Qué puede leer el servidor de WhatsApp de tus mensajes, y qué puede leer tu operador de red? Distingue contenido y metadatos.

---

**Navegación:** [⬅️ Bloque 8 — Procesado digital de señal (a nivel de idea)](08-procesado-senal.md) · [🏠 Índice](README.md) · [Bloque 10 — Tendencias actuales ➡️](10-tendencias.md)
