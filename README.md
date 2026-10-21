# Análisis de la Polarización y Fragmentación Política en Chile (2021)

## 📌 Resumen Ejecutivo
Este proyecto analiza la relación entre las características socioeconómicas de las 346 comunas de Chile y su comportamiento político en las elecciones parlamentarias de 2021. 

A través de la integración de datos electorales (SERVEL), demográficos (Censo 2024), socioeconómicos (IGVUST, CASEN) y de posición ideológica legislativa (Fábrega), el estudio desmitifica varias hipótesis sobre el voto en Chile:
*   **La Educación y la Polarización:** La polarización legislativa (distancia ideológica entre los diputados de un distrito) está impulsada principalmente por el nivel educativo de la comuna, no por la pobreza.
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
*   **Sistema Electoral:** La polarización se calcula a nivel de *distrito* (28 unidades), ya que los diputados se eligen por distrito, no por comuna. Las comunas heredan el valor de su distrito.
*   **Sesgo del Voto Voluntario:** La elección de 2021 se realizó bajo voto voluntario, lo que sesga la muestra hacia votantes de mayor edad y educación. Los resultados deben interpretarse bajo este contexto.

## 📈 Hallazgos Clave (Regresiones Múltiples)
Se utilizaron regresiones estandarizadas para aislar el efecto real de cada variable (controlando por confusión):
*   **Variable Dependiente: Polarización.** \(R^2\) = 0.113. Único predictor significativo: `promedio_escolaridad` (β = 0.361, p<0.001).
*   **Variable Dependiente: Voto de Derecha.** \(R^2\) = 0.136. Predictores significativos: `tasa_pobreza` (β = 0.338, p<0.001) y `edad_promedio` (β = 0.227, p<0.001).
*   **Variable Dependiente: Fragmentación.** \(R^2\) = 0.109. Predictores significativos: `edad_promedio` (β = -0.226, p<0.001) y `tasa_desocupacion` (β = 0.190, p<0.01).

## ⚙️ Cómo Reproducir este Proyecto
1. Clonar el repositorio.
2. Crear un entorno virtual (`python -m venv venv`).
3. Instalar dependencias: `pip install pandas duckdb matplotlib scipy openpyxl`.
4. Ejecutar los notebooks en la carpeta `notebooks/` en orden secuencial (01 al 05).

*Nota: Todos los datasets crudos y procesados se encuentran en la carpeta `datos/` y `resultados/` respectivamente.*