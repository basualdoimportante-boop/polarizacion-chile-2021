# Análisis de la Polarización y Fragmentación Política en Chile (2021)

## 📌 Resumen Ejecutivo
Este proyecto analiza la relación entre las características socioeconómicas de las 346 comunas de Chile y su comportamiento político en las elecciones parlamentarias de 2021. 

A través de la integración de datos electorales (SERVEL), demográficos (Censo 2024), socioeconómicos (IGVUST, CASEN) y de posición ideológica legislativa (Fábrega), el estudio evalúa hipótesis sobre el voto en Chile, aplicando correcciones metodológicas avanzadas:
*   **Educación y Polarización (Corrección Metodológica):** El análisis inicial a nivel comunal (N=346) sugería que la educación impulsaba la polarización. Sin embargo, tras corregir la pseudoreplicación (la polarización es un dato distrital, no comunal) y ponderar por electores, la educación perdió significancia estadística en el modelo de N=28.
*   **La Pobreza y el Voto de Derecha:** En 2021, las comunas más pobres votaron mayoritariamente por la derecha (Chile Podemos +). La hipótesis de que la migración o la tasa de delitos objetivos empujan el voto a la derecha fue estadísticamente rechazada.
*   **Fragmentación y Edad:** La fragmentación política (número de partidos compitiendo) está determinada por la estructura etaria (comunas más jóvenes = más fragmentadas), mientras que la escolaridad y la ruralidad resultaron ser variables espurias.

## 📊 Fuentes de Datos
El proyecto utiliza exclusivamente datos abiertos y oficiales:
1.  **Resultados Electorales (SERVEL):** Diputados 2021 (16 archivos regionales, ~2.5M de registros por mesa).
2.  **Censo 2024 (INE):** Microdatos procesados con DuckDB para obtener variables demográficas y educativas por comuna.
3.  **Índice Global de Vulnerabilidad Socioterritorial (IGVUST 2026):** Para la segmentación socioeconómica de las comunas en cuartiles.
4.  **Estimaciones de Pobreza Comunal (CASEN 2024):** Vía metodología SAE (Small Area Estimation).
5.  **Ideología Legislativa (Fábrega, 2025):** Datos de roll-call y estimaciones DW-NOMINATE (Harvard Dataverse) para calcular la verdadera polarización por distrito.
6.  **Estadísticas delictuales (CEAD):** Casos policiales por comuna (procesados vía repositorio open-source).

## 🔬 Metodología y Limitaciones
*   **Procesamiento Eficiente:** Se utilizó `DuckDB` para procesar archivos CSV de 2.5 GB (Censo) en segundos, sin saturar la memoria RAM.
*   **Nivel de Análisis:** Ecológico (comunal). Se advierte explícitamente el riesgo de **falacia ecológica** (no se pueden inferir comportamientos individuales a partir de promedios comunales).
*   **Sistema Electoral:** La polarización se calcula a nivel de *distrito* (28 unidades). Para la agregación de variables socioeconómicas, se utilizó una media ponderada por la cantidad de electores (padrón SERVEL 2021) para reflejar el peso real de cada comuna en el distrito.
*   **Sesgo del Voto Voluntario:** La elección de 2021 se realizó bajo voto voluntario. Se calculó la tasa de participación real por comuna (usando el padrón electoral de SERVEL) para controlar este sesgo en los modelos.

## 📈 Hallazgos Clave (Regresiones Múltiples)

**Nota metodológica:** Los modelos de fragmentación y voto de derecha se corrieron a nivel comunal (N=346) con variables estandarizadas. El modelo de polarización original (N=346) fue descartado por pseudoreplicación y recalculado a nivel distrital (N=28) con ponderación por electores.

*   **Variable Dependiente: Fragmentación (N=346).** \(R^2\) = 0.109. Predictores significativos: `edad_promedio` (β = -0.226, p<0.001) y `tasa_desocupacion` (β = 0.190, p<0.01).
*   **Variable Dependiente: Voto de Derecha (N=346).** \(R^2\) = 0.136. Predictores significativos: `tasa_pobreza` (β = 0.338, p<0.001) y `edad_promedio` (β = 0.227, p<0.001).
*   **Variable Dependiente: Polarización (N=28).** Regresión OLS ponderada. El modelo no es significativo en su conjunto (F=2.627, p=0.0735). El \(R^2\) in-sample es 0.247 (Adj. \(R^2\) = 0.153), pero el \(Q^2\) out-of-sample (Leave-One-Out) cae a 0.056, demostrando sobreajuste. Ningún predictor fue significativo (intervalos de confianza cruzando el cero). **Conclusión:** Las variables socioeconómicas no tienen capacidad predictiva sobre la polarización distrital.

## 🧭 Análisis Espacial y Validación (Fase 4)

### Autocorrelación Espacial (Moran's I)
Se calculó el Índice de Moran Global para evaluar si las variables están agrupadas geográficamente. Los resultados confirman una fuerte dependencia espacial:
*   **Polarización:** I = 0.715 (p < 0.001)
*   **Pobreza:** I = 0.721 (p < 0.001)
*   **Voto de Derecha:** I = 0.518 (p < 0.001)
*   **Educación:** I = 0.508 (p < 0.001)

*Esto justifica matemáticamente la necesidad de usar validación espacial, ya que la validación aleatoria inflaría artificialmente el rendimiento del modelo.*

### Modelo Predictivo y Validación Espacial (Block CV)
Se entrenó un modelo de Regresión Ridge para predecir la polarización utilizando variables socioeconómicas. Se compararon dos métodos de validación:
*   **Validación Aleatoria (KFold):** R² = 0.026
*   **Validación Espacial (Block CV por 4 zonas):** R² = -3.464

**Conclusión:** El modelo predictivo basado en variables socioeconómicas **no generaliza a nuevas regiones**. El R² negativo demuestra que la identidad política local y la geografía tienen un peso mayor que las variables del Censo. Un modelo espacial avanzado (como GWR o incluyendo efectos fijos regionales) sería necesario para superar este límite.

## 🛠️ Correcciones Metodológicas (Auditoría Interna)
Durante la revisión del proyecto, se identificaron y corrigieron tres errores metodológicos:
1.  **Pseudoreplicación:** Se asumió erróneamente que las 346 comunas tenían valores independientes de polarización, cuando esta variable solo tiene 28 valores (uno por distrito). Esto inflaba los grados de libertad y los p-valores en los modelos originales. Se corrigió rehaciendo la regresión a nivel distrital.
2.  **Tasa de Participación:** Se usó la población del Censo 2024 como denominador, lo que generó tasas de participación imposibles (>100%) en comunas con alta migración flotante (ej. Torres del Paine). Se corrigió extrayendo la columna `Electores` directamente de SERVEL.
3.  **Datos Faltantes:** A 3 comunas (Paihuano, Trehuaco, Antártica) no se les pudo cruzar el padrón electoral por errores tipográficos en el mapa del INE. Se imputó su tasa de participación usando la mediana nacional.

## ⚙️ Cómo Reproducir este Proyecto
1. Clonar el repositorio.
2. Crear un entorno virtual (`python -m venv venv`).
3. Instalar dependencias: `pip install pandas duckdb matplotlib seaborn scipy scikit-learn statsmodels libpysal esda pyshp openpyxl`.
4. Ejecutar los notebooks en la carpeta `notebooks/` en orden secuencial (01 al 06).

*Nota: Todos los datasets crudos y procesados se encuentran en la carpeta `datos/` y `resultados/` respectivamente.*
