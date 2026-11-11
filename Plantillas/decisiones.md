# Registro de decisiones — Equipo Medicina

Cada decisión de limpieza o de método, con la alternativa que se descartó.
Sirve para escribir la metodología del informe sin tener que reconstruirla
de memoria al final.

## Limpieza de datos

| # | Fecha | Decisión | Alternativa descartada | El por que de la decision | Efecto esperado en el resultado |
|---|-------|----------|------------------------|---------|-------------------------------|
| 1 | 15-09-2026 | Verificar datos faltantes y valores duplicados | Eliminar filas | El dataset no tiene valores faltantes ni filas duplicadas, así que no hacía falta hacerlo | Ninguno: se conservan las 70.000 filas |
| 2 | 15-09-2026 | Eliminar pacientes con altura fuera de 120–220 cm o peso fuera de 30–200 kg | Sustituir valores fuera de rango con la mediana | Son valores médicamente imposibles o errores de carga | Se eliminan 60 filas (0,09 %) |
| 3 | 15-09-2026 | Eliminar filas donde la presión diastólica (`ap_lo`) es mayor que la sistólica (`ap_hi`) | Intercambiar los valores o corregir el error de tipeo | Es fisiológicamente imposible que la diastólica supere a la sistólica | Se eliminan 1.231 filas (1,8 %). |
| 4 | 15-09-2026 | Eliminar presiones fuera de 60–250 mmHg (sistólica) y 40–200 mmHg (diastólica) | Corregir errores de tipeo | Valores como −150 o 16.020 no son medibles en un paciente vivo | Se eliminan 91 filas. |
| 5 | 15-09-2026 | Convertir la edad de días a años con `edad / 365,25` | Dejar la edad en días o redondear | Mejor formato para analizar | Mejora la lectura de gráficos y tablas. Rango final: 29–64 años |

## Transformación y nuevas variables

| # | Fecha | Decisión | Alternativa descartada | El por que de la decision | Efecto esperado en el resultado |
|---|-------|----------|------------------------|---------|-------------------------------|
| 6 | 22-09-2026 | Crear `categoria_presion` (1 = hipotensión a 6 = crisis) según la tabla de clasificación de presión arterial, evaluando de la más grave a la menos grave | Usar solo las presiones numéricas | Junta sistólica y diastólica en una sola variable con sentido clínico | Fuerte concentracion en grupos 4 y 5 (hipertension nivel 1 y 2) |
| 7 | 22-09-2026 | Calcular el IMC (`peso / altura²`) y categorizarlo según la OMS (6 grupos) | Usar peso y altura por separado | El imc es el indicador clinico estandar | IMC se asocia más con ECV que el peso solo |
| 8 | 22-09-2026 | Crear `presion_pulso` = sistólica − diastólica | - | Es un indicador clínico arterial | Relacion moderada con la variable objetivo |
| 9 | 22-09-2026 | Crear `habitos_riesgo` (fuma + alcohol + inactivo) y `sindrome_metabolico` (colesterol + glucosa) | Analizar cada componente por separado | Intento de juntar variables débiles para encontrar mayor relacion | No sirve |

## Método de análisis

| # | Fecha | Decisión | Alternativa descartada | Por qué | Efecto en el resultado |
|---|-------|----------|------------------------|---------|------------------------|
| 10 | 14-10-2026 | Analizar cada variable con gráficos de barras del % de enfermos por grupo | Otros tests | Es más simple, visual y fácil de explicar | Se ve qué variables aumentan el riesgo |
| 11 | 14-10-2026 | Mostrar hábitos y género juntos en un solo gráfico | Un gráfico por variable, o no mostrarlos | El objetivo pide analizar el estilo de vida, aunque casi no influya | Todos quedan entre 47 % y 53 %: los hábitos no sirven para predecir |
| 12 | 14-10-2026 | Medir la correlación con el % de enfermos de cada grupo | Quedarse solo con la correlación del heatmap | Como `cardio` es 0 o 1, el heatmap subestima la relación | Las correlaciones pasan de 0,2–0,4 a más de 0,9 |
| 13 | 14-10-2026 | Comparar sanos y enfermos con boxplots | Comparar solo promedios | Mejor visualmente | Se ve que 120 mmHg funciona como limite entre sanos y enfermos |
| 14 | 14-10-2026 | Sumar curvas de distribución de presión, edad e IMC | Quedarse solo con los boxplots | Muestran la forma completa de cada grupo | Se ve el pico de los sanos en 120 mmHg y cuánto se superponen los grupos |
| 15 | 14-10-2026 | Contar cuántos factores de riesgo tiene cada paciente (presión alta, colesterol alto, 55+ años, obesidad) | No combinar variables | Es la forma más simple de ver si el riesgo se acumula | El % de enfermos va de 21 % (ningún factor) a 82 % (los cuatro) |
| 16 | 14-10-2026 | Hacer un mapa de riesgo cruzando edad y presión | Un heatmap de correlación común | Muestra cómo se combinan dos factores | El riesgo va de 8 % a 89 % |

## Modelado: regresión logística (logit)

| # | Fecha | Decisión | Alternativa descartada | Por qué | Efecto en el resultado |
|---|-------|----------|------------------------|---------|------------------------|
| 17 | 11-11-2026 | Calcular las probabilidades con un modelo logit (regresión logística) | Regresión lineal | La variable a predecir es binaria (0/1) | Cada paciente recibe una probabilidad entre 0 y 1 |
| 18 | 11-11-2026 | Definir `presion_alta` = 1 si `categoria_presion >= 5` (hipertensión nivel 2 o más) | Incluir también HTA nivel 1, o usar solo la sistólica | Son las categorias consideradas como factor de riesgo de ECV | Define la Y binaria del modelo de presión alta |
| 19 | 11-11-2026 | No usar `ap_hi`, `ap_lo`, `categoria_presion`, `presion_pulso` ni `cantidad_factores` como variables explicativas del modelo de presión alta | - | Con ellas se construye la variable a predecir | Evita valores sobreestimados |
| 20 | 11-11-2026 | Cambiar `gender` (1 y 2) a `sexo` (1 = hombre, 0 = mujer) | Usar `gender` tal cual | Con 0/1 el coeficiente se interpreta más fácil | El coeficiente de sexo no cambia, solo la constante |
| 21 | 11-11-2026 | Variables del modelo de presión alta: `age_number`, `sexo`, `imc`, `cholesterol`, `gluc`, `smoke`, `alco`. | Incluir todas las variables | Evitar variables no significativas (`active`) o redundantes | Pseudo R² 0,079 y AUC 0,694: el modelo explica poco |
| 22 | 11-11-2026 | Mantener `gluc` aunque su coeficiente sea negativo (−0,09) | Sacarla del modelo | Mejora levemente el ajuste | Signo contraintuitivo. La correlación entre glucosa y colesterol (0,45) es moderada y no alcanza para explicarlo |
| 23 | 11-11-2026 | Ajustar el mismo modelo solo con pacientes con ECV (`cardio == 1`) | Un único modelo con toda la población | Para poder comparar el R² de ambos modelos | Pseudo R² 0,029 y AUC 0,622 |
| 24 | 11-11-2026 | Crear un tercer modelo con Y = `cardio`, agregando `ap_hi` y `ap_lo` como variables explicativas | Seguir solo con presión alta | Para crear un modelo mas explicativo que prediga si un paciente padece o no de una ECV | Pseudo R² 0,190 y AUC 0,790: ajusta mucho mejor |
| 25 | 11-11-2026 | Usar el modelo de cardio como principal | Dejar el modelo de presión alta como resultado principal | El modelo de cardio es el más sólido | Los gráficos de riesgo salen del modelo de cardio. El modelo de cardio tiene más información (incluye la presión) |

## Evaluación del modelo y gráficos de riesgo

| # | Fecha | Decisión | Alternativa descartada | Por qué | Efecto en el resultado |
|---|-------|----------|------------------------|---------|------------------------|
| 26 | 11-11-2026 | Evaluar el modelo de cardio con 70 % de pacientes para entrenar y 30 % para probar | Evaluar con los mismos datos del entrenamiento | Evaluar con datos que el modelo no vio es más confiable | AUC de prueba 0,791, casi igual al de entrenamiento (0,790): el modelo generaliza bien |
| 27 | 11-11-2026 | Clasificar con un corte de probabilidad de 0,4 y analizar la matriz de confusión (falsos positivos y falsos negativos) | Corte de 0,5 | En medicina suele ser más grave no detectar a un enfermo que dar una falsa alarma | Sensibilidad (recall) de "Con ECV" de 0,66 a 0,80; falsos negativos de 3.435 a 2.068; falsos positivos de 2.153 a 3.795; exactitud de 0,73 a 0,72 |
| 28 | 11-11-2026 | Armar 5 niveles de riesgo con la probabilidad del modelo cardio (cortes en 0,2, 0,4, 0,6 y 0,8) | - | Cortes simples | Un tercio de los pacientes está en riesgo alto o muy alto |
| 29 | 11-11-2026 | Mapa de riesgo cruzando nivel de riesgo del modelo y grupo de edad | - | Permite ver si, entre pacientes de la misma edad, el % real de ECV aumenta con el nivel de riesgo del modelo | El % real de ECV sube de 9–26 % (Muy bajo) a 84–88 % (Muy alto) |