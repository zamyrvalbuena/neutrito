# Resultados de Experimentos: Neurito - Cerebro Artificial

Este repositorio Documenta los resultados y el analisis del comportamiento de una red neuronal sencilla diseñada para controlar un sistema de mascota virtual("Neurito") basándose en tres entradas principales: Hambre, Energía y Aburrimiento.

## 1. Experimientos realizados

### variación de una sola necesidad 

Prueba: Se mantuvieron fijas el hambre y el aburrimiento en 0.2 (bajas), mientras se redujo gradualmente la energía desde 1.0 hacia 0.0. Resultado: La acción predicha se mantuvo en estado neutral o satisciendo las nececidades bajas hasta que la energía cayó por debajo de 0.8 (es decir, el cansancio superó el 0.2). en ese punto de cruce, la red cambio de inmediato su predicción predominante a la acción de DORMIR. Esto demuestra que la red aprendió exitosamente las fronteras de decisión dictadas por la regla sintética argmax.

### Prioridades en conflicto (Casi iguales)

Prueba: Se configuraron las entradas con nescesidades criticas idénticas, por ejemplo: Hambre = 85, aburrimiento = 0.8, Energía = 0.15 (cansacio = 0.85). Resultado: Las salidas de la función Softmax mostraron una incertidumbre. Las probabilidades de Comer y Dormir oscilaron cerca del 45% cada una, mientras que Jugar obtuvo un 10%. La función Softmax resalta la competitividad de las clases: en lugar de un salto binario absoluto, refleja matemáticamente el empate probabilistico entre las dos necesidads más urgentes.

### Modificación de la arquitectura (Capa oculta)

Prueba: Se ajustó el tamaño de la capa oculta (w1, b1, w2), probando con 3, 10 y 20 neuronas (originalmente 6).

#### 3 Neuronas:
 la perdida se estancó en valores más altos (0.3) y la precisión bajó al 85%. La red sufre de subajuste(underfitting); le faltan párametros para trazar fronteras de desición complejas.

#### 10 y 20 Neuronas:
La pérdida convergió mucho más rapido y a un valor más cercano a 0, logrando una presición casi perfecta de (>98%). Mayor cantidad de neuronas otorga mayor capacidad de representación para la función objetivo.

### Ajuste de Hiperparámetros (Épocas y Tasa de Aprendizaje)
Prueba: Alteración del ciclo de entrenamiento original (1200 épocas, LR = 0.1).

Tasa de aprendizaje alta (LR = 1.0): El entrenamiento se volvió inestable. La pérdida osciló salvajemente y la precisión final fue deficiente debido a que los saltos en el descenso del gradiente eran demasiado grandes (sobrepasando el mínimo local).

Tasa de aprendizaje baja (LR = 0.001): El entrenamiento se estancó. Después de 1200 épocas, la pérdida seguía alta. Requeriría decenas de miles de épocas extra para converger.

Más épocas (ej. 5000 con LR 0.1): El entrenamiento mejora marginalmente al principio, pero luego la mejora se aplana.

### Adición de una Cuarta Acción

#### 1. Datos de entrada (X):
Se requeriría una cuarta variable, por ejemplo, "Suciedad" (valores de 0 a 1). La matriz X pasa de (3000, 3) a (3000, 4).

#### 2. Etiquetas (Targets):
La regla de generación debería evaluar 4 columnas:
np.column_stack((Hambre, 1 - Energía, Aburrimiento, Suciedad)).

#### 3. Arquitectura:
La matriz de pesos de salida W2 pasaría de ser (6, 3) a (6, 4) y el sesgo b2 sería de tamaño (1, 4).

### 2. Análisis Conceptual
#### 1. ¿En qué se diferencian pesos y sesgos?
Pesos (Weights): Multiplican el valor de entrada. Determinan la "importancia" o fuerza de la conexión entre una entrada y la siguiente neurona.

Sesgos (Biases): Son valores constantes que se suman al resultado de la multiplicación. Permiten desplazar la función de activación, haciendo posible que una neurona se active (o se inhiba) incluso si todos los valores de entrada son cero.

#### 2. ¿Por qué el cansancio es 1 - energía?
La política del modelo evalúa qué necesidad tiene el valor más alto numéricamente usando np.argmax. El hambre y el aburrimiento ya se miden en términos de "necesidad" (valores cercanos a 1 significan urgencia). La energía, por el contrario, es un recurso; un valor de 1 significa estar descansado. Restar 1 - energía transforma la métrica del recurso (energía) en una métrica de necesidad (cansancio), alineándola en la misma escala lógica que las demás para que argmax tome la decisión correcta.

#### 3. ¿Qué hace ReLU y qué hace Softmax?
ReLU (Rectified Linear Unit): Se aplica en la capa oculta. Es una función no lineal que devuelve 0 si el valor es negativo, y deja pasar el valor intacto si es positivo. Permite que la red aprenda relaciones complejas y no lineales sin colapsar en una simple regresión lineal.

Softmax: Se aplica en la capa de salida. Toma los puntajes brutos (logits) y los transforma en una distribución de probabilidades donde todos los valores suman exactamente 1.0 (o 100%). Esto es ideal para clasificación multiclase porque exagera las diferencias, haciendo que la opción ganadora destaque.

#### 4. ¿Por qué una precisión alta puede ser engañosa?
Una precisión cercana al 100% puede indicar sobreajuste (overfitting), donde la red simplemente "memoriza" los datos de entrenamiento pero es incapaz de generalizar ante datos nuevos no vistos. Además, si el conjunto de datos sintéticos estuviera desbalanceado (ej. 90% de los casos dictan "COMER"), un modelo que siempre prediga "COMER" obtendría un 90% de precisión sin haber aprendido absolutamente nada de la lógica real.
