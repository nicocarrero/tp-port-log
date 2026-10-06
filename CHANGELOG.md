# CHANGELOG

# Sprint 2


## [Ejercicio 06]

- Redacción de la conclusión sobre los datos tabulares y las imágenes.
- Guardado de la conclusión en port_log/reports/conclusion_sprint_2.md

## [Ejercicio 05]

- Cálculo de infracciones con y sin imagen asociada.
- Cálculo de imágenes sin match en el dataset.
- Cálculo del ratio promedio de coincidencia de los matches.
- Comparación de la tasa de match entre los grupos `plates` y `completes`.
- Cálculo de infracciones en estado `PENDIENTE` sin evidencia visual.

## [Ejercicio 04]

- Instalación y configuración de EasyOCR para la extracción de matrículas.
- Extracción de matrículas a partir de las imágenes suavizadas.
- Normalización y matching de matrículas con un umbral del 75%.
- Comparación del rendimiento del OCR en cada etapa de preprocesamiento.
- Diagnóstico de las imágenes sin match (originales y suavizadas).
- Incorporación de la matrícula detectada y los datos del matching.
- Actualización de `group_images.json` con `matricula_imagen`.
- Generación de `port_movements_image.csv` con las imágenes asociadas.

## [Ejercicio 03]

- Conversión de las imágenes a escala de grises.
- Aplicación de ecualización adaptativa de histograma mediante CLAHE.
- Aplicación de suavizado gaussiano.
- Detección de bordes mediante Canny.
- Visualización de muestras de cada etapa del preprocesamiento.
- Guardado de las imágenes procesadas en `port_log/data/interim/imgs`.

## [Ejercicio 02]

- Listado de las imágenes disponibles y sus tamaños.
- Separación de las imágenes en los grupos `plates` y `completes`.
- Creación y guardado de `group_images.json` con información de las imágenes.
- Cálculo de resolución, área y tamaño promedio por grupo.
- Creación de la función `mostrar_muestra` para visualizar cada grupo.

## [Ejercicio 01]

- Creación de la rama Sprint_2 a partir de Sprint_1.
- Descarga y descompresión del dataset de imágenes en `port_log/data/raw/imgs`.
- Verificación de los archivos del Sprint 1.
- Conteo de registros de los datasets verificados.
- Actualización del README.md
---

# Sprint 1


## [Ejercicio 07]

- Análisis a modo de conclusión sobre el trabajo realizado, la calidad de los datos y los patrones de infracción.
- Propuesta de un schema y validaciones para mejorar la captura de datos.
- Creación del archivo `port_log/reports/conclusion.md`.

## [Ejercicio 06]

- Cálculo del porcentaje de infracciones con fecha inválida.
- Cálculo del porcentaje de infracciones con hora inválida.
- Identificación del tipo de carga más frecuente.
- Identificación del origen más frecuente de los buques infractores.
- Cálculo de la duración promedio de estadía en muelle.

## [Ejercicio 05]

- Creación de gráficos para el análisis de las infracciones.
- Gráfico del top 10 de matrículas más reincidentes.
- Gráfico de infracciones por turno.
- Gráfico de infracciones por mes.
- Histograma y curva KDE del exceso de velocidad real.
- Gráfico del exceso de velocidad promedio por muelle.
- Gráfico de fechas válidas e inválidas.
- Exportación de los gráficos en formato JPG.

## [Ejercicio 04]

- Creación de la clase `PortAnalyzer`.
- Implementación de métodos para analizar infracciones por matrícula, turno, exceso de velocidad, muelle y tipo de carga.
- Creación del objeto `PortAnalyzer` e invocación de sus métodos.

## [Ejercicio 03]

- Normalización de fechas y horas (y transformación a datetime).
- Cálculo de la duración horas entre ingreso y egreso.
- Normalización de matrículas y muelles.
- Eliminación de registros con nulos en columnas críticas.
- Detección y eliminación de outliers mediante IQR.
- Cálculo del exceso de velocidad real y con tolerancia (5%).
- Eliminación de registros sin infracción.
- Guardado del dataset limpio y del resumen estadístico.

## [Ejercicio 02]
- Descarga y almacenamiento del dataset en `data/raw/port_movements.csv`.
- Análisis de los tipos de datos.
- Identificación de columnas que requieren conversión.
- Análisis de valores nulos y completitud del dataset.

## [Ejercicio 01]

- Inicialización y configuración del repositorio.
- Creación de la rama Sprint_1.
- Configuración del repositorio remoto.
- Creación de la estructura de directorios del proyecto.
- Creación del README.md y del CHANGELOG.md
