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

| # | Fecha | Decisión | Alternativa descartada | El por que de la decision | Efecto esperado en el resultado |
|---|-------|----------|------------------------|---------|-------------------------------|
| 10 | 14-10-2026 | Usar correlación de **Spearman** en lugar de Pearson | Seguir con Pearson | Indicación de la profe. Pearson solo mide relaciones lineales entre variables numéricas; Spearman trabaja por rangos y sirve para variables ordinales (colesterol, glucosa, categorías) | Los valores cambian poco (ap_hi: 0,43 → 0,45) pero el método es adecuado para el tipo de datos |
| 11 | 14-10-2026 | Comparar variables continuas entre pacientes con y sin ECV con **Mann-Whitney U** | t-test de Student | Las variables no son normales (presiones concentradas en valores redondos como 120 o 80) y Mann-Whitney no asume normalidad | Comparación válida; medianas por grupo fáciles de interpretar |
| 12 | 14-10-2026 | Medir variables categóricas con **chi-cuadrado + V de Cramér** y mostrar % de ECV por categoría | Solo correlaciones o solo el p-valor | Permite ver relaciones no lineales, como la curva en J de la presión (hipotensión 28 % vs. normal 22 % de ECV) | Ranking comparable entre variables categóricas |
| 13 | 14-10-2026 | Interpretar **tamaños de efecto** (V de Cramér, rango-biserial) y no los p-valores | Decidir por p < 0,05 | Con 68.618 filas casi cualquier diferencia da significativa, aunque sea irrelevante | Evita concluir que variables como género o alcohol "importan" |
| 14 | 14-10-2026 | Mantener `smoke` y estratificarla por edad para entender su relación negativa con ECV | Eliminarla o "corregirla" | Que fumar parezca proteger va contra la evidencia médica. Se probó si lo explicaban el género o la edad y no alcanzan | Se reporta como limitación del dataset: dato auto-reportado, posible causalidad inversa y no se mide cuánto se fuma |
| 15 | 14-10-2026 | Usar **información mutua** para el ranking final de variables | Ordenar solo por correlación | Capta relaciones no lineales y no monótonas; sirve para elegir las variables del modelo | Top 5: sistólica, categoría de presión, diastólica, presión de pulso, edad |
| 16 | 14-10-2026 | Usar muestras aleatorias (5.000 y 300 filas, `random_state = 42`) solo para dibujar los gráficos de dispersión; las correlaciones se calculan con todos los datos | Graficar las 68.618 filas | Con todas las filas los puntos se superponen y el gráfico es ilegible; la semilla fija lo hace reproducible | Solo afecta la visualización, no los resultados numéricos |