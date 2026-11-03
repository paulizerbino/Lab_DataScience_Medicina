# Bitácora — Equipo Medicina

## Semana del 17/08 al 23/08

**Qué intentamos:** crear el repositorio, subir el dataset de Kaggle (Cardiovascular Disease) y redactar el README con los datos, el problema y los objetivos.

**Qué falló:** se subió el dataset de manera errónea (.zip), se eliminó y se volvió a subir correctamente.

**Qué decidimos:** trabajar en una rama aparte (Rama-principal) e integrar los cambios por pull request para controlar que cada cambio sea el correcto.

## Semana del 14/09 al 20/09

**Qué intentamos:** subir el gráfico de clase y limpiar los datos: revisar faltantes y duplicados (no había), filtrar valores imposibles y pasar la edad de días a años.

**Qué falló:** subimos por error un archivo del sistema (.DS_Store). Además, los datos traían valores imposibles: presiones de −150 y 16.020 mmHg, diastólica mayor que sistólica, alturas y pesos fuera de rango.

**Qué decidimos:** eliminar esas 1.382 filas (60 por altura y peso, 1.231 por presión diastólica > sistólica, 91 por presiones fuera de rango); convertir la edad de días a años (÷ 365,25; el .25 considera los años bisiestos). El código de limpieza se subió el 22/09.

## Semana del 21/09 al 27/09

**Qué intentamos:** crear variables nuevas: categoría de presión, IMC (categorías según imagen subida), hábitos de riesgo, síndrome metabólico. Sumar la definición de ECV al README y subir los primeros gráficos. Crear mapa de correlación entre todas las variables y cardio.

**Qué falló:** todas las variables daban correlaciones bajas, parecía que ninguna servía para predecir ECV. Los archivos venv y .DS_Store se versionaban.

**Qué decidimos:** usar categorías clínicas (tabla de presión arterial y OMS) en vez de valores sueltos, porque juntan sistólica y diastólica en una variable con sentido médico; agregar un .gitignore para que ignore los archivos.

## Semana del 12/10 al 18/10

**Qué intentamos:** comparamos cada variable por separado, con los pacientes que padecen una ECV (boxplots y curvas), contar factores de riesgo y armar un mapa de riesgo edad × presión.

**Qué falló:** hábitos de riesgo, como fumar, parecen no ser un factor que influye en padecer una ECV. El mapa de correlación de la semana anterior era bajo debido a que se analizaban todos los pacientes, los que padecían la enfermedad y los que no.

**Qué decidimos:** agrupamos cada variable (ej. variable edad: todos los de 50 años) y en cada grupo calculamos el porcentaje de enfermos. Luego correlacionamos ese porcentaje con la variable cardio y obtuvimos relaciones más fuertes.

## Semana del 02/11 al 08/11

**Qué intentamos:** que el repositorio se reproduzca corriendo el script para que otra persona lo pueda clonar correctamente.

**Qué falló:** había una carpeta de gráficos versionada que el script ya genera: archivos que podían quedar desactualizados.

**Qué decidimos:** eliminar la carpeta Graficos; los gráficos se regeneran al correr el script.