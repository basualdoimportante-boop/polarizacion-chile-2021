# Análisis del Voto y Fragmentación en Chile (2021) y Polarización de Élite (2022-2026)

## 📌 Resumen Ejecutivo
Este proyecto analiza la relación entre las características socioeconómicas de las 346 comunas de Chile y su comportamiento político. Se utilizan los resultados de las elecciones parlamentarias de 2021 (SERVEL) y la polarización legislativa del periodo 2022-2026 (Fábrega), integrando datos demográficos (Censo 2024) y socioeconómicos (IGVUST, CASEN).

*   **Educación y Polarización:** El análisis inicial a nivel comunal sugería que la educación impulsaba la polarización. Sin embargo, tras corregir la pseudoreplicación (la polarización es un dato distrital, no comunal) y ponderar por electores, la educación perdió significancia estadística en el modelo de N=28.
*   **La Pobreza y el Voto de Derecha:** En las comunas que, según CASEN 2024, pertenecen al cuartil más pobre (Q1, N=87 de 346), la suma de los bloques CHILE PODEMOS + y FRENTE SOCIAL CRISTIANO —dos coaliciones distintas en 2021— alcanzó el 37.6% de los votos, sin mayoría absoluta. Si se considera el bloque individual más votado, CHILE PODEMOS + lidera con 25.5%, seguido de APRUEBO DIGNIDAD con 24.6%, una diferencia de 0.85 puntos. No se encontró evidencia de asociación significativa entre la migración o la tasa de delitos objetivos y el voto de derecha al controlar por otras variables.
*   **Fragmentación y Edad:** La fragmentación política está asociada a la estructura etaria (comunas más jóvenes = más fragmentadas).

## 📊 Fuentes de Datos
1.  **Resultados Electorales (SERVEL):** Diputados 2021.
2.  **Censo 2024 (INE):** Microdatos procesados con DuckDB.
3.  **Índice Global de Vulnerabilidad Socioterritorial (IGVUST 2026).**
4.  **Estimaciones de Pobreza Comunal (CASEN 2024).**
5.  **Ideología Legislativa (Fábrega, 2025):** Periodo_6 (2022-2026). *Versión descargada: Octubre 2026.*
6.  **Estadísticas delictuales (CEAD):** Casos 2021, denominador poblacional Censo 2024.

*Nota: Los datos crudos no se versionan por tamaño y licencia.*

## 🔬 Metodología y Limitaciones
*   **Nivel de Análisis:** Ecológico. Se advierte el riesgo de **falacia ecológica**.
*   **Sistema Electoral:** Polarización a nivel distrital (N=28), variables ponderadas por electores.
*   **Desfase Temporal:** Los predictores (CASEN 2024, IGVUST 2026) son posteriores a la elección. Las asociaciones son **espaciales, no temporales ni causales**. La tasa de delitos usa casos de 2021 pero población de 2024.
*   **Voto Voluntario:** Se incluyó la tasa de participación como covariable para controlar parcialmente el efecto del voto voluntario; no se corrige el sesgo de selección.
*   **Fábrega:** Polarización calculada sobre 155 de 157 diputados (2 reemplazantes excluidos sin imputación).
*   **Clasificación de bloques:** Se considera "derecha" a la suma de CHILE PODEMOS + (RN, UDI, Evópoli) y FRENTE SOCIAL CRISTIANO (Partido Republicano, Partido Conservador), que compitieron como pactos separados en 2021. Otros bloques de centro o centro-derecha (PARTIDO DE LA GENTE, INDEPENDIENTES UNIDOS, NUEVO TIEMPO) no se incluyen en esta suma.

## 📈 Descriptivos de los Targets
| Variable | N | Media | Std | Mín | Máx |
|---|---|---|---|---|---|
| Laakso_Taagepera | 346 | 4.67 | 1.04 | 1.86 | 7.70 |
| pct_voto_derecha | 346 | 38.05% | 12.46% | 10.82% | 82.06% |
| polarizacion_distrito | 28 | 1.55 | 0.34 | 0.85 | 1.96 |


## 📈 Hallazgos Clave y Resultados Negativos
*   **Fragmentación (N=346).** R² = 0.109 (Q² LOO = 0.081). Predictores: `edad_promedio` (β = -0.226, p<0.001) y `tasa_desocupacion` (β = 0.190, p<0.01).
*   **Voto de Derecha (N=346).** R² = 0.136 (Q² LOO = 0.092). Predictores: `tasa_pobreza` (β = 0.338, p<0.001) y `edad_promedio` (β = 0.227, p<0.001).
*   **Polarización (N=28).** Regresión OLS ponderada. F=2.627 (p=0.0735). R² in-sample = 0.247; Q² LOO = -0.021. No se detecta asociación significativa; Q² negativo indica nula capacidad predictiva.
    *   *Nota:* El Q² previo (0.056) se calculó sin ponderación; el correcto (WLS) es -0.021.

**Qué no funcionó:**
*   Educación no predice polarización distrital.
*   Migración y delitos no asocian con voto de derecha.
*   Ridge no generaliza espacialmente (Block CV R² = -3.464, partición inestable con N=28).
*   Poder explicativo bajo (Q² < 0.10).

## 🧭 Análisis Espacial
*   **Moran's I:** Polarización (I=0.715, N=28); Pobreza (I=0.721, N=346); Voto Derecha (I=0.518, N=346); Educación (I=0.508, N=346, incluido por completitud). Magnitudes no comparables entre N distintos.
*   **Block CV:** R² = -3.464 (advertencia: baja potencia por N=28 y 4 zonas).

## ⚠️ Consideraciones Éticas
*   Riesgo de microtargeting y manipulación de audiencias.
*   Privacidad: datos agregados, no individuales.
*   Sesgos algorítmicos por subrepresentación en el Censo.

## 🚀 Próximos Pasos
*   Clustering (K-Means) para segmentación territorial.
*   Inferencia ecológica (Flaxman et al., 2015).
*   Extensión a elecciones 2025 (voto obligatorio).

## 👤 Autor y Stack
*   **Autor:** basualdoimportante-boop
*   **Stack:** Python 3.14, pandas, DuckDB, statsmodels, scikit-learn, libpysal, esda, matplotlib, seaborn.
*   **Licencia:** MIT (ver archivo LICENSE).

## ⚙️ Cómo Reproducir este Proyecto
Debido a la consolidación del código, el pipeline actual consta de los siguientes notebooks en la carpeta `notebooks/`:
1.  `01_inspeccion_servel.ipynb` (Procesamiento electoral).
2.  `03_censo_duckdb.ipynb` (Procesamiento demográfico).
3.  `06_correcciones_metodologicas.ipynb` (Análisis, modelado y validación).
*Nota: Los pasos intermedios fueron consolidados en el notebook 06 durante la fase de auditoría.*
*Colocar los archivos crudos en la carpeta `datos/` siguiendo la estructura descrita en el notebook 01.*

Cifra calculada desde dataset_integrado.csv; verificar contra SERVEL oficial antes de citar.
