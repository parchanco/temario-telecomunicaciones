# Bloque 19 — Electromagnetismo y líneas de transmisión

Las comunicaciones por cobre, fibra y radio parecen tecnologías distintas, pero todas transportan energía e información mediante campos electromagnéticos.

## Campos eléctricos y magnéticos

Una carga crea un campo eléctrico; una corriente, un campo magnético. Cuando los campos cambian en el tiempo se influyen mutuamente y pueden propagarse como una onda. Las ecuaciones de Maxwell formalizan esta relación; aquí importa la consecuencia: una perturbación puede transportar energía sin transportar materia de un extremo al otro.

El medio modifica velocidad, pérdidas y dirección. En el vacío la onda viaja a la velocidad de la luz; en cable o fibra lo hace más despacio.

## Frecuencia, longitud de onda y velocidad

Velocidad, frecuencia y longitud de onda están ligadas. Al aumentar la frecuencia, la longitud de onda disminuye. Esto determina el tamaño práctico de antenas, el comportamiento de obstáculos y cuándo una pista de circuito deja de ser un simple conductor y pasa a ser una línea de transmisión.

La frecuencia no hace que la información “viaje más rápido”; cambia cuántos ciclos ocurren por segundo y qué ancho de banda puede asignarse alrededor de una portadora.

## Propagación, polarización y potencia

La orientación del campo eléctrico define la polarización. Emisor y receptor mal alineados pierden potencia. Reflexión, difracción y dispersión crean múltiples caminos; las copias pueden sumarse o cancelarse según su fase.

La densidad de potencia disminuye al repartirse la energía sobre una superficie mayor. Ganancia de antena no crea energía: la concentra en unas direcciones a costa de otras.

## Líneas de transmisión y reflexiones

Cuando el tiempo de propagación por un conductor ya no es despreciable, tensión y corriente dependen de la posición. El cable tiene una impedancia característica. Si la carga no coincide con ella, parte de la onda se refleja hacia la fuente.

El coeficiente de reflexión y la relación de onda estacionaria cuantifican ese desajuste. Esta es la misma intuición que explica ecos en acústica y reflexiones en interfaces ópticas.

## Compatibilidad electromagnética

Todo circuito puede emitir interferencia y recibirla. Reducirla exige controlar los caminos de ida y retorno de corriente, filtrar entradas, apantallar cuando corresponde y evitar bucles grandes. “Masa” no es un sumidero mágico: la corriente siempre cierra un recorrido.

## Profundización

### Campo cercano y campo lejano

Cerca de una antena dominan componentes que almacenan energía y la geometría es compleja. Lejos, la onda se aproxima a una onda plana y patrón, ganancia y polarización resultan más fáciles de definir. La frontera depende del tamaño de la antena y la longitud de onda.

### Guías, coaxial y fibra

Una guía confina la onda mediante fronteras. El coaxial guía campos entre conductores; la fibra confina luz en el núcleo; una guía metálica transporta modos por encima de ciertas frecuencias. Cambian los materiales, pero la pregunta común es qué configuraciones de campo pueden propagarse.

### Energía por bit

Recibir información requiere distinguir estados pese al ruido. La energía disponible por bit conecta potencia, velocidad y robustez: con potencia fija, aumentar la tasa reparte menos energía a cada bit.

## Ejercicio práctico

Calcula la longitud de onda aproximada para radio FM, WiFi de 2,4 GHz, WiFi de 5 GHz y una portadora óptica. Compárala con antenas u objetos cotidianos y explica por qué el tamaño relativo cambia la propagación y el diseño.

## Autoevaluación

1. ¿Qué se propaga en una onda electromagnética?
2. ¿Por qué una frecuencia mayor no reduce por sí sola el tiempo de viaje?
3. ¿Cómo puede una antena tener ganancia sin crear energía?
4. ¿Qué provoca una reflexión en un cable?
5. ¿Por qué el camino de retorno importa en compatibilidad electromagnética?
