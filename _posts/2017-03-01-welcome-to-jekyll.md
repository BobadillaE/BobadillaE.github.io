---
title: "Ejemplo"
layout: post
---

::: {.cell .markdown id="mar7vOJ62dj7"}
# Prediccion de Default en Prestamos
:::

::: {.cell .markdown id="nT_Jiy1h2dj-"}
Para este proyecto utilizaremos un sample de los datos de Lending Club.
La idea es predecir si cierto usuario cometera Default basado en
informacion que la plataforma recolecta. Esto nos ayudara a mejorar la
metodologia/pipeline de prestamo.
:::

::: {.cell .markdown id="YnlPKJo72dj_"}
# Descripcion
:::

::: {.cell .markdown id="Skk_zv6H2dj_"}
Contiene los prestamos de esta plataforma:

    periodo 2007-2017Q3.
    887mil observaciones, sample de 100mil
    150 variables
    Target: loan status
:::

::: {.cell .markdown id="yJ-DZvAm2dkA"}
# Objetivo
:::

::: {.cell .markdown id="tju3AIk42dkA"}
Realizar un ETL y un EDA
:::

::: {.cell .markdown id="afx5YAWC2dkA"}
## ETL
:::

::: {.cell .markdown id="CRjy3qOe2dkA"}
1.  Limpia los datos de tal manera que al final del ETL queden en
    formato `tidy`.
2.  Asegurate de cargar y leer los datos
3.  Crea una tabla donde se guarde el nombre de la columna y el tipo de
    dato: (`column_name`, `type`).
4.  Asegurate de pensar cual es el tipo de dato correcto. Porque
    elejiste strig/object o float o int?. No hay respuestas incorrectas
    como tal, pero tienes que justificar tu decision.
5.  Maneja missings o nans de la manera adecuada. Justifica cada
    decision
:::

::: {.cell .markdown id="wcJNlk1H2dkB"}
## EDA
:::

::: {.cell .markdown id="GMNwL3KZ2dkB"}
1.  Preparar lo datos para un pipeline de datos
2.  Quitar columnas inservibles
3.  Imputar valores
4.  Mantener replicabildiad y reproducibilidad
:::

::: {.cell .markdown id="0qjWy7SG2dkB"}
**No olvides anotar tus justificaciones en celdas para recordar cuando
te toque explicarlo.** Puedes agregar el numero de celdas que necesites
para poner tu explicacion y el codigo, solo manten la estructura.
:::

::: {.cell .markdown id="Sg2LDKaO2dkC"}
# ETL {#etl}
:::

::: {.cell .code id="4dKsUgTk2dkC"}
``` python
import pandas as pd
import numpy as np
pd.set_option("display.max_columns", None)
pd.set_option('display.max_colwidth', None)
```
:::

::: {.cell .markdown id="eBVT4UrQ2dkD"}
Vas a obtener 2 errores, solucionalo con los visto en clase.\
Tip: Se arreglan con argumentos adicionales de la funcion `read_csv`\
Documentacion:
<https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html>
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":713}" id="e02f9ZwB2dkE" outputId="1b13340c-52de-41bd-fb28-7f2623cf01f1"}
``` python
loans = pd.read_csv('https://github.com/sonder-art/fdd_prim_2023/blob/main/codigo/pandas/LoansData_sample.csv.gz?raw=true', compression="gzip") 
loans
```