# Conclusión

El proyecto construyó, a lo largo de tres sprints, un sistema de control de infracciones portuarias que parte de un registro heredado poco confiable y termina identificando buques a partir de una imagen. En el Sprint 1 se depuró el dataset (de 1500 registros a 459), corrigiendo fechas, horas y matrículas inconsistentes y descartando valores nulos y extremos. En el Sprint 2 se asoció cada infracción con su evidencia fotográfica mediante OCR: 98 de las 100 imágenes superaron el umbral de matching y 443 de las 459 infracciones (96,51 %) quedaron respaldadas por una imagen. En el Sprint 3 el sistema se profesionalizó: los datos pasaron a una base relacional (20 buques, 8 radares, 6 muelles, 459 movimientos y 443 evidencias), los archivos se versionaron con DVC y se incorporó una base vectorial para identificar un buque por su aspecto visual.

La base vectorial resuelve una debilidad del OCR que ya se había visto en el Sprint 2: leer texto es frágil. Un solo carácter mal reconocido (como `0` por `O`), una imagen lejana, oscura o borrosa alcanzan para que el matching falle. La búsqueda por similitud no depende de la patente y devuelve una distancia que funciona como medida de confianza. Por eso no reemplaza al OCR sino que lo complementa en los casos donde este no alcanza.

Aun así, el sistema tiene límites claros:

- **Cobertura vectorial parcial:** solo 16 de los 20 buques (80 %) están indexados, porque se usa únicamente la imagen `complete`, y una sola por buque.
- **Sin umbral de confianza:** la búsqueda siempre devuelve el buque más cercano. Con una imagen `plate`, que difiere de las indexadas, puede dar una identificación incorrecta.
- **Identidad y calidad de datos:** hay 40 `buque_id` para 20 matrículas, por lo que la matrícula funciona como clave aunque depende de una lectura correcta. Persisten 21 fechas inválidas (`1900-01-01`), 16 egresos anteriores al ingreso y 21 movimientos sin radar, lo que limita el análisis temporal.

Para llevarlo al Puerto de Rosario, el foco estaría en prevenir los errores antes de que lleguen a la base y en no depender de un único método de identificación:

- **Calidad desde el origen:** validar los datos al momento de la carga con un schema (formatos, rangos, campos obligatorios, egreso posterior al ingreso) y guardar el `movimiento_id` junto con cada fotografía.
- **Identificación confiable:** usar un identificador oficial del buque (como el número IMO), indexar varias imágenes por buque (incluidas las `plate`) y definir un umbral de distancia validado, de modo que ante la duda el resultado sea "no identificado".
- **Decisión compartida:** combinar OCR y búsqueda visual, con revisión humana de los casos dudosos antes de aplicar una sanción.
- **Operación:** quitar las credenciales del código y usar un gestor de secretos, con una base propia con backups, procesar los registros a medida que llegan desde los radares y sumar tests y monitoreo.
