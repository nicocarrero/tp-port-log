# CHANGELOG

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
