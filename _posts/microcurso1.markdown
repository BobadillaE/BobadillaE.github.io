---
title: "Visualización de datos geoespaciales en Python"
layout: page
jupyter:
  colab:
  kernelspec:
    display_name: Python 3
    name: python3
  language_info:
    name: python
  nbformat: 4
  nbformat_minor: 0
---

::: {.cell .markdown id="EPK26OWA3DN9"}
![geopandas_logo_big_1.png](vertopal_b936ac1a9d814ae1aea2c6a91aaab12e/ccd407096eb189eb68aad365417b835ac73a485a.png)
:::

::: {.cell .markdown id="GmXI_URS0O9k"}
\#**¿Qué es GeoPandas?**
:::

::: {.cell .markdown id="iHDPjjvb4KG5"}
**GeoPandas** es un proyecto open source cuyo objetivo es facilitar el
trabajo con **datos geoespaciales**
:::

::: {.cell .markdown id="ZvnfW0-U3ovi"}
*GeoPandas dota a Python de acceso a una serie de funcionalidades
realmente útiles para realizar análisis geoespacial, gestionar
geoinformación y crear salidas cartográficas de nuestros datos
geográficos.*

*Combina las capacidades de pandas y shapely, proporcionando operaciones
geoespaciales en pandas y una interfaz de alto nivel para múltiples
geometrías a shapely. GeoPandas le permite realizar fácilmente
operaciones en python que de otro modo requerirían una base de datos
espacial como PostGIS.*
:::

::: {.cell .markdown id="9bsOUO4v0VKv"}
-   Se basa en la librería **Pandas** de Python, adaptando varias de sus
    clases para dar soporte a datos con geometrías asociadas

-   Además de Pandas, también se sustenta sobre la librería **Shapely**
    que permite llevar a cabo operaciones geoespaciales con las
    geometrías asociadas a esos datos

-   Geopandas depende además de **Fiona** para el acceso a archivos y de
    **Matplotlib** para el plotting
:::

::: {.cell .markdown id="v99ezRPi4-PP"}

------------------------------------------------------------------------
:::

::: {.cell .markdown id="9pURXElf4bOM"}
\#**GeoPandas y Pandas**
:::

::: {.cell .markdown id="sKDQjiw849mU"}
**GeoPandas**, como su nombre indica, amplía la biblioteca de ciencia de
datos **Pandas** añadiendo soporte para datos geoespaciales.

GeoPandas implementa dos estructuras de datos principales: `GeoSeries` y
`GeoDataFrame`. Estas son subclases de `pandas.Series` y
`pandas.DataFrame`, respectivamente.
:::

::: {.cell .markdown id="WjQmFcC-6Aok"}
## **GeoSeries**
:::

::: {.cell .markdown id="FPkGsQ7r6Euj"}
Una `GeoSerie` es esencialmente un vector en el que cada entrada es un
conjunto de formas que corresponden a una observación.

Una entrada puede consistir en una sola forma (como un único polígono) o
en varias formas que deben considerarse como una observación (como los
diferentes polígonos que componen el Estado de Jalisco o un país como
México)
:::

::: {.cell .markdown id="Yqv4bXR17Wkg"}
**GeoPandas** tiene tres clases básicas de objetos geométricos (que en
realidad son objetos de *shapely*):

-   Puntos / Multipuntos (centroides)

-   Líneas / Multi-Líneas

-   Polígonos / Multi-Polígonos
:::

::: {.cell .markdown id="cyVq9tR18yk5"}
Algunos atributos de `GeoSeries`:

-   `area`: área de la forma

-   `bounds`: tupla de coordenadas máximas y mínimas en cada eje para
    cada forma

-   `total_bounds`: tupla de coordenadas máximas y mínimas en cada eje
    para toda la GeoSeries

-   `geom_type`: tipo de geometría

-   `is_valid`: comprueba si las coordenadas forman una figura
    geométrica razonable
:::

::: {.cell .markdown id="nazB-een9jol"}
Algunos metodos de `GeoSeries`

-   `distance()`: devuelve Series con la distancia mínima de cada
    entrada a otra

-   `centroid`: devuelve GeoSeries de centroides

-   `representative_point()`: devuelve GeoSeries de puntos que se
    garantiza que están dentro de cada geometría. NO devuelve
    centroides.

-   `to_crs()`: cambia el sistema de referencia de coordenadas

-   `plot()`: traza GeoSeries

-   `geom_almost_equals()`: te dice si la forma es casi igual a otra

-   `contains()`: te dice si la forma está contenida dentro de otra

-   `intersects()`: te dice si la forma interseca a otra
:::

::: {.cell .markdown id="dnXLvCXC-ZcZ"}
## **GeoDataFrame**
:::

::: {.cell .markdown id="k0NKoLZw921P"}
![dataframe1.jpg](vertopal_b936ac1a9d814ae1aea2c6a91aaab12e/ce848d25877111e2963d31749992d1cc7c11f1c5.jpg)
:::

::: {.cell .markdown id="PzNQJFPM-7Db"}
La estructura de datos central en GeoPandas es `geopandas.GeoDataFrame`,
que puede almacenar columnas de geometría y realizar operaciones
espaciales.

Un `GeoDataFrame` es una estructura de datos tabular que contiene una
`GeoSerie`
:::

::: {.cell .markdown id="yOZw46Bs-6n_"}
El `geopandas.GeoSeries`, como ya se explico, maneja las geometrías. Por
tanto, el `GeoDataFrame` es una combinación de `pandas.Series`, con
datos tradicionales (numéricos, booleanos, texto, etc.), y
`geopandas.GeoSeries`, con geometrías (puntos, polígonos, etc.).

Puede tener tantas columnas con geometrías como desee, cada `GeoSerie`
puede contener cualquier tipo de geometría, sin embargo, sólo una
`GeoSerie` en un `GeoDataFrame` se considera la geometría activa, lo que
significa que todas las operaciones geométricas aplicadas a un
`GeoDataFrame` operan sobre esta columna activa.
:::
