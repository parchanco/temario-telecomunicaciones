# Bloque 2 — Teoría de la comunicación

## El modelo básico de Shannon

Claude Shannon, en 1948, propuso un esquema que sigue siendo la base de todo sistema de comunicación moderno:

```
Fuente → Codificador → Canal (con ruido) → Decodificador → Destino
```

- La **fuente** genera información (voz, datos, vídeo).
- El **codificador** la transforma para transmitirla eficientemente y protegerla de errores.
- El **canal** es el medio físico (cable, aire, fibra) — y siempre añade ruido.
- El **decodificador** deshace la transformación e intenta recuperar la información original a pesar del ruido.
- El **destino** recibe (idealmente) lo mismo que generó la fuente.

Lo importante de este modelo no es memorizar las cajas, sino la idea revolucionaria detrás: Shannon demostró que **se puede transmitir información con una tasa de error tan baja como se quiera, siempre que no se supere un límite llamado capacidad del canal** — sin necesidad de "gritar más fuerte", solo codificando mejor. Antes de Shannon se pensaba que el ruido imponía un límite de calidad; él demostró que impone un límite de *velocidad*, no de fiabilidad.

## Capacidad de canal (idea, sin fórmula)

La **capacidad de un canal** es la máxima cantidad de información (bits por segundo) que se puede transmitir por él con una probabilidad de error tan pequeña como se quiera. Depende de dos cosas: el **ancho de banda** disponible (bloque 1) y el **SNR** (bloque 1).

La intuición: más ancho de banda es como tener más "carriles" para mandar información; más SNR es como tener una carretera más limpia donde se distinguen mejor los "coches" (símbolos) del "polvo" (ruido). Ambos factores, combinados, ponen un techo teórico a la velocidad de cualquier comunicación — techo que la ingeniería moderna se ha ido acercando cada vez más (los códigos LDPC y turbo-códigos actuales operan muy cerca de ese límite teórico).

## Modulación analógica: AM, FM, PM

**Modular** significa "montar" la información que quieres transmitir sobre una onda de alta frecuencia (la portadora), que es la que realmente viaja bien por el aire o el cable. La pregunta es: ¿qué característica de la onda portadora cambio para representar la información?

- **AM (Amplitud Modulada)**: varío la **amplitud** (la "altura") de la onda según la señal. Sencilla de generar y demodular, pero muy sensible al ruido (el ruido también afecta a la amplitud, así que se mezcla directamente con la señal).
- **FM (Frecuencia Modulada)**: varío la **frecuencia** de la onda. Mucho más resistente al ruido, porque la mayoría del ruido afecta a la amplitud y un receptor de FM puede ignorarla; por eso la radio FM suena más limpia que la AM.
- **PM (Fase Modulada)**: varío la **fase** de la onda. Es prima hermana de la FM en comportamiento, y es la base conceptual de casi toda la modulación digital moderna (ver PSK más abajo).

No hace falta saber derivar sus espectros: lo importante es la idea de que **cada una sacrifica algo distinto** (AM es simple pero ruidosa, FM es robusta pero ocupa más ancho de banda) y que esa disyuntiva — robustez frente a eficiencia espectral — reaparece constantemente en comunicaciones.

## Modulación digital: ASK, FSK, PSK, QAM y las constelaciones

Cuando la información a transmitir es digital (bits), la lógica es la misma que en analógico pero discretizada: se cambia amplitud, frecuencia o fase, pero solo entre un conjunto finito de valores posibles, cada uno representando un patrón de bits.

- **ASK** (Amplitude Shift Keying): distintos niveles de amplitud representan distintos símbolos.
- **FSK** (Frequency Shift Keying): distintas frecuencias representan distintos símbolos (el módem antiguo sonaba así).
- **PSK** (Phase Shift Keying): distintas fases representan distintos símbolos. Muy usado porque es robusto.
- **QAM** (Quadrature Amplitude Modulation): combina amplitud y fase a la vez, permitiendo representar muchos más símbolos distintos (16-QAM, 64-QAM, 256-QAM...).

La forma habitual de visualizar esto es la **constelación**: un diagrama en dos ejes donde cada punto representa un símbolo posible (una combinación de amplitud y fase). Cuantos más puntos tiene la constelación, **más bits transporta cada símbolo** — pero los puntos están más juntos entre sí, así que hace falta menos ruido para que el receptor confunda un punto con el vecino y cometa un error.

Esta es la disyuntiva central de toda la modulación digital: **velocidad vs robustez**. Las redes modernas (WiFi, 4G/5G) son "adaptativas": si tienes buena señal, usan una constelación densa (256-QAM, mucha velocidad); si la señal empeora, bajan a una constelación más simple (QPSK, menos velocidad pero más fiable) automáticamente.

## Multiplexación: compartir el medio

Casi nunca hay un solo usuario para un canal — hay que repartirlo entre muchos. Multiplexar es la técnica de meter varias señales en un mismo medio físico sin que se interfieran:

- **FDM (Frequency Division Multiplexing)**: a cada usuario se le asigna una porción distinta del espectro de frecuencias, y transmiten todos a la vez, cada uno en "su" franja. Es como varias emisoras de radio emitiendo simultáneamente, cada una en su frecuencia.
- **TDM (Time Division Multiplexing)**: todos los usuarios comparten toda la banda de frecuencia, pero se turnan en el tiempo — a cada uno se le asigna una "rebanada" de tiempo muy corta, y van rotando tan rápido que parece simultáneo.
- **CDM (Code Division Multiplexing)**: todos transmiten a la vez en la misma frecuencia y en el mismo tiempo, pero cada uno usa un "código" matemático distinto que permite separarlos después (es la base de CDMA, usado en 3G). La analogía clásica: es como una fiesta donde todos hablan a la vez pero en idiomas distintos — puedes "sintonizar" tu oído al idioma que entiendes e ignorar el resto como ruido de fondo.

## Codificación de canal (FEC): corregir errores sin retransmitir

La **codificación de canal** o **FEC (Forward Error Correction)** consiste en añadir información redundante calculada a partir de los datos originales, de forma que el receptor pueda **detectar e incluso corregir** errores sin tener que pedir que se reenvíe nada.

La analogía más simple: si te dicto un número de teléfono y lo repito dos veces, y en la segunda vez oyes un dígito distinto, sabes que hubo un error (detección) pero no sabes cuál de las dos veces es la correcta. Si en cambio te lo dicto de una forma más inteligente —por ejemplo, añadiendo un dígito de "suma de comprobación"— puedes no solo detectar el error sino, con suficiente redundancia bien diseñada, deducir cuál era el valor correcto (corrección).

Esta idea es la que permite que un CD raspado siga sonando bien, que un enlace de wifi con interferencia siga funcionando, o que las sondas espaciales manden fotos nítidas desde miles de millones de kilómetros con una señal muy débil: la redundancia bien diseñada compensa el ruido del canal, al precio de "gastar" parte del ancho de banda en esa redundancia en lugar de en datos útiles nuevos.

---

## Profundización

### Entropía: qué es exactamente "información"

La aportación más profunda de Shannon no fue el modelo de bloques sino definir **cuánta información contiene un mensaje**: la información es, esencialmente, **sorpresa**. Un mensaje que ya sabías que iba a llegar ("mañana saldrá el sol") no aporta información aunque ocupe muchas letras; uno improbable aporta mucha. La **entropía** de una fuente mide su sorpresa promedio, y marca el límite de cuánto se puede comprimir sin pérdida: un texto en español se puede comprimir mucho porque es muy predecible (después de "q" casi siempre viene "u" — poca sorpresa, poca información real por letra); un archivo ya comprimido o cifrado no se puede comprimir más porque parece ruido aleatorio (máxima sorpresa por bit).

Esto une dos mundos que parecían separados: la **compresión** (bloque 8) intenta quitar toda la redundancia natural del mensaje para acercarlo a su entropía, y la **codificación de canal (FEC)** añade después redundancia *artificial y controlada* para protegerlo. Comprimir y proteger son las dos caras de la misma moneda: gestionar cuánta redundancia hay y de qué tipo.

### OFDM: la modulación que domina el mundo actual

Merece sección propia porque es la base de WiFi, 4G, 5G y la TDT (bloque 16). En lugar de transmitir un único flujo muy rápido sobre una portadora ancha, **OFDM (Orthogonal Frequency Division Multiplexing)** divide el canal en cientos o miles de **subportadoras estrechas y lentas** que transmiten en paralelo.

¿Por qué es mejor muchos flujos lentos que uno rápido? Por el multitrayecto (bloque 4): cuando la señal llega por varios caminos con retardos distintos, un símbolo muy corto (flujo rápido) se "pisa" con el eco del símbolo anterior. Si cada subportadora transmite símbolos largos y lentos, el eco es una fracción pequeña del símbolo y se puede ignorar con un margen de guarda. Además, OFDM permite ser adaptativo por subportadora: si una zona del espectro está sucia (interferencia justo ahí), se cargan menos bits en esas subportadoras y más en las limpias — como un camión que reparte el peso según el estado de cada eje.

### La cadena completa: en qué orden se aplica todo

Con las piezas de este bloque ya se puede montar la cadena real de cualquier transmisor moderno, que es un buen resumen mental:

```
bits → compresión (quitar redundancia natural) → cifrado (bloque 9)
     → FEC (añadir redundancia controlada) → modulación (bits a símbolos)
     → multiplexación (compartir el medio) → antena
```

El receptor la deshace en orden inverso. Cada asignatura de la carrera profundiza en una caja de esta cadena; tener la cadena en la cabeza evita perderse.

## Ejercicio práctico

Diseña en papel (o en 20 líneas de código, si te apetece) un **código de repetición**: cada bit se transmite 3 veces y el receptor decide por mayoría. Luego responde:

1. Si el canal corrompe un bit de cada 10 al azar, ¿qué probabilidad hay de que un bit decodificado por mayoría sea erróneo? (Necesita que fallen 2 de 3 — calcúlalo o simúlalo con `Math.random()`.)
2. ¿Cuánto ancho de banda útil has sacrificado? (Pista: transmites 3× para 1× de datos.)
3. Verás que triplicar el coste solo mejora el error moderadamente — los códigos reales (Hamming, LDPC) consiguen mucho más gastando mucho menos, y ahora entiendes exactamente qué problema resuelven mejor que tu versión ingenua.

## Autoevaluación

1. ¿Por qué un archivo ZIP no se puede volver a comprimir? Explícalo usando la palabra "sorpresa".
2. ¿Qué demostró Shannon que se creía imposible antes de 1948?
3. En una constelación 256-QAM frente a QPSK: ¿qué ganas, qué pierdes, y por qué tu WiFi cambia entre ellas sin preguntarte?
4. ¿Por qué OFDM tolera el multitrayecto mejor que una portadora única rápida?
