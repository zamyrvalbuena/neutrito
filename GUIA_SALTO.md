# Neurito 2.0: aprende a saltar obstáculos

Neurito aprende **cuándo saltar** ante obstáculos que se aproximan con diferentes velocidades. El cerebro se implementa manualmente con NumPy y el aprendizaje puede observarse en Pygame.

## ¿Qué aprende?

La red recibe tres características normalizadas: distancia al obstáculo, velocidad y tiempo estimado hasta alcanzarlo (distancia / velocidad). La última es una característica derivada, no una observación independiente.

La arquitectura es **3 → 12 → 1**: doce neuronas ocultas con ReLU y una salida con sigmoide. La última devuelve una puntuación entre cero y uno que, con el umbral 0.5, determina si Neurito debe saltar cuando está en el suelo.

## ¿Cómo aprende?

Aplicamos **aprendizaje supervisado**, no aprendizaje por refuerzo. Creamos datos sintéticos que incluyen ejemplos donde es conveniente saltar y otros donde debe esperar. Para generar las etiquetas, definimos una ventana de referencia: saltar cuando el tiempo estimado está entre **7 y 13 fotogramas**. No es una regla aprendida por recompensa ni garantiza evitar choques en todas las situaciones.

La red ajusta sus pesos a partir de la **entropía cruzada binaria**, la **retropropagación** y el **descenso de gradiente**:

`W_nuevo = W_anterior - tasa_de_aprendizaje * gradiente`

El conjunto de entrenamiento equilibra aproximadamente las dos decisiones. De otra manera, una red podría aparentar buena precisión diciendo casi siempre «esperar».

La precisión se mide sobre ejemplos distintos de los utilizados en el entrenamiento. **Una precisión alta no garantiza un buen resultado en el juego**, que también depende de la física del salto.

## Archivos

```text
Neurito/
├── main.py                 # Mascota original: comer, dormir, jugar
├── red-sencilla.py         # Ejemplo de una neurona
├── salto_modelo.py         # Red neuronal manual y datos de entrenamiento
├── salto_juego.py          # Física y detección de colisiones
├── saltar.py               # Juego y visualización del entrenamiento
├── GUIA_SALTO.md
└── assets/
    └── jugar_1.png        # Reutilizado en el nuevo juego
```

## Ejecución

```bash
uv sync
uv run saltar.py
```

No se necesitan dependencias nuevas: se reutilizan NumPy y Pygame.

## Controles

| Tecla | Función |
|---|---|
| `1` | Entrenar una época |
| `2` | Entrenar cien épocas |
| `3` | Activar o pausar entrenamiento continuo |
| `A` | Alternar control automático y manual |
| `Espacio` | Saltar manualmente |
| `C` | Reiniciar pista y contadores |
| `R` | Reiniciar pesos y contadores |
| `Esc` | Salir |

## Experimentos

1. Prueba el juego sin entrenar. Anota colisiones y obstáculos esquivados.
2. Entrena de cien en cien épocas; compara la pérdida, la precisión y el comportamiento.
3. Cambia `ocultas=12` por `ocultas=3` o `ocultas=24`; compara generalización.
4. Modifica la tasa de aprendizaje `0.8` a `0.01` o `1.2` y estudia la convergencia.
5. Cambia la ventana de entrenamiento en `etiqueta()` y en la generación de positivos. Comprueba si la política resultante es compatible con la física.

**Pregunta central:** ¿por qué una red puede clasificar ejemplos con buena precisión y aun así chocar?

# Experimentos: Neurito 2.0(salto)

## 1. Juego sin entrenar
Al inicial el simulador sin entrenamiento, los pesos de la red son aleatorios y la probabilidad de salto es errática. El personaje choca constantemente contra los obstáculos porque no reconoce la relación entre distancia, la velocidad y la necesidad de saltar.

## 2. Entrenamiento gradual (Cien épocas)
Entrenar en bloques de 100 épocasa revela una mejora progresiva. La pérdida (entropía cruzada binaria) disminuye drasticamente en las primeras 300 épocas. La presición sube por encima del 90%, y visualmente Neurito comienza a saltar justo cuando el obstaculo entra en la "ventana de salto" (entre 7 y 13 fotogramas de tiempo estimado).

## 3. modificacion de la capa oculta (ocultas=12 a 3 o 24) 
La red original utiliza una arquitectura 3->12->1.

### Con 3 neuronas:
La red sufre sub ajuste (underfitting). Le cuesta converger y la precisión se estanca en valores bajos, provocando colisiones frecuentes especialmente en el cambio de velocidad del obstaculo.

### Con 24 neuronas: 
La perdida baja más rapido en las primeras épocas debido a la mayor capacidad de representación, pero el costo computacional aumenta. La presición de prueba es marginalmente mejor, auntq puede ser mas susceptible al sobreajuste.

## Cambio en la tasa de parendizaje (0.8 a 0.01 o 1.2)
El modelo aplica descenso de gradiente multiplicando el gradiente por la tasa de aprendisaje.

### Tasa 0.01:
el descenso de gradiente es demasiado pequeño. se requieren miles de épocas adicionales para ver una reducción significativa en la pérdida, haciendo el entrenamiento ineficiente.

### Tasa 1.2:
El tamaño del paso es excesivo. La pérdida oscila bruscamente y la red no logra estabilizarse en un mínimo, arruinando la precisión y provocando saltos erráticos