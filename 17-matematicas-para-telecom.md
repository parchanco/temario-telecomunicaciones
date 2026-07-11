# Bloque 17 — Matemáticas para telecomunicaciones

Las matemáticas no son un peaje previo a la ingeniería: son un lenguaje para comprimir relaciones. Este bloque no busca calcular más, sino reconocer qué herramienta describe cada fenómeno.

## Modelar: decidir qué ignorar

Un modelo conserva lo importante para una pregunta y descarta el resto. Un cable puede ser una línea sin grosor al dibujar una red, una resistencia al estudiar pérdidas o un sistema distribuido cuando su longitud es comparable con la longitud de onda. Ningún modelo es “el verdadero”; es útil dentro de unas condiciones.

Antes de usar una ecuación pregunta: ¿qué representa cada variable?, ¿qué se supone constante?, ¿en qué rango funciona? Las unidades son la primera prueba de coherencia: no se pueden sumar segundos y metros, y ambos lados de una igualdad deben tener dimensiones compatibles.

## Números complejos: amplitud y fase en una sola pieza

Una señal sinusoidal tiene amplitud y fase. Los números complejos permiten tratarlas juntas y convierten muchos desplazamientos y oscilaciones en multiplicaciones sencillas. En circuitos de corriente alterna aparecen como impedancias; en radio, como componentes I/Q; en Fourier, como coeficientes de frecuencia.

La parte imaginaria no es una magnitud “irreal”: es un segundo eje matemático que mantiene información de fase que se perdería usando solo un número real.

## Derivadas, integrales y ecuaciones diferenciales

La derivada mide cambio local: pendiente, velocidad, rapidez con la que varía una tensión. La integral acumula: carga eléctrica, energía, área bajo una señal. Una ecuación diferencial relaciona el estado de un sistema con su cambio y describe memoria: un condensador no puede cambiar su tensión instantáneamente y una bobina no puede cambiar su corriente de golpe.

En ingeniería suele importar más interpretar una solución —si crece, oscila, se amortigua o se estabiliza— que resolverla a mano.

## Álgebra lineal: combinar y separar señales

Vectores y matrices organizan muchas cantidades relacionadas. Una transformación lineal mezcla entradas para producir salidas; cambiar de base es mirar los mismos datos desde ejes más útiles. Fourier puede entenderse como proyectar una señal sobre una familia de ondas. MIMO usa matrices para separar señales que llegan mezcladas a varias antenas.

Los autovectores representan direcciones que una transformación no gira, solo escala. Aparecen en modos de propagación, estabilidad, compresión y análisis de redes.

## Probabilidad: razonar sobre lo que no controlamos

Ruido, errores y tráfico no se predicen muestra a muestra, pero sí mediante distribuciones. La media describe el centro; la varianza, la dispersión; una probabilidad condicionada actualiza lo que esperamos cuando llega información nueva.

Correlación no implica causalidad: indica que dos variables cambian de forma relacionada. En procesado de señal también sirve para localizar un patrón conocido dentro de datos ruidosos.

## Profundización

### Logaritmos y decibelios

El logaritmo convierte productos en sumas y comprime rangos enormes. Por eso ganancias y pérdidas de una cadena se suman en dB. Un dB expresa una relación, no una potencia absoluta; dBm sí fija una referencia de 1 mW.

### Fourier y Laplace como cambios de perspectiva

Fourier separa una señal en frecuencias. Laplace añade información sobre crecimiento y amortiguamiento, útil para transitorios y estabilidad. No crean información: reorganizan la misma señal para que ciertas relaciones sean visibles.

### Orden de magnitud y estimación

Una estimación aproximada detecta resultados imposibles antes que una calculadora. Aprende prefijos —mili, micro, nano, kilo, mega, giga— y acostúmbrate a preguntar si el resultado está en el orden de magnitud esperado.

## Ejercicio práctico

Elige una conexión WiFi y crea una tabla con frecuencia, longitud de onda aproximada, potencia recibida en dBm y velocidad observada. No busques precisión de laboratorio: anota supuestos, convierte unidades y explica por qué potencia y velocidad no tienen una relación uno a uno.

## Autoevaluación

1. ¿Por qué un mismo cable necesita modelos distintos según la pregunta?
2. ¿Qué información añade la fase a la amplitud de una señal?
3. Da un ejemplo físico de derivada y otro de integral.
4. ¿Por qué una distribución puede ser útil aunque no prediga la próxima muestra de ruido?
5. ¿Qué ventaja práctica aporta expresar ganancias y pérdidas en decibelios?

---

**Navegación:** [⬅️ Bloque 16 — Sistemas de telecomunicación del mundo real: VoIP, GPS y radiodifusión](16-sistemas-mundo-real.md) · [🏠 Índice](README.md) · [Bloque 18 — Circuitos y electrónica ➡️](18-circuitos-electronica.md)
