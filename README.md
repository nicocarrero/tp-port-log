
# Port Log

## Sobre el proyecto

Este proyecto aborda el análisis y procesamiento de datos relacionados con movimientos portuarios. Está planificado en tres Sprints: el primero consistió en el análisis y depuración de los registros portuarios, el segundo en el procesamiento de imágenes para relacionarlas con las infracciones registradas, y el tercero en la persistencia de los datos en una base relacional, su versionado con DVC y la identificación de buques por similitud visual.

| Sprint | Estado |
| --- | --- |
| Sprint 1 | Completado |
| Sprint 2 | Completado |
| Sprint 3 | Completado (sprint actual) |

## Estructura del proyecto

## Estructura del proyecto

```text
tp-port-log/
├── .dvc/
├── .dvcignore
├── .gitignore
├── CHANGELOG.md
├── README.md
└── port_log/
    ├── data/
    │   ├── raw/
    │   │   ├── port_movements.csv
    │   │   └── imgs.dvc
    │   ├── interim/
    │   │   ├── port_movements.csv
    │   │   ├── group_images.json
    │   │   ├── plots.dvc
    │   │   └── imgs/
    │   │       ├── 03_01_gray/
    │   │       ├── 03_02_equalized/
    │   │       ├── 03_03_blur/
    │   │       └── 03_04_canny/
    │   └── processed/
    │       └── port_movements_image.csv.dvc
    └── reports/
        ├── summary_sprint1.csv
        ├── conclusion.md
        ├── conclusion_sprint_2.md
        └── conclusion_sprint_3.md
```

Los datos versionados con DVC (`raw/imgs/`, `interim/plots/` y `processed/port_movements_image.csv`) no se guardan en Git: el repositorio solo contiene sus archivos `.dvc`. Se recuperan con `dvc pull`.

- `CHANGELOG.md`: registro de los cambios realizados en cada ejercicio, con lo último primero.
- `.dvc/` y `.dvcignore`: configuración de DVC, que versiona los archivos pesados con Supabase como almacenamiento S3.
- `data/raw/`: datos originales e imágenes (las imágenes están versionadas con DVC mediante `imgs.dvc`).
- `data/interim/`: datos y resultados intermedios (dataset depurado del Sprint 1, metadatos de las imágenes, imágenes de cada etapa de preprocesamiento y gráficos versionados con DVC mediante `plots.dvc`).
- `data/processed/`: dataset final del Sprint 2. `port_movements_image.csv` se versiona con DVC (se almacena en el S3 de Supabase y Git solo guarda `port_movements_image.csv.dvc`); se recupera con `dvc pull`.
- `reports/`: reporte del Sprint 1 y conclusiones de cada sprint.

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

El cruce se realiza por matrícula: a cada infracción se le asigna una imagen de su buque (se prioriza la `complete` y, dentro del grupo, la de mayor coincidencia), por lo que varios movimientos del mismo buque comparten imagen. El dataset final conserva las 459 infracciones.

Archivos generados:
- `data/interim/group_images.json`: metadatos y matrícula detectada de cada imagen.
- `data/interim/imgs/`: imágenes de cada etapa de preprocesamiento.
- `data/processed/port_movements_image.csv`: todas las infracciones con su imagen asociada (vacío si no tienen).

---

## Sprint 3

### Objetivo

Aplicar conocimientos de bases de datos relacionales (SQLAlchemy), versionado de datos (DVC) y búsqueda por similitud visual (ChromaDB) para profesionalizar el sistema portuario.

### Introducción y contexto del problema

El sistema creció en volumen y complejidad. El equipo de desarrollo portuario necesita:

- **Persistencia estructurada:** pasar de archivos CSV a una base de datos relacional con ORM.
- **Auditoría de cambios:** poder rastrear qué versión del dataset se usó en cada análisis.
- **Identificación visual de buques:** dado que el OCR no siempre funciona bien, el sistema debe poder identificar un buque por la similitud visual de su imagen, sin depender del texto.

### Desarrollo

En este Sprint se trabajó con el dataset final del Sprint 2 (`port_movements_image.csv`) y con las imágenes de los buques. Las actividades realizadas fueron:

- Creación de la rama `Sprint_3` a partir de `Sprint_2` y verificación de los datasets de los sprints anteriores.
- Versionado con DVC de las imágenes, los gráficos y el CSV procesado, utilizando Supabase como almacenamiento remoto S3. Git guarda los archivos `.dvc` que referencian cada versión y DVC guarda los datos.
- Diseño del modelo lógico (Buque, Radar, Muelle, Movimiento y Evidencia) con su diagrama entidad-relación y sus cardinalidades.
- Implementación del modelo relacional con SQLAlchemy, con claves primarias, claves foráneas, relaciones y `__repr__`, y de la función `procesar_fila_csv`, que mapea una fila del CSV a las instancias de los modelos.
- Creación de las tablas en PostgreSQL (Supabase) y migración de los datos desde el CSV. Como Supabase ofrece una única base por proyecto (`postgres`), la base `transito_portuario` del enunciado se implementó en ella.
- Consultas con SQLAlchemy: buques con más infracciones, movimientos recientes sin evidencia, radares más activos, reincidentes por período, exceso promedio por muelle y cobertura de evidencia.
- Creación de la base vectorial `buque_vectorial` con ChromaDB y OpenCLIP, con un embedding por buque a partir de su imagen `complete`.
- Implementación de la función `buscar_buque_por_imagen`, que busca el buque más similar a una imagen y recupera sus datos, movimientos y evidencias desde la base relacional.
- Redacción de una conclusión integradora de los tres sprints.

### Resultado

La base relacional quedó con 20 buques, 8 radares, 6 muelles, 459 movimientos y 443 evidencias, es decir que el 96,51 % de las infracciones tiene imagen asociada. Los 109 objetos versionados con DVC (100 imágenes, 6 gráficos, 1 CSV y 2 archivos `.dir`) quedaron sincronizados con el remoto.

La base vectorial contiene 16 de los 20 buques (80 % de cobertura), ya que solo se indexan los buques que tienen una imagen `complete`. La búsqueda por imagen devuelve el buque más similar junto con su distancia y su historial de movimientos. Tiene una limitación: siempre devuelve el buque más cercano, aunque la distancia sea alta, por lo que una imagen `plate` (acercamiento de la patente) puede provocar una identificación incorrecta. Como mejora se propone definir un umbral de distancia validado experimentalmente.

Archivos generados:
- `.dvc/`, `imgs.dvc`, `plots.dvc` y `port_movements_image.csv.dvc`: configuración y referencias de DVC.
- `port_log/reports/conclusion_sprint_3.md`: conclusión integradora del trabajo.
