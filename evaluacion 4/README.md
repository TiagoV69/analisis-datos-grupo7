# Evaluación 4: Reconocimiento de actividades humanas

**Asignatura:** Análisis de Datos

**Docente:** Daniel Alexis Nieto Mora

**Equipo:** 7

**Integrantes:** Santiago Varela, Ferney Orlando López Copete, José Ricardo Quirós, Leidy Melissa Trejos Pamplona

## Problema

Clasificar seis actividades (caminar, subir escaleras, bajar escaleras, sentado, de pie y acostado) a partir de los sensores de un smartphone en la cintura. El componente no supervisado busca agrupaciones sin usar etiquetas y las compara con las actividades reales.

**Pregunta de trabajo:** ¿distinguir estático de dinamico es más fácil que distinguir dentro de cada grupo?

## Fuente de datos

Human Activity Recognition Using Smartphones (UCI Machine Learning Repository, id 240).
Reyes-Ortiz, J., Anguita, D., Ghio, A., Oneto, L., & Parra, X. (2013). DOI: 10.24432/C54S4K. Licencia CC BY 4.0.

El dataset no se sube al repo. Para reproducirlo, descargalo desde la pagina de UCI y extraelo en `evaluacion 4/data/`.

## EDA

- 10 299 registros y 561 variables. Sin nulos ni duplicados.
- Variables ya normalizadas entre -1 y 1.
- Clases razonablemente balanceadas: no aplicamos estrategias de desbalance.
- La partición original separa personas (21 en entrenamiento, 9 en prueba), así que la prueba mide generalización.
- 308 de 561 variables están muy correlacionadas (> 0.95, umbral elegido por nosotros).
- PCA: 2 componentes explican 67.5% de la varianza; 67 explican 95%.
- Con tBodyAccMag-mean() los grupos estático y dinámico casi no se traslapan. Dentro de las dinámicas, WALKING y WALKING_UPSTAIRS se solapan.

## Pendiente por mis compañeros

Preprocesamiento,
Supervisado,
No supervisado,
Resultados y
Conclusiones
