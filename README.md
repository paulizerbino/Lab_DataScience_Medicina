PROBLEMA A RESOLVER

Una de las principales causas de muerte a nivel mundial son las enfermedades cardiovasculares. Gran parte de los factores de riesgo asociados a la enfermedad (presión arterial, colesterol, glucosa, adicciones, etc), son medibles, y muchas veces, prevenibles. Sin embargo, la dificultad se encuentra en detectar qué pacientes presentan mayores riesgos de padecerla, lo cual no es fácil de hacer desde una sola inspección clínica ni con grandes volúmenes de datos.

El problema que buscaremos resolver responderá si: ¿es posible identificar patrones en los datos clínicos y de estilo de vida de un paciente que permitan predecir la presencia de una enfermedad cardiovascular? A partir del dataset de Kaggle buscaremos analizar qué variables están más asociadas a la enfermedad y evaluar la posibilidad de construir un modelo que la prediga con la mejor precisión posible. 


OBJETIVOS

El objetivo general de este trabajo es analizar los datos clínicos y el estilo de vida de pacientes para identificar patrones y características asociadas a las enfermedades cardiovasculares y, a partir de esto, desarrollar un modelo que nos permita predecir si un paciente presenta o no la enfermedad.

Los objetivos que identificamos son:
- Realizar una correcta limpieza de datos, tanto de valores atípicos o inconsistentes, como también conversión de variables para poder obtener un análisis más preciso.
- Analizar la relación entre las variables clínicas y los datos sobre el estilo de vida de los pacientes.
- Identificar cuales son las variables con mayor influencia en la presencia de la enfermedad. 
- Comparar las características de aquellos pacientes que presentan una enfermedad cardiovascular con los que no la tienen.
- Desarrollar un modelo predictivo que nos permita determinar, con las variables analizadas, si un paciente presenta o no la patología. 
- Evaluar el desempeño del modelo desarrollado con métricas (precisión, recall, matriz de confusión, entre otras) para determinar qué tan precisa resulta la clasificación de los pacientes.


DATOS QUE VAMOS A UTILIZAR

Para analizar este problema y alcanzar nuestros objetivos, elegimos un dataset de Kaggle que contiene la información necesaria para hacerlo. Se trata del dataset llamado “Cardiovascular Disease dataset”, de Svetlana Ulianova, actualizado por última vez hace 8 años. Cuenta con 70.000 registros de distintos pacientes, combinando información dada por el paciente, resultados de exámenes médicos y la variable objetivo: si el paciente presenta o no la enfermedad cardiovascular. 

Cada paciente está identificado por un número único, junto con 11 características que lo describen, además de la variable objetivo. Estas variables se pueden agrupar según su tipo de dato:
1. Números enteros (INT): edad (en días), altura (en cm), presión arterial sistólica y presión arterial diastólica. 
2. Decimales (FLOAT): peso (en kg). 
3. Binarios (0/1): fumar, consumo de alcohol, actividad física y la característica objetivo, donde 1 (indica que la condición aplica) y 0 (que no aplica). 
4. Códigos categóricos: género, colesterol y glucosa. En género: 1 (mujer), 2 (hombre). Mientras que en colesterol y glucosa: 1 (normal), 2 (por encima de lo normal), 3 (muy por encima de lo normal). 

Link-Kaggle: https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset?utm_source=chatgpt.com 
