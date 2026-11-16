# trabajo-ieep
Genera tablas y gráficos de los **ingresos de los Gobiernos Regionales del Perú** (presupuesto inicial, presupuesto modificado y recaudado) por departamento y rubro, a partir de la base abierta del MEF. Une en un solo documento los dos códigos anteriores (`codigo_ieep_2012_2025.Rmd` y `codigo_ieep_2025.Rmd`).

## Herramienta

**R** (R Markdown, salida `html_document`). Librerías clave:

| Librería | Uso |
|---|---|
| `tidyverse` | Lectura (`readr`), manipulación (`dplyr`, `tidyr`) y gráficos (`ggplot2`) |
| `scales` | Formato de números y porcentajes en los gráficos |
| `DT` | Tabla interactiva con totales por departamento |
| `gt` | Tabla final con formato de publicación |
| `webshot2` | Exportar las tablas `gt` como PNG (requiere Chrome o Chromium instalado) |

Para instalarlas, corre una vez el chunk `instalar` (viene con `eval=FALSE`, así que no se ejecuta al tejer).

## Datos de entrada

`comparativo_ingresos_2012_2025.csv`, de datos abiertos del MEF. Si el archivo no está en la carpeta de trabajo, el código lo descarga solo desde:

`https://fs.datosabiertos.mef.gob.pe/datastorefiles/comparativo_ingresos_2012_2025.csv`

Columnas que usa:

- Filtros: `NIVEL_GOBIERNO_NOMBRE`, `SECTOR_NOMBRE`.
- Clasificación: `RUBRO` (código, se lee como texto), `RUBRO_NOMBRE`, y la columna de la unidad (`DEPARTAMENTO_EJECUTORA_NOMBRE` por defecto).
- Montos por año, de 2012 a 2025: `MONTO_PIA_aaaa`, `MONTO_PIM_aaaa` y `MONTO_RECAUDADO_aaaa`.
- Solo en la exploración (opcional): `NIVEL_GOBIERNO`, `SECTOR`, `PLIEGO_NOMBRE`, `FUENTE_FINANC_NOMBRE`.

## Cómo correrlo

1. Abre `codigo_ieep.Rmd` en RStudio.
2. En el chunk `setup`, cambia la ruta de `root.dir` a tu carpeta de trabajo (ahí se guarda el CSV y se escriben las salidas).
3. Revisa los parámetros en el chunk `parametros`.
4. Ejecuta los chunks en orden, o usa **Knit** para generar el HTML completo.

## Parámetros (chunk `parametros`)

| Parámetro | Qué controla |
|---|---|
| `anio_sel` | Año de la tabla (2012-2025) |
| `medidas` | Medidas a usar: `PIA`, `PIM`, `RECAUDADO` |
| `unidad` | Filas: `DEPARTAMENTO_EJECUTORA_NOMBRE` (geográfico) o `PLIEGO_NOMBRE` (solo para validar contra Consulta Amigable) |
| `medida_graf` | Medida que muestran los tres gráficos |
| `divisor` | `1e6` para millones de soles, `1` para soles |
| `digitos` | Decimales de la tabla final |
| `nombres_cortos` | Título de columna por código de rubro (09, 13, 15, 18, 19) |

Si la base trae otro rubro, agrégalo a `nombres_cortos`; si no, saldrá sin nombre corto.

## Qué produce

| Salida | Dónde |
|---|---|
| `tabla_mef.csv`: tabla de departamento x rubro con PIA, PIM y recaudado del año elegido | Carpeta de trabajo |
| `tabla_1.png` y `tabla_2.png`: tabla final dividida en dos láminas (la segunda incluye la fila TOTAL) | Carpeta de trabajo |
| Tabla interactiva con totales por departamento | En pantalla / HTML |
| Control de calidad: compara los totales de la tabla con los de la base (debe dar `TRUE`) | En pantalla / HTML |
| Gráficos: barras apiladas por departamento, pie por rubro y serie histórica 2012-2025 | En pantalla / HTML |

## Estructura del documento

1. Librerías y setup.
2. Carga de la base y parámetros.
3. Exploración (chunk con `eval=FALSE`; se corre a mano una vez).
4. Tabla resumen, control de calidad y tabla con totales.
5. Tabla final y exportación a PNG.
6. Gráficos.

## Cosas fijas en el código

- **Cortes de la tabla final:** `slice(tabla_final, 1:13)` y `14:n_tot` asumen unos 25 departamentos repartidos en dos láminas.
- **Serie histórica:** se elige con `depto_sel` y `rubro_sel` (uno de los dos puede ser `NULL`); el eje usa `2012:2025`.
- **Filtro de mancomunidades:** `SECTOR_NOMBRE == "GOBIERNOS REGIONALES"` deja fuera las mancomunidades regionales.
- **Código de rubro como texto:** `RUBRO` se lee con `col_character()`. Si en la base viniera sin cero inicial ("9" en vez de "09"), hay que ajustar los códigos de `nombres_cortos`.

## Estado

Este documento se armó consolidando dos códigos, pero **no se pudo ejecutar** al crearlo (sin R ni los datos a mano). Corre todo una vez de principio a fin y revisa que el control de calidad dé `TRUE`.
