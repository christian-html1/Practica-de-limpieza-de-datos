# Practica de limpieza de datos
Aplicar técnicas de limpieza y transformación de datos con Python en una base de datos diseñada para contener problemas de calidad.
----------------------------------------------------------------------------------------------------------------------------------------------
En este repositorio muestro los pasos que seguí para limpiar la base de datos usando Python.
El dataset venía con varios problemas de calidad (datos vacíos, errores de texto, tipos de datos mal asignados y fechas con fallas), así que apliqué una serie de transformaciones para dejar la tabla lista para trabajar.

1. Carga inicial y diagnóstico
Lo primero que hice fue cargar el archivo `dirty_cafe_sales.csv` en un DataFrame de Pandas para revisar qué columnas tenían problemas. Me encontré con que venían valores de texto como `"UNKNOWN"`, `"ERROR"` o espacios en blanco repartidos en varias columnas.

2. Estandarización de nulos y tipos de datos
Para poder trabajar bien los datos:
* Reemplacé todas las cadenas `"UNKNOWN"`, `"ERROR"` y textos vacíos por `NaN` de Numpy.
* Convertí las columnas `Quantity`, `Price Per Unit` y `Total Spent` a valores numéricos (`float`), ya que venían leídas como texto por culpa de las palabras con error.
* Convertí la columna `Transaction Date` a formato de fecha (`datetime`).

3. Recuperación de datos con lógica del negocio
En lugar de borrar filas a lo loco, aproveché la relación entre las columnas para recuperar información:
* **Precios y Productos:** Noté que cada producto tiene un precio fijo (por ejemplo, el café siempre vale 2.0). Hice un diccionario con los precios oficiales y lo usé para rellenar los `Item` o `Price Per Unit` que faltaban basándome en el otro dato.
* **Cantidades y Totales:** Rellené las cantidades vacías dividiendo el total entre el precio unitario (`Total Spent / Price Per Unit`). Luego recalculé la columna `Total Spent` multiplicando `Quantity * Price Per Unit` para arreglar inconsistencias de dinero.

### 4. Manejo de métodos de pago y ubicaciones
Para las columnas `Payment Method` y `Location` no había forma matemática de adivinar el dato faltante. Para no borrar casi el 30% del dataset, decidí reemplazar los nulos por la etiqueta `"Desconocido"`. Así mantengo las ventas registradas para el análisis global.

### 5. Limpieza final de fechas y exportación
Filtré las filas donde la fecha venía tan mal que no se pudo convertir (`NaT`). Por último, guardé el resultado final en el archivo `clean_cafe_sales.csv`.

Tabla Resumen de Problemas y Decisiones

| Problema encontrado | Registros afectados | Acción realizada | Justificación |

| **Cadenas no válidas** (`"ERROR"`, `"UNKNOWN"`, espacios) | ~500 - 1,000 filas | Los cambié por valores nulos normales (`np.nan`). | Así puedo convertir las columnas a números o fechas sin que marquen error. |

| **Tipos de datos incorrectos** (números leídos como texto) | Columnas de `Quantity`, `Price Per Unit`, `Total Spent` | Convertí las columnas a tipo numérico con `pd.to_numeric`. | Necesito que sean números para poder hacer cálculos y multiplicar. |

| **Faltantes en `Item` y `Price Per Unit`** | ~333 (`Item`), ~179 (`Price Per Unit`) | Rellené los huecos usando un diccionario con los precios fijos del menú. | Como el precio del producto no cambia, puedo deducir el dato con total seguridad. |

| **Faltantes y errores en `Total Spent` y `Quantity`** | ~173 (`Total Spent`), ~138 (`Quantity`) | Recalculé los valores con la fórmula: $\text{Total} = \text{Cantidad} \times \text{Precio}$. | Corrije los errores de dinero y llena los vacíos sin perder la coherencia. |

| **Faltantes en `Payment Method` y `Location`** | ~2,579 (`Payment Method`), ~3,265 (`Location`) | Les asigné la categoría `"Desconocido"`. | Evita que borre miles de filas y me deja conservar el registro de la venta. |
| **Fechas no válidas** | ~159 filas | Convertí a `datetime` y eliminé los registros sin fecha (`NaT`). | La fecha no se puede adivinar y es un dato clave para analizar ventas en el tiempo. |

Archivos en este repositorio
* `dirty_cafe_sales.csv`: Archivo original con los datos sucios.
* `clean_cafe_sales.csv`: Resultado final con los datos limpios.
* `notebook_cleaning.ipynb`: El cuaderno de Python donde ejecuté todo el código.
* `README.md`: Este archivo explicativo.
