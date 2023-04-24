---
title: "Scrapping de la Liga MX"
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

::: {.cell .markdown id="ksXWejKPTVUL"}
# Scrapping los standings de los últimos años de la Liga MX
:::

::: {.cell .markdown id="ypVTEQV9Shaq"}
En el siguiente ipny se automatiza el scrapping de datos de la liga mx
por equipos y por temporadas. Se pueden obtener tantos años como
quieras.

Funciona de la siguiente manera: en la primera tabla se accede a cada
uno de los links de cada uno de los equipos lo que nos lleva a la
segunda tabla (por cada equipo). Esta es la tabla que utilizamos y
además le agregamos un extra de información que es la tabla de
\"Tiros\". Si se quiere se pueden agregar todavía más variables aún para
mejorar el dataset
:::

::: {.cell .markdown id="9W3mXWVYUczM"}
Clacificación de todos los equipos a lo largo de un año especifico
![FullST_beforSC.png](vertopal_3dfee39d9dbf4ac2837d0d45e4f4ce1f/e938f3f80de84643a933adc943276b51c7c2019e.png)
:::

::: {.cell .markdown id="8aLKw1i6U8Od"}
Ejemplo de la información por equipo
![ej_Tigres.png](vertopal_3dfee39d9dbf4ac2837d0d45e4f4ce1f/9165d160505de33067c04630b0ffe35be45be9b1.png)
:::

::: {.cell .markdown id="8NsmzVTBSf8y"}

------------------------------------------------------------------------
:::

::: {.cell .code id="YUOAtoKBWAdy"}
``` python
import requests
from bs4 import BeautifulSoup
import pandas as pd
```
:::

::: {.cell .markdown id="Rgx0huIsNAhN"}
Aquí ponemos el link del año del cual queremos obtener el dataset, si
son varios años hacer el proceso varias veces con los distintos links de
cada año y al final concatenar todos
:::

::: {.cell .code id="P6KCpS7EWBqb"}
``` python
clasificacion_url ="https://fbref.com/es/comps/31/2020-2021/Estadisticas-2020-2021-Liga-MX"
```
:::

::: {.cell .code id="rFtocNDlWbYa"}
``` python
data= requests.get(clasificacion_url)
```
:::

::: {.cell .code id="ESVkY7kPXPRE"}
``` python
soup = BeautifulSoup(data.text)
```
:::

::: {.cell .markdown id="QbhfraP-XeT4"}
Crear nuestro selector para tomar los URLs de la clasificación original
y acceder a cada uno de los equipos
:::

::: {.cell .code id="PyGpNDrIXY5K"}
``` python
clasificacion= soup.select('table.stats_table')[0]
```
:::

::: {.cell .code id="qcPiT9U8YuTN"}
``` python
links = clasificacion.find_all('a')
```
:::

::: {.cell .code id="Z_oyNh4SY1l7"}
``` python
links = [l.get("href") for l in links]
```
:::

::: {.cell .code id="xcjRm3-jfFYM"}
``` python
links= [l for l in links if '/equipos/' in l]
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="G6o5qhdIfWO3" outputId="08424254-d99b-44f5-f7a7-555936204f5c"}
``` python
links
```

::: {.output .execute_result execution_count="90"}
    ['/es/equipos/632f1838/2020-2021/Estadisticas-de-Cruz-Azul',
     '/es/equipos/18d3c3a3/2020-2021/Estadisticas-de-America',
     '/es/equipos/fd7dad55/2020-2021/Estadisticas-de-Leon',
     '/es/equipos/dd5ca9bd/2020-2021/Estadisticas-de-Monterrey',
     '/es/equipos/d9e1bd51/2020-2021/Estadisticas-de-UANL',
     '/es/equipos/03b65ba9/2020-2021/Estadisticas-de-Santos-Laguna',
     '/es/equipos/c9d59c6c/2020-2021/Estadisticas-de-UNAM',
     '/es/equipos/c02b0f7a/2020-2021/Estadisticas-de-Guadalajara',
     '/es/equipos/73fd2313/2020-2021/Estadisticas-de-Puebla',
     '/es/equipos/1be8d2e3/2020-2021/Estadisticas-de-Pachuca',
     '/es/equipos/44b88a4e/2020-2021/Estadisticas-de-Toluca',
     '/es/equipos/7c76bc53/2020-2021/Estadisticas-de-Atlas',
     '/es/equipos/f0297c23/2020-2021/Estadisticas-de-Mazatlan',
     '/es/equipos/a42ddf2f/2020-2021/Estadisticas-de-Tijuana',
     '/es/equipos/752db496/2020-2021/Estadisticas-de-Necaxa',
     '/es/equipos/c3352ce7/2020-2021/Estadisticas-de-Queretaro',
     '/es/equipos/29bff345/2020-2021/Estadisticas-de-FC-Juarez',
     '/es/equipos/5d274ee4/2020-2021/Estadisticas-de-Atletico']
:::
:::

::: {.cell .markdown id="J2DyRuvfNo1e"}
Completamos los links
:::

::: {.cell .code id="w-nK1bA1g8Qd"}
``` python
team_links= [f"https://fbref.com{l}" for l in links]
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="BcdEm-bdhYfz" outputId="3a1021fe-a53f-4794-e73a-6d97d0a3d343"}
``` python
team_links
```

::: {.output .execute_result execution_count="92"}
    ['https://fbref.com/es/equipos/632f1838/2020-2021/Estadisticas-de-Cruz-Azul',
     'https://fbref.com/es/equipos/18d3c3a3/2020-2021/Estadisticas-de-America',
     'https://fbref.com/es/equipos/fd7dad55/2020-2021/Estadisticas-de-Leon',
     'https://fbref.com/es/equipos/dd5ca9bd/2020-2021/Estadisticas-de-Monterrey',
     'https://fbref.com/es/equipos/d9e1bd51/2020-2021/Estadisticas-de-UANL',
     'https://fbref.com/es/equipos/03b65ba9/2020-2021/Estadisticas-de-Santos-Laguna',
     'https://fbref.com/es/equipos/c9d59c6c/2020-2021/Estadisticas-de-UNAM',
     'https://fbref.com/es/equipos/c02b0f7a/2020-2021/Estadisticas-de-Guadalajara',
     'https://fbref.com/es/equipos/73fd2313/2020-2021/Estadisticas-de-Puebla',
     'https://fbref.com/es/equipos/1be8d2e3/2020-2021/Estadisticas-de-Pachuca',
     'https://fbref.com/es/equipos/44b88a4e/2020-2021/Estadisticas-de-Toluca',
     'https://fbref.com/es/equipos/7c76bc53/2020-2021/Estadisticas-de-Atlas',
     'https://fbref.com/es/equipos/f0297c23/2020-2021/Estadisticas-de-Mazatlan',
     'https://fbref.com/es/equipos/a42ddf2f/2020-2021/Estadisticas-de-Tijuana',
     'https://fbref.com/es/equipos/752db496/2020-2021/Estadisticas-de-Necaxa',
     'https://fbref.com/es/equipos/c3352ce7/2020-2021/Estadisticas-de-Queretaro',
     'https://fbref.com/es/equipos/29bff345/2020-2021/Estadisticas-de-FC-Juarez',
     'https://fbref.com/es/equipos/5d274ee4/2020-2021/Estadisticas-de-Atletico']
:::
:::

::: {.cell .markdown id="npEfw-csVyUe"}

------------------------------------------------------------------------
:::

::: {.cell .markdown id="0-vpDYjJVlaE"}
Agarraremos el url de cada equipo de la liga para tomar toda la
información que necesitamos de sus partidos
:::

::: {.cell .code id="Mk5UbLdQi2bC"}
``` python
t1= team_links[0]
t2= team_links[1]
t3= team_links[2]
t4= team_links[3]
t5= team_links[4]
t6= team_links[5]
t7= team_links[6]
t8= team_links[7]
t9= team_links[8]
t10= team_links[9]
t11= team_links[10]
t12= team_links[11]
t13= team_links[12]
t14= team_links[13]
t15= team_links[14]
t16= team_links[15]
t17= team_links[16]
t18= team_links[17]
```
:::

::: {.cell .code id="4xKopQq_i7-I"}
``` python
data1= requests.get(t1)
data2= requests.get(t2)
data3= requests.get(t3)
data4= requests.get(t4)
data5= requests.get(t5)
data6= requests.get(t6)
data7= requests.get(t7)
data8= requests.get(t8)
data9= requests.get(t9)
data10= requests.get(t10)
data11= requests.get(t11)
data12= requests.get(t12)
data13= requests.get(t13)
data14= requests.get(t14)
data15= requests.get(t15)
data16= requests.get(t16)
data17= requests.get(t17)
data18= requests.get(t18)
```
:::

::: {.cell .markdown id="ZdtqvXLANzYb"}
Creamos un dataset por equipo con la información de su respectiva
temporada
:::

::: {.cell .code id="0j5upsztjHaN"}
``` python
matches1= pd.read_html(data1.text,match= "Marcadores y partidos ")[0]
matches2= pd.read_html(data2.text,match= "Marcadores y partidos ")[0]
matches3= pd.read_html(data3.text,match= "Marcadores y partidos ")[0]
matches4= pd.read_html(data4.text,match= "Marcadores y partidos ")[0]
matches5= pd.read_html(data5.text,match= "Marcadores y partidos ")[0]
matches6= pd.read_html(data6.text,match= "Marcadores y partidos ")[0]
matches7= pd.read_html(data7.text,match= "Marcadores y partidos ")[0]
matches8= pd.read_html(data8.text,match= "Marcadores y partidos ")[0]
matches9= pd.read_html(data9.text,match= "Marcadores y partidos ")[0]
matches10= pd.read_html(data10.text,match= "Marcadores y partidos ")[0]
matches11= pd.read_html(data11.text,match= "Marcadores y partidos ")[0]
matches12= pd.read_html(data12.text,match= "Marcadores y partidos ")[0]
matches13= pd.read_html(data13.text,match= "Marcadores y partidos ")[0]
matches14= pd.read_html(data14.text,match= "Marcadores y partidos ")[0]
matches15= pd.read_html(data15.text,match= "Marcadores y partidos ")[0]
matches16= pd.read_html(data16.text,match= "Marcadores y partidos ")[0]
matches17= pd.read_html(data17.text,match= "Marcadores y partidos ")[0]
matches18= pd.read_html(data18.text,match= "Marcadores y partidos ")[0]
```
:::

::: {.cell .code id="Fc8UL35BJJJ-"}
``` python
name1 = team_links[0].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name2 = team_links[1].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name3 = team_links[2].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name4 = team_links[3].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name5 = team_links[4].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name6 = team_links[5].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name7 = team_links[6].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name8 = team_links[7].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name9 = team_links[8].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name10 = team_links[9].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name11= team_links[10].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name12 = team_links[11].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name13 = team_links[12].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name14 = team_links[13].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name15 = team_links[14].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name16 = team_links[15].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name17 = team_links[16].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
name18 = team_links[17].split("/")[-1].replace("Estadisticas-de-", "").replace("-"," ")
```
:::

::: {.cell .code id="AFo385RPKA8Z"}
``` python
matches1["Equipo"]= name1
matches2["Equipo"]= name2
matches3["Equipo"]= name3
matches4["Equipo"]= name4
matches5["Equipo"]= name5
matches6["Equipo"]= name6
matches7["Equipo"]= name7
matches8["Equipo"]= name8
matches9["Equipo"]= name9
matches10["Equipo"]= name10
matches11["Equipo"]= name11
matches12["Equipo"]= name12
matches13["Equipo"]= name13
matches14["Equipo"]= name14
matches15["Equipo"]= name15
matches16["Equipo"]= name16
matches17["Equipo"]= name17
matches18["Equipo"]= name18
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":354}" id="PZ086kTbKNK9" outputId="8a617e34-55fd-4579-e391-a2a2a1e68cd3"}
``` python
matches3.head()
```

::: {.output .execute_result execution_count="98"}
```{=html}

  <div id="df-2d376e42-78ca-47a0-8d72-98aadbf62188">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Fecha</th>
      <th>Hora</th>
      <th>Comp</th>
      <th>Ronda</th>
      <th>Día</th>
      <th>Sedes</th>
      <th>Resultado</th>
      <th>GF</th>
      <th>GC</th>
      <th>Adversario</th>
      <th>xG</th>
      <th>xGA</th>
      <th>Pos.</th>
      <th>Asistencia</th>
      <th>Capitán</th>
      <th>Formación</th>
      <th>Árbitro</th>
      <th>Informe del partido</th>
      <th>Notas</th>
      <th>Equipo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-07-25</td>
      <td>19:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Visitante</td>
      <td>E</td>
      <td>0</td>
      <td>0</td>
      <td>Guadalajara</td>
      <td>1.1</td>
      <td>0.6</td>
      <td>58</td>
      <td>NaN</td>
      <td>Luis Montes</td>
      <td>4-1-3-2</td>
      <td>Jorge Rojas</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Leon</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-08-03</td>
      <td>21:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Lun</td>
      <td>Local</td>
      <td>V</td>
      <td>1</td>
      <td>0</td>
      <td>Monterrey</td>
      <td>0.5</td>
      <td>0.9</td>
      <td>66</td>
      <td>NaN</td>
      <td>Luis Montes</td>
      <td>4-4-2</td>
      <td>Fernando Guerrero</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Leon</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-08</td>
      <td>19:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>2</td>
      <td>Cruz Azul</td>
      <td>1.0</td>
      <td>1.5</td>
      <td>66</td>
      <td>NaN</td>
      <td>Luis Montes</td>
      <td>4-2-3-1</td>
      <td>César Arturo Ramos</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Leon</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-11</td>
      <td>19:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Mar</td>
      <td>Visitante</td>
      <td>V</td>
      <td>1</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>1.7</td>
      <td>0.6</td>
      <td>55</td>
      <td>NaN</td>
      <td>Luis Montes</td>
      <td>4-2-3-1</td>
      <td>Eduardo Basulto</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Leon</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-08-17</td>
      <td>21:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Lun</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>1</td>
      <td>Tijuana</td>
      <td>0.9</td>
      <td>1.4</td>
      <td>61</td>
      <td>NaN</td>
      <td>Luis Montes</td>
      <td>4-2-3-1</td>
      <td>Erick Yair Miranda</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Leon</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-2d376e42-78ca-47a0-8d72-98aadbf62188')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">
        
  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>
      
  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-2d376e42-78ca-47a0-8d72-98aadbf62188 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-2d376e42-78ca-47a0-8d72-98aadbf62188');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>
  
```
:::
:::

::: {.cell .markdown id="LVnsRvlTkX63"}
Aquí ya convertimos el html en una base de datos en pandas por equipo.

Ahora nos damos cuenta que quizás necesitamos un poco más de variables
para hacer un algoritmo predictivo. Tomaremos el link de Tiros por
partido para tener más variables por analizar. Para esto hacemos un
procedimiento análogo al que ya realizamos, vamos a filtrar los links y
agarrar la tabla especifica que necesitamos para concatenarla a nuestro
dataset y tener uno más robusto
:::

::: {.cell .markdown id="Ok4cb84El0aO"}
![tiros.png](vertopal_3dfee39d9dbf4ac2837d0d45e4f4ce1f/8e515964a3b4f7b47a32aeb65d9a6d5829c25fab.png)
:::

::: {.cell .code id="nIbW2YBoDViY"}
``` python
soup1= BeautifulSoup(data1.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data1 = requests.get(f"https://fbref.com{links1[0]}")
tiros1 = pd.read_html(data1.text, match= "Tiros")[0]

soup1= BeautifulSoup(data2.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data2 = requests.get(f"https://fbref.com{links1[0]}")
tiros2 = pd.read_html(data2.text, match= "Tiros")[0]

soup1= BeautifulSoup(data3.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data3 = requests.get(f"https://fbref.com{links1[0]}")
tiros3 = pd.read_html(data3.text, match= "Tiros")[0]

soup1= BeautifulSoup(data4.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data4 = requests.get(f"https://fbref.com{links1[0]}")
tiros4 = pd.read_html(data4.text, match= "Tiros")[0]

soup1= BeautifulSoup(data5.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data5 = requests.get(f"https://fbref.com{links1[0]}")
tiros5 = pd.read_html(data5.text, match= "Tiros")[0]

soup1= BeautifulSoup(data6.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data6 = requests.get(f"https://fbref.com{links1[0]}")
tiros6 = pd.read_html(data6.text, match= "Tiros")[0]

soup1= BeautifulSoup(data7.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data7 = requests.get(f"https://fbref.com{links1[0]}")
tiros7 = pd.read_html(data7.text, match= "Tiros")[0]

soup1= BeautifulSoup(data8.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data8 = requests.get(f"https://fbref.com{links1[0]}")
tiros8 = pd.read_html(data8.text, match= "Tiros")[0]

soup1= BeautifulSoup(data9.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data9 = requests.get(f"https://fbref.com{links1[0]}")
tiros9 = pd.read_html(data9.text, match= "Tiros")[0]

soup1= BeautifulSoup(data10.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data10 = requests.get(f"https://fbref.com{links1[0]}")
tiros10 = pd.read_html(data10.text, match= "Tiros")[0]

soup1= BeautifulSoup(data11.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data11 = requests.get(f"https://fbref.com{links1[0]}")
tiros11 = pd.read_html(data11.text, match= "Tiros")[0]


soup1= BeautifulSoup(data12.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data12 = requests.get(f"https://fbref.com{links1[0]}")
tiros12 = pd.read_html(data12.text, match= "Tiros")[0]

soup1= BeautifulSoup(data13.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data13 = requests.get(f"https://fbref.com{links1[0]}")
tiros13 = pd.read_html(data13.text, match= "Tiros")[0]

soup1= BeautifulSoup(data14.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data14 = requests.get(f"https://fbref.com{links1[0]}")
tiros14 = pd.read_html(data14.text, match= "Tiros")[0]

soup1= BeautifulSoup(data15.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data15 = requests.get(f"https://fbref.com{links1[0]}")
tiros15 = pd.read_html(data15.text, match= "Tiros")[0]

soup1= BeautifulSoup(data16.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data16 = requests.get(f"https://fbref.com{links1[0]}")
tiros16 = pd.read_html(data16.text, match= "Tiros")[0]

soup1= BeautifulSoup(data17.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data17 = requests.get(f"https://fbref.com{links1[0]}")
tiros17 = pd.read_html(data17.text, match= "Tiros")[0]

soup1= BeautifulSoup(data18.text)
links1 = soup1.find_all('a')
links1 = [l.get("href") for l in links1]
links1 = [l for l in links1 if l and 'all_comps/shooting/' in l]
data18 = requests.get(f"https://fbref.com{links1[0]}")
tiros18 = pd.read_html(data18.text, match= "Tiros")[0]
```
:::

::: {.cell .code id="bF4kIZLqls6Q"}
``` python
tiros1.columns= tiros1.columns.droplevel()
tiros2.columns= tiros2.columns.droplevel()
tiros3.columns= tiros3.columns.droplevel()
tiros4.columns= tiros4.columns.droplevel()
tiros5.columns= tiros5.columns.droplevel()
tiros6.columns= tiros6.columns.droplevel()
tiros7.columns= tiros7.columns.droplevel()
tiros8.columns= tiros8.columns.droplevel()
tiros9.columns= tiros9.columns.droplevel()
tiros10.columns= tiros10.columns.droplevel()
tiros11.columns= tiros11.columns.droplevel()
tiros12.columns= tiros12.columns.droplevel()
tiros13.columns= tiros13.columns.droplevel()
tiros14.columns= tiros14.columns.droplevel()
tiros15.columns= tiros15.columns.droplevel()
tiros16.columns= tiros16.columns.droplevel()
tiros17.columns= tiros17.columns.droplevel()
tiros18.columns= tiros18.columns.droplevel()
```
:::

::: {.cell .markdown id="fc4ot48ZOF4v"}
Finalmente mergeamos uno a uno los datasets de cada equipo. Mergeamos el
dataset original con información limitada + la información de tiros por
partido a lo largo de la temporada
:::

::: {.cell .code id="kKVCZvQ-G7gU"}
``` python
team_data1 = matches1.merge(tiros1[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data2 = matches2.merge(tiros2[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data3 = matches3.merge(tiros3[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data4 = matches4.merge(tiros4[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data5 = matches5.merge(tiros5[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data6 = matches6.merge(tiros6[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data7 = matches7.merge(tiros7[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data8 = matches8.merge(tiros8[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data9 = matches9.merge(tiros9[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data10 = matches10.merge(tiros10[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data11 = matches11.merge(tiros11[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data12 = matches12.merge(tiros12[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data13 = matches13.merge(tiros13[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data14 = matches14.merge(tiros14[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data15 = matches15.merge(tiros15[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data16 = matches16.merge(tiros16[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data17 = matches17.merge(tiros17[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
team_data18 = matches18.merge(tiros18[["Fecha","Dis", "DaP", "Dist", "FK", "TP", "TPint"]], on="Fecha")
```
:::

::: {.cell .markdown id="QwN59V8wOV7r"}
Aquí hacemos un append de todos nuestros datasets ya mergeados
:::

::: {.cell .code id="f8p_ETqIIlCc"}
``` python
partidos_completo = []
```
:::

::: {.cell .code id="dPDEF-YILO_D"}
``` python
partidos_completo.append(team_data1)
partidos_completo.append(team_data2)
partidos_completo.append(team_data3)
partidos_completo.append(team_data4)
partidos_completo.append(team_data5)
partidos_completo.append(team_data6)
partidos_completo.append(team_data7)
partidos_completo.append(team_data8)
partidos_completo.append(team_data9)
partidos_completo.append(team_data10)
partidos_completo.append(team_data11)
partidos_completo.append(team_data12)
partidos_completo.append(team_data13)
partidos_completo.append(team_data14)
partidos_completo.append(team_data15)
partidos_completo.append(team_data16)
partidos_completo.append(team_data17)
partidos_completo.append(team_data18)
```
:::

::: {.cell .code id="mWDIWULTOiuY"}
``` python
datasetConcat1 = pd.concat(partidos_completo)
```
:::

::: {.cell .markdown id="PJIWsY1WRdgz"}
Si se hace scrapping de varios años, en este paso poner los distintos
datasets que se van obteniendo en \"frames\" para no sobreescribir sobre
el dataset anterior y poder concatenarlos más adelante
:::

::: {.cell .code id="1ATMOzxnQ8mF"}
``` python
frames = [datasetConcat1]
```
:::

::: {.cell .code id="7UkBkeoBQRwn"}
``` python
LigaMX_ds = pd.concat(frames)
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":658}" id="uspQ-XhMQkEb" outputId="e29b06fd-423d-46e5-ccd9-702eb3c1dce8"}
``` python
LigaMX_ds
```

::: {.output .execute_result execution_count="115"}
```{=html}

  <div id="df-07fa1457-2e63-43fe-841b-9b24e56ae4d8">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Fecha</th>
      <th>Hora</th>
      <th>Comp</th>
      <th>Ronda</th>
      <th>Día</th>
      <th>Sedes</th>
      <th>Resultado</th>
      <th>GF</th>
      <th>GC</th>
      <th>Adversario</th>
      <th>...</th>
      <th>Árbitro</th>
      <th>Informe del partido</th>
      <th>Notas</th>
      <th>Equipo</th>
      <th>Dis</th>
      <th>DaP</th>
      <th>Dist</th>
      <th>FK</th>
      <th>TP</th>
      <th>TPint</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-07-25</td>
      <td>21:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>Santos</td>
      <td>...</td>
      <td>Fernando Hernández</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Cruz Azul</td>
      <td>21</td>
      <td>6</td>
      <td>21.1</td>
      <td>1.0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-07-31</td>
      <td>19:30</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Vie</td>
      <td>Visitante</td>
      <td>E</td>
      <td>1</td>
      <td>1</td>
      <td>Puebla</td>
      <td>...</td>
      <td>Óscar Macias</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Cruz Azul</td>
      <td>24</td>
      <td>7</td>
      <td>17.3</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-08</td>
      <td>19:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>León</td>
      <td>...</td>
      <td>César Arturo Ramos</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Cruz Azul</td>
      <td>15</td>
      <td>3</td>
      <td>17.8</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-12</td>
      <td>17:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Mié</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>1</td>
      <td>Querétaro</td>
      <td>...</td>
      <td>Jorge Rojas</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Cruz Azul</td>
      <td>20</td>
      <td>4</td>
      <td>19.7</td>
      <td>2.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-08-15</td>
      <td>19:00</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>3</td>
      <td>2</td>
      <td>FC Juárez</td>
      <td>...</td>
      <td>Jorge Pérez</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>Cruz Azul</td>
      <td>16</td>
      <td>6</td>
      <td>15.4</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>2022-04-09</td>
      <td>21:00</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Sáb</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>3</td>
      <td>América</td>
      <td>...</td>
      <td>César Arturo Ramos</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>FC Juarez</td>
      <td>4</td>
      <td>1</td>
      <td>22.1</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>2022-04-15</td>
      <td>20:00</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Vie</td>
      <td>Local</td>
      <td>D</td>
      <td>1</td>
      <td>2</td>
      <td>Pachuca</td>
      <td>...</td>
      <td>Óscar Macias</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>FC Juarez</td>
      <td>13</td>
      <td>3</td>
      <td>19.8</td>
      <td>2.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>2022-04-19</td>
      <td>21:00</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Mar</td>
      <td>Visitante</td>
      <td>V</td>
      <td>1</td>
      <td>0</td>
      <td>Toluca</td>
      <td>...</td>
      <td>Victor Caceres</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>FC Juarez</td>
      <td>8</td>
      <td>3</td>
      <td>23.5</td>
      <td>1.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>2022-04-22</td>
      <td>20:00</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Vie</td>
      <td>Local</td>
      <td>D</td>
      <td>0</td>
      <td>2</td>
      <td>Mazatlán</td>
      <td>...</td>
      <td>Oscar Mejia</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>FC Juarez</td>
      <td>5</td>
      <td>0</td>
      <td>27.3</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>33</th>
      <td>2022-04-30</td>
      <td>17:00</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Sáb</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>4</td>
      <td>Querétaro</td>
      <td>...</td>
      <td>Adonai Escobedo</td>
      <td>Informe del partido</td>
      <td>NaN</td>
      <td>FC Juarez</td>
      <td>5</td>
      <td>1</td>
      <td>25.9</td>
      <td>1.0</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>2052 rows × 26 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-07fa1457-2e63-43fe-841b-9b24e56ae4d8')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">
        
  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>
      
  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-07fa1457-2e63-43fe-841b-9b24e56ae4d8 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-07fa1457-2e63-43fe-841b-9b24e56ae4d8');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>
  
```
:::
:::

::: {.cell .markdown id="p0C5cijzUNLC"}
Código para guardar tu dataframe final
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":17}" id="XYVZqb-6UBEJ" outputId="5e7eae75-63d3-47ef-acfe-8d0c08706a20"}
``` python
from google.colab import files
LigaMX_ds.to_csv('output.csv', encoding = 'utf-8-sig') 
files.download('output.csv')
```

::: {.output .display_data}
    <IPython.core.display.Javascript object>
:::

::: {.output .display_data}
    <IPython.core.display.Javascript object>
:::
:::
