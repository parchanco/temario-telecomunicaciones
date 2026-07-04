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
