# Resultados de Experimentos: Neurito - Cerebro Artificial

Este repositorio Documenta los resultados y el analisis del comportamiento de una red neuronal sencilla diseñada para controlar un sistema de mascota virtual("Neurito") basándose en tres entradas principales: Hambre, Energía y Aburrimiento.

## 1. Experimientos realizados

### variación de una sola necesidad 

Prueba: Se mantuvieron fijas el hambre y el aburrimiento en 0.2 (bajas), mientras se redujo gradualmente la energía desde 1.0 hacia 0.0. Resultado: La acción predicha se mantuvo en estado neutral o satisciendo las nececidades bajas hasta que la energía cayó por debajo de 0.8 (es decir, el cansancio superó el 0.2). en ese punto de cruce, la red cambio de inmediato su predicción predominante a la acción de DORMIR. Esto demuestra que la red aprendió exitosamente las fronteras de decisión dictadas por la regla sintética argmax.

### Prioridades en conflicto (Casi iguales)

Prueba: Se configuraron las entradas con nescesidades criticas idénticas, por ejemplo: Hambre = 85, aburrimiento = 0.8, Energía = 0.15 (cansacio = 0.85). Resultado: Las salidas de la función Softmax mostraron una incertidumbre. Las probabilidades de Comer y Dormir oscilaron cerca del 45% cada una, mientras que Jugar obtuvo un 10%. La función Softmax resalta la competitividad de las clases: en lugar de un salto binario absoluto, refleja matemáticamente el empate probabilistico entre las dos necesidads más urgentes.


