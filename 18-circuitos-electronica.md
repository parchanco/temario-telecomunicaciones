# Bloque 18 — Circuitos y electrónica

Un sistema de telecomunicación termina tocando materia: sensores, fuentes, amplificadores, conversores y cables. Este bloque construye el puente conceptual entre una señal abstracta y el circuito que la produce o mide.

## Tensión, corriente, potencia y energía

La tensión expresa diferencia de potencial; la corriente, flujo de carga. No son dos fluidos independientes: el circuito y sus componentes relacionan ambas. La potencia indica la rapidez con la que se entrega o disipa energía. Un componente puede soportar la tensión y aun quemarse por exceso de potencia.

La referencia de masa es el cero elegido para medir tensiones. No siempre equivale a tierra física, y confundirlas causa muchos errores de medida.

## Resistencia e impedancia

La resistencia relaciona tensión y corriente y disipa energía. En señales variables, condensadores y bobinas almacenan y devuelven energía; su oposición depende de la frecuencia. La **impedancia** reúne resistencia y desfase en una cantidad compleja.

Adaptar impedancias permite transferir potencia y evitar reflexiones. En baja frecuencia un cable puede parecer ideal; en radio se comporta como línea de transmisión y una mala terminación devuelve parte de la onda.

## Componentes pasivos y filtros

Resistencias, condensadores y bobinas permiten dividir tensión, suavizar una alimentación, bloquear continua o seleccionar frecuencias. Un filtro no reconoce “ruido” por intención: atenúa regiones de frecuencia. Si señal y ruido se solapan, filtrarlos perfectamente es imposible.

## Diodos y transistores

Un diodo conduce principalmente en una dirección y permite rectificar, limitar o detectar señales. Un transistor usa una señal pequeña para controlar una mayor. Puede trabajar como interruptor —base de la lógica digital— o en una región aproximadamente lineal —base de la amplificación analógica—.

La electrónica real impone límites: ruido, velocidad, consumo, calentamiento y no linealidad. El componente ideal es un modelo de primera aproximación.

## Conversión entre mundo analógico y digital

Un ADC muestrea y cuantiza una tensión; un DAC reconstruye niveles analógicos a partir de números. La resolución en bits determina cuántos niveles existen, pero no garantiza precisión: referencia, ruido, linealidad y frecuencia de muestreo también importan.

El acondicionamiento previo —amplificar, desplazar y filtrar— hace que la señal aproveche el rango del conversor sin saturarlo.

## Profundización

### Realimentación en amplificadores

Devolver parte de la salida a la entrada puede estabilizar la ganancia y reducir distorsión a cambio de ganancia disponible. La misma idea reaparece en control automático y en lazos de sincronización.

### Integridad de alimentación y señal

Un circuito digital consume corriente en pulsos rápidos. Condensadores de desacoplo cercanos entregan esos pulsos y reducen variaciones de alimentación. Pistas largas, retornos mal diseñados y conectores también forman parte del circuito.

### Medir cambia el sistema

Toda sonda añade impedancia, ruido y ancho de banda limitado. Una medida fiable incluye qué instrumento se usó, dónde se conectó y qué parte de la señal podría no estar viendo.

## Ejercicio práctico

Usa un simulador de circuitos online o local para montar un filtro RC paso bajo. Aplica una señal cuadrada, cambia R o C y observa cómo se redondean los flancos. Explica el resultado en tiempo y en frecuencia sin derivar la función de transferencia.

## Autoevaluación

1. ¿En qué se diferencian energía y potencia?
2. ¿Por qué la impedancia depende de la frecuencia?
3. ¿Cuándo trabaja un transistor como interruptor y cuándo como amplificador?
4. ¿Por qué más bits en un ADC no garantizan una medida correcta?
5. ¿Cómo puede una sonda alterar aquello que intenta medir?
