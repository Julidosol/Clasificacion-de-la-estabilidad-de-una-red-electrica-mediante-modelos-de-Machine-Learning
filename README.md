# Clasificación de la estabilidad de una red eléctrica mediante modelos de Machine Learning

## Problema abordado

En este proyecto se aborda el problema de **clasificar la estabilidad de una red eléctrica** utilizando técnicas de Machine Learning.

El objetivo es determinar si una configuración determinada de una red eléctrica presenta un estado **estable (`stable`)** o **inestable (`unstable`)**, a partir de diferentes parámetros relacionados con los tiempos de reacción, potencia y coeficientes de elasticidad de los nodos.

El problema corresponde a un caso de **aprendizaje supervisado de clasificación binaria**, debido a que se dispone de una variable objetivo previamente etiquetada.

---

## Dataset utilizado

Se utilizó el dataset **Electrical Grid Stability Simulated Data**, correspondiente a datos simulados de una red eléctrica compuesta por cuatro nodos.

La base de datos contiene **10.000 registros y 14 variables**. De estas, 12 variables fueron utilizadas como entradas del modelo:

* `tau1`, `tau2`, `tau3`, `tau4`: tiempos de reacción de los cuatro nodos.
* `p1`, `p2`, `p3`, `p4`: parámetros relacionados con la potencia.
* `g1`, `g2`, `g3`, `g4`: coeficientes de elasticidad.

La variable objetivo fue `stabf`, que contiene dos clases:

* `stable`: sistema estable.
* `unstable`: sistema inestable.

La variable `stab` no fue utilizada como predictor, debido a su relación directa con la estabilidad del sistema y al riesgo de introducir fuga de información (*data leakage*) en el modelo.

La base presenta 6.380 registros de la clase `unstable` y 3.620 de la clase `stable`.

---

## Solución propuesta

Se implementaron y compararon cinco algoritmos de clasificación:

1. **Árbol de decisión**
2. **Bagging con árboles de decisión**
3. **Random Forest**
4. **AdaBoost**
5. **XGBoost**

Los datos fueron divididos en un **80 % para entrenamiento y 20 % para prueba**. Para el entrenamiento se utilizó **validación cruzada estratificada de 10 folds**.

Para cada algoritmo se realizó una búsqueda de hiperparámetros mediante `GridSearchCV`, con el objetivo de encontrar la configuración con mejor desempeño promedio.

Los modelos fueron evaluados mediante:

* Accuracy
* Precision
* Recall
* F1-score
* Matriz de confusión

Finalmente, el modelo seleccionado fue almacenado mediante `Joblib` para comprobar que pudiera ser cargado y utilizado nuevamente.

---

## Resultados encontrados

Los resultados obtenidos durante la validación cruzada fueron:

| Modelo            | Accuracy CV | Accuracy Test |   Precision |      Recall |    F1-score |
| ----------------- | ----------: | ------------: | ----------: | ----------: | ----------: |
| Árbol de decisión |     85,80 % |       85,85 % |     89,37 % |     88,32 % |     88,85 % |
| Bagging           |     91,53 % |       91,55 % |     92,68 % |     94,20 % |     93,43 % |
| Random Forest     |     92,06 % |       92,45 % |     92,45 % |     96,00 % |     94,19 % |
| AdaBoost          |     84,31 % |       86,45 % |     86,87 % |     92,79 % |     89,73 % |
| **XGBoost**       | **94,70 %** |   **95,15 %** | **95,38 %** | **97,10 %** | **96,23 %** |

El modelo seleccionado mediante la métrica **F1-score** fue **XGBoost**.

La configuración obtenida mediante la búsqueda de hiperparámetros fue:

* `learning_rate = 0.1`
* `max_depth = 7`
* `n_estimators = 200`
* `subsample = 0.8`

Sobre el conjunto de prueba, XGBoost obtuvo un **Accuracy de 95,15 %** y un **F1-score de 96,23 %**.

La clasificación obtenida para las dos clases fue:

| Clase    | Precision | Recall | F1-score |
| -------- | --------: | -----: | -------: |
| Stable   |      95 % |   92 % |     93 % |
| Unstable |      95 % |   97 % |     96 % |

Las variables con mayor importancia dentro del modelo XGBoost fueron `tau4`, `tau1`, `tau3`, `tau2` y `g3`.

Finalmente, el modelo fue guardado como:

`mejor_modelo_estabilidad_electrica.joblib`

Después de cargar nuevamente el archivo, se obtuvo un **Accuracy de 95,15 %**, verificando que el modelo guardado conservara su capacidad de predicción.

---

## Conclusiones

El proyecto permitió aplicar técnicas de Machine Learning a un problema relacionado con la **estabilidad de sistemas eléctricos**, utilizando datos simulados de una red de cuatro nodos.

La comparación de los cinco algoritmos mostró diferencias en su desempeño. Los métodos de ensamble presentaron, en general, mejores resultados que el árbol de decisión individual.

Entre los modelos evaluados, **XGBoost obtuvo un Accuracy de 95,15 % y un F1-score de 96,23 % sobre el conjunto de prueba**, siendo el modelo seleccionado para este proyecto de acuerdo con el procedimiento implementado.

El uso de validación cruzada estratificada de 10 folds y búsqueda de hiperparámetros permitió realizar una evaluación sistemática de las diferentes configuraciones de los modelos.

Finalmente, el modelo seleccionado pudo ser guardado y posteriormente cargado mediante `Joblib`, manteniendo un Accuracy de 95,15 %, lo que demuestra que el modelo entrenado puede ser reutilizado para realizar nuevas predicciones sobre configuraciones de la red eléctrica.
