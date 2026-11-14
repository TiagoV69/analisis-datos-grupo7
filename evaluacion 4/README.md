# Evaluación 4: Reconocimiento de actividades humanas

**Asignatura:** Análisis de Datos

**Docente:** Daniel Alexis Nieto Mora

**Equipo:** 7

**Integrantes:** Santiago Varela, Ferney Orlando López Copete, José Ricardo Quirós, Leidy Melissa Trejos Pamplona

## 1. Problema

Clasificar seis actividades (caminar, subir escaleras, bajar escaleras, sentado, de pie y acostado) a partir de los sensores de un smartphone en la cintura. El componente no supervisado busca agrupaciones sin usar etiquetas y las compara con las actividades reales.

Para desarrollar el análisis se utilizan técnicas de aprendizaje automático desde dos enfoques complementarios:

- Aprendizaje supervisado: permite construir modelos de clasificación para reconocer las actividades a partir de las características extraídas de los sensores.

- Aprendizaje no supervisado: busca identificar agrupaciones y patrones en las señales de movimiento sin utilizar las etiquetas de las actividades durante el entrenamiento.

**Pregunta de trabajo:** ¿distinguir estático de dinamico es más fácil que distinguir dentro de cada grupo?

## 2. Fuente de datos

Human Activity Recognition Using Smartphones (UCI Machine Learning Repository, id 240).
Reyes-Ortiz, J., Anguita, D., Ghio, A., Oneto, L., & Parra, X. (2013). DOI: 10.24432/C54S4K. Licencia CC BY 4.0.

El dataset no se sube al repo. Para reproducirlo, descargalo desde la pagina de UCI y extraelo en `evaluacion 4/data/`.

## 3. Análisis exploratorio de dato EDA

- 10 299 registros y 561 variables. Sin nulos ni duplicados.
- Variables ya normalizadas entre -1 y 1.
- Clases razonablemente balanceadas: no aplicamos estrategias de desbalance.
- La partición original separa personas (21 en entrenamiento, 9 en prueba), así que la prueba mide generalización.
- 308 de 561 variables están muy correlacionadas (> 0.95, umbral elegido por nosotros).
- PCA: 2 componentes explican 67.5% de la varianza; 67 explican 95%.
- Con tBodyAccMag-mean() los grupos estático y dinámico casi no se traslapan. Dentro de las dinámicas, WALKING y WALKING_UPSTAIRS se solapan.

## 4. Preprocesamiento

En esta etapa se prepararon los datos para su utilización en los componentes supervisado y no supervisado. Se realizaron las siguientes actividades:

- Carga de los conjuntos de entrenamiento y prueba.
- Asignación de nombres únicos a las 561 características.
- Verificación de valores faltantes, duplicados y variables constantes.
- Asociación de las etiquetas numéricas con los nombres de las actividades.
- Identificación de las categorías estáticas y dinámicas.
- Análisis de valores atípicos y distribución de las variables.
- Evaluación de la necesidad de escalamiento y reducción de dimensionalidad mediante PCA.
- Análisis de validación por grupos de participantes para evitar fuga de información.

Los datos procesados se almacenan localmente en formato Pickle para que los notebooks posteriores puedan reutilizarlos.

## 5. Aprendizaje supervisado

Se desarrolló un componente de clasificación utilizando dos algoritmos:

1. **Regresión Logística:** modelo utilizado para identificar las relaciones entre las características de los sensores y las seis actividades humanas.
2. **Random Forest:** modelo basado en un conjunto de árboles de decisión que permite identificar patrones y relaciones entre las variables.

La evaluación se realizó utilizando el conjunto de prueba reservado para participantes diferentes a los utilizados durante el entrenamiento.

### Resultados de clasificación de seis actividades

| Modelo | Accuracy |
|---|---:|
| Regresión Logística | 94,98 % |
| Random Forest | 92,40 % |

La Regresión Logística presentó el mejor desempeño general en la clasificación de las seis actividades.

### Clasificación de actividades estáticas y dinámicas

Al agrupar las actividades en dos categorías se obtuvieron los siguientes resultados:

| Modelo | Accuracy |
|---|---:|
| Regresión Logística | 99,9 % |
| Random Forest | 100 % |

Los resultados evidencian que distinguir entre actividades estáticas y dinámicas es considerablemente más sencillo que reconocer individualmente las seis actividades.

Las matrices de confusión permitieron identificar dificultades principalmente entre las categorías sentado y de pie, debido a la similitud de sus patrones de movimiento.

**Notebook:** `notebooks/03_supervisado.ipynb`.

Los resultados complementarios se encuentran en `resultados_supervisado/`.

## 6. Aprendizaje no supervisado

Este componente descubre estructuras en las señales sin utilizar las etiquetas durante PCA ni durante el entrenamiento de los algoritmos. Se compararon **K-means** y una **mezcla gaussiana (GMM)** sobre los 36 componentes necesarios para conservar el 90,22 % de la varianza. Las actividades reales se consultaron únicamente después de formar los grupos.

### Selección del número de grupos

Ambos algoritmos obtuvieron su mayor Silhouette Score con dos grupos:

| Algoritmo | Mejor k | Silhouette |
|---|---:|---:|
| K-means | 2 | 0,5348 |
| Mezcla gaussiana | 2 | 0,5236 |

La evaluación posterior mostró que esos dos grupos corresponden casi exactamente a actividades estáticas y dinámicas: K-means alcanzó 99,85 % de correspondencia y GMM 98,49 %. Esto confirma la pregunta de trabajo: distinguir movimiento de reposo es mucho más fácil que separar las seis actividades.

### Correspondencia con las seis actividades

Al fijar `k=6`, sin suministrar las etiquetas al entrenamiento, se obtuvieron los siguientes resultados:

| Algoritmo | Silhouette | ARI | NMI | Pureza | Correspondencia 1:1 |
|---|---:|---:|---:|---:|---:|
| K-means | 0,1716 | 0,4612 | 0,5887 | 0,5994 | 0,5994 |
| Mezcla gaussiana | 0,1133 | 0,4140 | 0,5823 | 0,6269 | 0,5946 |

K-means presentó la mejor separación interna y la mayor correspondencia uno a uno. Los grupos separan claramente lo estático de lo dinámico, pero mezclan actividades similares: `SITTING` con `STANDING` y las tres variantes de caminar. `LAYING` fue la actividad estática más diferenciable.

PCA mejoró el Silhouette de K-means con seis grupos de 0,1313 a 0,1716 respecto a las 561 variables originales. Para visualización, t-SNE conservó mejor las vecindades locales que PCA 2D (*trustworthiness* de 0,9895 frente a 0,8941), aunque t-SNE no se utilizó para entrenar los clusters.

En la prueba con participantes no vistos, K-means alcanzó ARI 0,4749 y NMI 0,6143; GMM obtuvo ARI 0,4483 y NMI 0,6022. La estructura se transfiere parcialmente entre usuarios, pero no identifica de forma fiable las seis actividades.

**Notebook:** `notebooks/04_no_supervisado.ipynb`.

Los resultados reproducibles se encuentran en `resultados_no_supervisado/`.

## 7. Estructura del proyecto

```text
evaluacion 4/
│
├── README.md
│
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_preprocesamiento.ipynb
│   ├── 03_supervisado.ipynb
│   └── 04_no_supervisado.ipynb
│
├── resultados_supervisado/
│   ├── metricas_validacion.csv
│   ├── metricas_prueba.csv
│   └── predicciones_test.csv
│
├── resultados_no_supervisado/
│   ├── metricas_agrupamiento.csv
│   ├── silhouette_por_k.csv
│   ├── correspondencia_estatica_dinamica_k2.csv
│   ├── metricas_generalizacion.csv
│   ├── composicion_clusters.csv
│   ├── composicion_estatica_dinamica.csv
│   └── asignaciones_clusters.csv
│
└── data/
    └── (dataset original y datos procesados, no incluidos en GitHub)
```

## 8. Instrucciones de ejecución

Para reproducir el proyecto se recomienda utilizar Python mediante Jupyter Notebook o Google Colab.

1. Descargar el conjunto de datos desde UCI Machine Learning Repository.
2. Organizar los archivos originales dentro de `evaluacion 4/data/`, respetando la estructura de carpetas requerida por los notebooks.
3. Instalar las bibliotecas necesarias:

```bash
pip install numpy pandas matplotlib seaborn scipy scikit-learn jupyter
```

4. Ejecutar los notebooks en el siguiente orden:

   - `01_eda.ipynb`: exploración y comprensión de los datos.
   - `02_preprocesamiento.ipynb`: preparación y almacenamiento de los datos procesados.
   - `03_supervisado.ipynb`: entrenamiento, evaluación y comparación de los modelos de clasificación.
   - `04_no_supervisado.ipynb`: descubrimiento de agrupaciones y patrones.

5. Consultar los resultados generados en `resultados_supervisado/` y `resultados_no_supervisado/`.

**Nota:** El dataset original y los archivos intermedios de procesamiento se excluyen del repositorio mediante `.gitignore`. Los notebooks y resultados necesarios para documentar el análisis sí se conservan en GitHub.

## 9. Conclusiones generales

El análisis exploratorio permitió reconocer las características y relaciones presentes en las señales de los sensores, así como identificar oportunidades para aplicar técnicas de clasificación y agrupamiento.

En el componente supervisado, la Regresión Logística obtuvo un Accuracy del 94,98 % en la clasificación de las seis actividades, superando el 92,40 % alcanzado por Random Forest. En la clasificación binaria de actividades estáticas y dinámicas, ambos modelos presentaron resultados superiores al 99 %.

Estos resultados muestran que las señales de movimiento contienen información útil para reconocer actividades humanas y que la dificultad de clasificación depende de la similitud entre las categorías.

El componente no supervisado confirmó que la separación dominante del conjunto es estática frente a dinámica: los dos clusters de K-means corresponden en 99,85 % con esas categorías. En cambio, al exigir seis grupos, la correspondencia uno a uno disminuye a cerca del 60 %. Sin etiquetas, los algoritmos descubren bien el nivel general de movimiento, pero no reconstruyen por completo el significado de cada actividad.

Los dos enfoques coinciden en las dificultades principales. En supervisado, los errores se concentran entre `SITTING` y `STANDING` y entre actividades dinámicas parecidas; en no supervisado, esas mismas actividades comparten clusters. La Regresión Logística sigue siendo la alternativa más adecuada si el objetivo es nombrar una de las seis actividades, mientras el agrupamiento es útil para explorar estructura, segmentar patrones o detectar categorías amplias sin anotaciones.

La generalización se evaluó con personas diferentes entre entrenamiento y prueba, pero el dataset utiliza un único modelo de teléfono, una posición fija en la cintura y un protocolo controlado. Los resultados no garantizan igual desempeño con otros dispositivos, ubicaciones, orientaciones, poblaciones o actividades no contempladas.



