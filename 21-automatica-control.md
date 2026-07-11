# Bloque 21 — Automática y control

Controlar es medir un sistema, comparar su estado con un objetivo y actuar para reducir la diferencia. Esa idea gobierna desde un termostato hasta la potencia de un móvil o el reloj de un receptor.

## Planta, referencia, sensor y actuador

La **planta** es aquello que queremos controlar. La referencia es el objetivo; el sensor estima el estado; el controlador decide; el actuador modifica la planta. La diferencia entre referencia y medida es el error.

Un buen diagrama separa señales, componentes y perturbaciones externas. Si el sensor mide tarde o mal, el controlador actúa sobre una versión incompleta de la realidad.

## Lazo abierto y lazo cerrado

En lazo abierto se actúa sin observar el resultado: es simple, pero no corrige cambios. En lazo cerrado la salida realimenta la decisión. Esto rechaza perturbaciones y compensa incertidumbre, aunque una realimentación mal ajustada puede provocar oscilación o inestabilidad.

La realimentación no pertenece solo a máquinas: TCP ajusta su ritmo según congestión observada y un sistema cloud autoescala según métricas.

## Respuesta temporal y estabilidad

Un escalón —cambiar bruscamente el objetivo— revela rapidez, sobreimpulso, oscilación y error final. Estable significa que una entrada limitada no hace crecer la salida sin control. Un sistema puede ser estable pero demasiado lento, o rápido pero frágil ante ruido y retrasos.

Los polos condensan la dinámica de un modelo lineal. Su posición indica si los modos decaen, oscilan o crecen; no son componentes físicos, sino propiedades del modelo.

## Control proporcional, integral y derivativo

La acción proporcional responde al error presente. La integral acumula error y elimina desviaciones persistentes, pero puede excederse. La derivativa anticipa la tendencia, aunque amplifica ruido. Un PID combina las tres; ajustarlo es equilibrar rapidez, precisión, ruido y robustez.

## Muestreo, retraso y control digital

Un controlador digital observa y actúa en instantes discretos. Si muestrea demasiado lento pierde dinámica; si hay retrasos, corrige un estado que ya cambió. Red, cómputo y jitter forman parte del lazo y pueden volver inestable un control distribuido.

## Profundización

### PLL y sincronización

Un lazo de enganche de fase compara la fase de una referencia con la de un oscilador y corrige este último. Recuperación de reloj, síntesis de frecuencia y demodulación dependen de variantes de esta estructura.

### Control de potencia en radio

Un terminal ajusta potencia para llegar con calidad suficiente sin desperdiciar batería ni interferir más de lo necesario. El canal cambia, la medida llega con retraso y otros usuarios comparten el medio: es un problema de control además de radio.

### Observabilidad y controlabilidad

Un estado es observable si puede inferirse a partir de medidas; controlable si los actuadores pueden llevarlo donde interesa. Ningún algoritmo corrige una variable que no se puede observar o influir.

## Ejercicio práctico

Simula manualmente un termostato en una hoja de cálculo: temperatura, objetivo, pérdida térmica y acción de calefacción. Compara control todo/nada con una acción proporcional. Añade dos pasos de retraso a la medida y observa cuándo aparece oscilación.

## Autoevaluación

1. ¿Qué información falta en un sistema de lazo abierto?
2. ¿Cómo puede la realimentación mejorar y empeorar un sistema?
3. ¿Qué revela una respuesta a escalón?
4. ¿Qué aporta cada término de un PID?
5. Da un ejemplo de control dentro de un sistema de comunicaciones.

---

**Navegación:** [⬅️ Bloque 20 — Programación y arquitectura de computadores](20-programacion-computadores.md) · [🏠 Índice](README.md) · [Bloque 22 — Audio, imagen y comunicaciones multimedia ➡️](22-audio-imagen-multimedia.md)
