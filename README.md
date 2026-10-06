
# Port Log

## Sobre el proyecto

Este proyecto aborda el análisis y procesamiento de datos relacionados con movimientos portuarios. Está planificado en tres Sprints: el primero consistió en el análisis y depuración de los registros portuarios, el segundo en el procesamiento de imágenes para relacionarlas con las infracciones registradas, y el tercero aún no fue desarrollado.

| Sprint | Estado |
| --- | --- |
| Sprint 1 | Completado |
| Sprint 2 | Completado (sprint actual) |
| Sprint 3 | Pendiente |

## Estructura del proyecto

```text
tp-port-log/
├── CHANGELOG.md
├── README.md
└── port_log/
    ├── data/
    │   ├── raw/
    │   │   ├── port_movements.csv
    │   │   └── imgs/
    │   ├── interim/
    │   │   ├── port_movements.csv
    │   │   ├── group_images.json
    │   │   ├── plots/
    │   │   └── imgs/
    │   └── processed/
    │       └── port_movements_image.csv
    └── reports/
        ├── summary_sprint1.csv
        └── conclusion.md
```

- `CHANGELOG.md`: registro de los cambios realizados en cada ejercicio, con lo último primero.
- `data/raw/`: datos originales e imágenes.
- `data/interim/`: datos y resultados intermedios (dataset depurado del Sprint 1, metadatos de las imágenes e imágenes de cada etapa de preprocesamiento).
- `data/processed/`: dataset final del Sprint 2.
- `reports/`: reportes del Sprint 1.

---

## Sprint 1

### Objetivo

Aplicar conocimientos de versionado, organización y análisis exploratorio de datos con pandas sobre un dataset real de operaciones portuarias.

### Introducción y contexto del problema

El Puerto Fluvial de Rosario es uno de los complejos portuarios más importantes de América del Sur, siendo un importante punto de exportación de granos y derivados de Argentina. Diariamente ingresan y egresan buques de distintas banderas con cargas de diverso tipo.

El sistema de registro de movimientos portuarios fue migrado desde un sistema heredado de los años '90. Durante ese período se acumularon inconsistencias en fechas, matrículas de buques y valores numéricos fuera de rango, generando registros que no podían incorporarse directamente al nuevo sistema.

En este Sprint se realizó el análisis y depuración de los datos del sistema antiguo.

Dataset: [port_movements](https://raw.githubusercontent.com/HAD141/datasets/refs/heads/main/TrabajosPracticos/port_log/port_movements.csv)

### Resultado

El dataset original contaba con 1500 registros y 15 columnas. Luego del proceso de limpieza y transformación se obtuvieron 459 registros y 18 columnas.

Se identificaron problemas relacionados con fechas, horas, matrículas, valores nulos y valores extremos. También se analizaron las infracciones según diferentes variables, como turno, muelle, tipo de carga y origen.

Como resultado, se obtuvo un dataset procesado que sirve como base para el desarrollo del Sprint 2.

---

## Sprint 2

### Objetivo

Aplicar conocimientos de tratamiento de imágenes y programación limpia sobre el contexto del sistema portuario.

### Introducción y contexto del problema

Los radares ubicados en los accesos a los muelles capturan evidencia fotográfica de las infracciones de velocidad. Las cámaras asociadas toman fotografías de la zona de proa donde está pintada la matrícula del buque.

En algunos casos, el sistema recorta automáticamente la zona de matrícula (`plates`); en otros, entrega la imagen completa (`completes`).

El sistema presenta las siguientes limitaciones:

- No todas las infracciones tienen imagen asociada.
- No todas las imágenes corresponden a una infracción real.
- Pueden existir errores de detección óptica debido a imágenes borrosas, nocturnas o tomadas a gran distancia.

El objetivo de este Sprint es determinar qué infracciones tienen evidencia visual válida.

Datasets:
- Dataset procesado en el Sprint 1.
- [Dataset de imágenes](https://github.com/HAD141/datasets/raw/refs/heads/main/TrabajosPracticos/port_log/port_log_images.zip) (100 imágenes: 60 `plates` y 40 `completes`).

### Desarrollo

En este Sprint se trabajó con el dataset procesado en el Sprint 1 y con el dataset de imágenes correspondiente. Las actividades realizadas fueron:

- Organización de las imágenes en `plates` y `completes`, y registro de sus metadatos (resolución, área y tamaño) en `group_images.json`.
- Preprocesamiento de imágenes en cuatro etapas: escala de grises, ecualización de histograma adaptativa (CLAHE), suavizado gaussiano y detección de bordes con Canny.
- Extracción de matrículas mediante OCR (EasyOCR).
- Comparación de las matrículas detectadas con las del dataset: se normaliza el texto y se calcula el porcentaje de caracteres iguales en la misma posición, con un umbral del 75 %.
- Asociación de las imágenes con las infracciones correspondientes y generación del dataset final `port_movements_image.csv`.
- Cálculo de métricas sobre el dataset final y análisis de las imágenes que no lograron coincidencia.

### Resultado

De las 100 imágenes, 98 superaron el umbral del 75 %, con un promedio de coincidencia del 96,10 %. De las 459 infracciones, 443 quedaron asociadas a una imagen y 16 no.

El suavizado gaussiano obtuvo el mejor resultado, con 98 coincidencias, por lo que se utilizó para la extracción de matrículas.

La coincidencia fue similar en ambos grupos: 98,33 % en plates y 97,50 % en completes. Las imágenes sin coincidencia se debieron principalmente a matrículas poco visibles por el desenfoque o el tamaño reducido y, en algunos casos, a diferencias entre letras y números que afectaron el matching posición por posición.

El cruce se realiza por matrícula, por lo que una imagen puede asociarse a varios movimientos del mismo buque.

Archivos generados:
- `data/interim/group_images.json`: metadatos y matrícula detectada de cada imagen.
- `data/interim/imgs/`: imágenes de cada etapa de preprocesamiento.
- `data/processed/port_movements_image.csv`: infracciones asociadas a sus imágenes.
