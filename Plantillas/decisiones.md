# Registro de decisiones — Equipo Medicina

Cada decisión de limpieza o de método, con la alternativa que se descartó.
Sirve para escribir la metodología del informe sin tener que reconstruirla
de memoria al final.

> Fechas: las decisiones 1–10 se tomaron antes del 14/10/2026 (completar con la fecha real si la tienen en los commits). Las 11–16 son del 14/10/2026.
> Conteos de filas calculados sobre `cardio_train.csv` original (70.000 filas).

## Limpieza de datos

| # | Fecha | Decisión | Alternativa descartada | Por qué | Efecto esperado en el resultado |
|---|-------|----------|------------------------|---------|-------------------------------|
| 1 | Completar | Verificar faltantes y duplicados sin aplicar ninguna corrección | Imputar o eliminar filas | El dataset no tiene valores faltantes ni filas duplicadas, así que no hacía falta tratarlos | Ninguno: se conservan las 70.000 filas en este paso |
| 2 | Completar | Eliminar pacientes con altura fuera de 120–220 cm o peso fuera de 30–200 kg | Imputar con la mediana o corregir a mano (ej. altura en metros) | Son valores médicamente imposibles o errores de carga (alturas de 55 cm, pesos de 10 kg); imputarlos sería inventar datos | Se eliminan 60 filas (0,09 %). Impacto mínimo; evita IMC absurdos |
| 3 | Completar | Eliminar filas donde la presión diastólica (`ap_lo`) es mayor que la sistólica (`ap_hi`) | Intercambiar los valores o corregir el error de tipeo (ej. 1000 → 100) | Es fisiológicamente imposible que la diastólica supere a la sistólica | Se eliminan 1.231 filas (1,8 %). **Ojo:** el 77,5 % de estas filas tenía ECV (vs. 49,5 % general), así que se pierde un grupo con más enfermedad. Muchas parecen tener un cero de más (160/1000) y podrían haberse corregido |
| 4 | Completar | Eliminar presiones fuera de 60–250 mmHg (sistólica) y 40–200 mmHg (diastólica) | Winsorizar (recortar a los límites) o corregir errores de tipeo | Valores como −150 o 16.020 no son medibles en un paciente vivo | Se eliminan 91 filas más. Dataset final: **68.618 filas** (se pierde el 1,97 %) |
| 5 | Completar | Convertir la edad de días a años con `edad / 365,25` truncado a entero | Dejar la edad en días o redondear en vez de truncar | Los años son interpretables; 365,25 compensa los bisiestos; truncar replica cómo se dice la edad ("tiene 52") | No cambia la asociación con `cardio`; mejora la lectura de gráficos y tablas. Rango final: 29–64 años |
| 6 | Completar | No usar la columna `id` en el análisis | Incluirla como variable | Es solo un identificador, no tiene información clínica | Evita correlaciones espurias |

## Transformación y nuevas variables

| # | Fecha | Decisión | Alternativa descartada | Por qué | Efecto esperado en el resultado |
|---|-------|----------|------------------------|---------|-------------------------------|
| 7 | Completar | Crear `categoria_presion` (1 = hipotensión a 6 = crisis) según la tabla de clasificación de presión arterial, evaluando de la más grave a la menos grave con `np.select` | Usar solo las presiones numéricas | Junta sistólica y diastólica en una sola variable con sentido clínico | V de Cramér 0,43, la más alta de las categóricas. Ningún paciente quedó sin categoría |
| 8 | Completar | Calcular el IMC (`peso / altura²`) y categorizarlo según la OMS (6 grupos) | Usar peso y altura por separado | El peso solo depende de la altura; el IMC corrige eso y es el indicador clínico estándar | IMC se asocia más con ECV que el peso solo (rango-biserial 0,23 vs 0,21); la altura sola no se asocia (−0,01) |
| 9 | Completar | Crear `presion_pulso` = sistólica − diastólica | No crearla | Es un indicador clínico de rigidez arterial | Asociación similar a la diastólica (0,38); está muy concentrada en 40 por la cantidad de pacientes con 120/80 |
| 10 | Completar | Crear `habitos_riesgo` (fuma + alcohol + inactivo, 0–3) y `sindrome_metabolico` (colesterol + glucosa, 2–6) sumando con el mismo peso | Ponderar cada componente o no combinarlos | Intento de juntar variables débiles para encontrar una señal más fuerte | `habitos_riesgo` casi no se asocia (V = 0,03). `sindrome_metabolico` (V = 0,20) no supera al colesterol solo (0,22). **Ojo con el nombre:** no es la definición clínica de síndrome metabólico |

## Método de análisis

| # | Fecha | Decisión | Alternativa descartada | Por qué | Efecto esperado en el resultado |
|---|-------|----------|------------------------|---------|-------------------------------|
| 11 | 14/10/2026 | Usar correlación de **Spearman** en lugar de Pearson | Seguir con Pearson | Indicación de la profe. Pearson solo mide relaciones lineales entre variables numéricas; Spearman trabaja por rangos y sirve para variables ordinales (colesterol, glucosa, categorías) | Los valores cambian poco (ap_hi: 0,43 → 0,45) pero el método es adecuado para el tipo de datos |
| 12 | 14/10/2026 | Comparar variables continuas entre pacientes con y sin ECV con **Mann-Whitney U** | t-test de Student | Las variables no son normales (presiones concentradas en valores redondos como 120 o 80) y Mann-Whitney no asume normalidad | Comparación válida; medianas por grupo fáciles de interpretar |
| 13 | 14/10/2026 | Medir variables categóricas con **chi-cuadrado + V de Cramér** y mostrar % de ECV por categoría | Solo correlaciones o solo el p-valor | Permite ver relaciones no lineales, como la curva en J de la presión (hipotensión 28 % vs. normal 22 % de ECV) | Ranking comparable entre variables categóricas |
| 14 | 14/10/2026 | Interpretar **tamaños de efecto** (V de Cramér, rango-biserial) y no los p-valores | Decidir por p < 0,05 | Con 68.618 filas casi cualquier diferencia da significativa, aunque sea irrelevante | Evita concluir que variables como género o alcohol "importan" |
| 15 | 14/10/2026 | Mantener `smoke` y estratificarla por edad para entender su relación negativa con ECV | Eliminarla o "corregirla" | Que fumar parezca proteger va contra la evidencia médica. Se probó si lo explicaban el género o la edad y no alcanzan | Se reporta como limitación del dataset: dato auto-reportado, posible causalidad inversa y no se mide cuánto se fuma |
| 16 | 14/10/2026 | Usar **información mutua** para el ranking final de variables | Ordenar solo por correlación | Capta relaciones no lineales y no monótonas; sirve para elegir las variables del modelo | Top 5: sistólica, categoría de presión, diastólica, presión de pulso, edad |
| 17 | Completar | Usar muestras aleatorias (5.000 y 300 filas, `random_state = 42`) solo para dibujar los gráficos de dispersión; las correlaciones se calculan con todos los datos | Graficar las 68.618 filas | Con todas las filas los puntos se superponen y el gráfico es ilegible; la semilla fija lo hace reproducible | Solo afecta la visualización, no los resultados numéricos |

## Pendientes / a revisar en equipo

- El comentario del código dice `Count final: 68674`, pero corriendo el código actual da **68.618**. Probablemente quedó de una versión anterior: actualizar el comentario.
- Decidir si conviene corregir las presiones con un cero de más (1000 → 100) en vez de eliminarlas, dado que ese grupo tiene mucha más ECV (decisión 3).
- Considerar renombrar `sindrome_metabolico` (por ejemplo, `colesterol_glucosa`) para no confundirlo con el diagnóstico clínico.