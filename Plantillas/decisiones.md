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