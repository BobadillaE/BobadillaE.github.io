---
title: "Prediciendo resultados de fútbol"
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
En el siguiente notebook se hace un análisis simple de un dataset oficial con las estadísticas oficiales de los resultados de la liga de fútbol mexicano en los últimos años. El objetivo es obtener un modelo predictivo muy simple que nos indique si un equipo ganará o no un partido en base a sus estadisticas anteriores.



``` python
import pandas as pd
```

Importamos nuestro dataset ya scrappeado

``` python
from google.colab import files
 
 
uploaded = files.upload()
```


``` python
matches = pd.read_csv("output.csv", index_col=0) 
```

``` python
matches
```
![Dataset_LigaMX.png](https://raw.githubusercontent.com/BobadillaE/BobadillaE.github.io/master/archivos/Dataset_LigaMX.png)

Análisis rápido de nuestro df

``` python
matches.shape
```


    (2052, 26)

``` python
matches["Equipo"].value_counts()
```

    Cruz Azul        126
    Pachuca          124
    Leon             122
    Puebla           122
    Santos Laguna    121
    UNAM             120
    Atlas            120
    UANL             118
    Guadalajara      118
    America          116
    Monterrey        112
    Toluca           111
    Atletico         106
    Necaxa           105
    Queretaro        104
    Mazatlan         103
    Tijuana          102
    FC Juarez        102
    Name: Equipo, dtype: int64


``` python
matches["Ronda"].value_counts()
```

    Temporada regular del Guardianes 2020    612
    Guardianes 2021 - Temporada regular      612
    Apertura 2021 - Temporada regular        306
    Clausura 2022 - Temporada regular        306
    Cuartos de final                          96
    Semifinales                               48
    Reclasificación                           48
    Finales                                   24
    Name: Ronda, dtype: int64
:::


``` python
matches.dtypes
```

    Fecha                  datetime64[ns]
    Hora                            int64
    Comp                           object
    Ronda                          object
    Día                            object
    Sedes                          object
    Resultado                      object
    GF                             object
    GC                             object
    Adversario                     object
    xG                            float64
    xGA                           float64
    Pos.                            int64
    Asistencia                    float64
    Capitán                        object
    Formación                      object
    Árbitro                        object
    Informe del partido            object
    Notas                          object
    Equipo                         object
    Dis                             int64
    DaP                             int64
    Dist                          float64
    FK                            float64
    TP                              int64
    TPint                           int64
    Sedes_num                        int8
    Adversario_num                   int8
    Dia_num                         int64
    Objetivo                        int64
    dtype: object
:::


Los algoritmos de machine learning trabajan con información númerica, no
con objetos. Necesitamos entonces limpiar nuestra base de datos para
poder usar un alg de machine learning

``` python
matches["Fecha"]= pd.to_datetime(matches["Fecha"])
```


``` python
matches["Sedes_num"]= matches["Sedes"].astype("category").cat.codes
```


``` python
matches["Adversario_num"] = matches["Adversario"].astype("category").cat.codes
```

``` python
matches["Hora"] = matches["Hora"].str.replace(":.+","", regex=True).astype("int")
```

``` python
matches["Dia_num"] = matches["Fecha"].dt.dayofweek
```

``` python
matches["Objetivo"] = (matches["Resultado"] == 'V').astype("int")
```


------------------------------------------------------------------------
:::

Ya estamos listos para nuestro modelo de machine learning
:::


``` python
from sklearn.ensemble import RandomForestClassifier
```

"Imagine you have a complex problem to solve, and you gather a group of
experts from different fields to provide their input. Each expert
provides their opinion based on their expertise and experience. Then,
the experts would vote to arrive at a final decision.

In a random forest classification, multiple decision trees are created
using different random subsets of the data and features. Each decision
tree is like an expert, providing its opinion on how to classify the
data. Predictions are made by calculating the prediction for each
decision tree, then taking the most popular result"

``` python
rf = RandomForestClassifier(n_estimators= 50, min_samples_split=10,random_state=1)
```

``` python
train = matches[matches["Fecha"] <  '2022-01-01']
```

``` python
test = matches[matches["Fecha"] > '2022-01-01']
```

``` python
predictors=["Sedes_num", "Adversario_num", "Hora", "Dia_num"]
```

``` python
rf.fit(train[predictors], train["Objetivo"])
```


``` python
predictions = rf.predict(test[predictors])
```

Ahora, ¿Cómo determinaremos la exactitud o fiabilidad de nuestro modelo?

``` python
from sklearn.metrics import accuracy_score
```

Accuracy_score es una metrica que nos dice si predecimos una victoria,
cuál es el porcentaje de veces en que realmente existe una victoria, al
igual que una derrota

``` python
acc = accuracy_score(test["Objetivo"], predictions)
```

Vamos a explorar más a fondo este resultado. Crearemos una base de datos
que combine nuestras predicciones con la realidad

``` python
combined = pd.DataFrame(dict(actual=test["Objetivo"], predicts = predictions))
```
``` python
pd.crosstab(index=combined["actual"], columns= combined["predicts"])
```


Nos damos cuenta que cuando predecimos una derrota o empate por lo
general lo hacemos bien (159). Sin embargo, cuando predecimos una
victoria nos equivocamos más de lo que acertamos. Hay que revisar de
nuevo con otra metrica

``` python
from sklearn.metrics import precision_score
```

Esta metrica lo que nos dira es cuando predecimos una victoria cual
porcentaje de las veces acertamos
:::

``` python
precision_score(test["Objetivo"], predictions)
```

    0.3870967741935484


Necesitamos mejorar

Crearemos un dataset por equipo para tener un analisis mas profundo

``` python
grouped_matches = matches.groupby("Equipo")
```
:::

::: {.cell .code id="Cecx1cBBjouJ"}
``` python
group = grouped_matches.get_group("Guadalajara")
```

``` python
group
```


Queremos hacer \"roling averages\", por ejemplo si chivas ganó en la
semana 5, como le fue en sus partidos proximos anteriores, y asi le
pasamos esta informacion a nuestro modelo de rf y lo podra usar para
mejorar la calidad de predicciones

``` python
def rolling_avgs(group, cols, new_cols):
  group = group.sort_values("Fecha")
  rolling_stats = group[cols].rolling(3, closed='left').mean()
  group[new_cols] = rolling_stats
  group=group.dropna(subset=new_cols)
  return group
```


Aqui es donde son útiles esas variables extras que añadimos en el
scrapping para tener un analisis mas exhaustivo

``` python
cols= ["Dis", "DaP", "Dist","FK", "TP","TPint"]
```

``` python
new_cols=[f"{c}_rolling" for c in cols]
```
:::

``` python
rolling_avgs(group,cols, new_cols)
```


``` python
matches_rolling = matches.groupby("Equipo").apply(lambda x: rolling_avgs(x, cols, new_cols))
```

``` python
matches_rolling
```


``` python
matches_rolling =matches_rolling.droplevel("Equipo")
```

``` python
def make_predictions(data, predictors):
  train = matches_rolling[matches_rolling["Fecha"] <  '2022-01-01']
  test = matches_rolling[matches_rolling["Fecha"] > '2022-01-01']
  rf.fit(train[predictors], train["Objetivo"])
  predictions = rf.predict(test[predictors])
  combined = pd.DataFrame(dict(actual=test["Objetivo"], predicts = predictions))
  precision= precision_score(test["Objetivo"], predictions)
  return combined, precision
```

``` python
combined, precision = make_predictions(matches_rolling, predictors + new_cols)
```


``` python
precision
```


    0.48333333333333334
:::



``` python
combined
```


Logramos mejorar, pero aún podemos indagar más

``` python
combined= combined.merge(matches_rolling[["Fecha", "Equipo","Adversario", "Resultado"]], left_index= True, right_index=True)
```

``` python
merged = combined.merge(combined, left_on=["Fecha", "NEquipo"], right_on= ["Fecha", "Adversario"])
```

``` python
merged
```


``` python
merged = merged.drop_duplicates()
```

``` python
merged.droplevel
```

    <bound method NDFrame.droplevel of         actual_x  predicts_x      Fecha Equipo_x Adversario_x Resultado_x  \
    0              1           0 2021-01-09    Atlas    Monterrey           D   
    2              1           0 2021-01-09    Atlas    Monterrey           D   
    4              1           0 2021-01-09    Atlas    Monterrey           D   
    6              1           0 2021-01-09    Atlas    Monterrey           D   
    40             0           0 2021-01-09    Atlas    Monterrey           D   
    ...          ...         ...        ...      ...          ...         ...   
    251645         1           1 2022-05-26    Atlas      Pachuca           V   
    251646         1           1 2022-05-26    Atlas      Pachuca           V   
    251647         1           1 2022-05-26    Atlas      Pachuca           V   
    251652         0           0 2022-05-29    Atlas      Pachuca           D   
    251655         0           0 2022-05-29    Atlas      Pachuca           D   

           NEquipo_x  actual_y  predicts_y   Equipo_y Adversario_y Resultado_y  \
    0          Atlas         0           1  Monterrey        Atlas           V   
    2          Atlas         1           1  Monterrey        Atlas           V   
    4          Atlas         0           0  Monterrey        Atlas           V   
    6          Atlas         1           0  Monterrey        Atlas           V   
    40         Atlas         0           1  Monterrey        Atlas           V   
    ...          ...       ...         ...        ...          ...         ...   
    251645     Atlas         0           1    Pachuca        Atlas           D   
    251646     Atlas         1           0    Pachuca        Atlas           D   
    251647     Atlas         0           0    Pachuca        Atlas           D   
    251652     Atlas         0           0    Pachuca        Atlas           V   
    251655     Atlas         1           0    Pachuca        Atlas           V   

            NEquipo_y  
    0       Monterrey  
    2       Monterrey  
    4       Monterrey  
    6       Monterrey  
    40      Monterrey  
    ...           ...  
    251645    Pachuca  
    251646    Pachuca  
    251647    Pachuca  
    251652    Pachuca  
    251655    Pachuca  

    [4914 rows x 13 columns]>


``` python
merged.droplevel
```

::: {.output .execute_result execution_count="147"}
    <bound method NDFrame.droplevel of         actual_x  predicts_x      Fecha Equipo_x Adversario_x Resultado_x  \
    0              1           0 2021-01-09    Atlas    Monterrey           D   
    2              1           0 2021-01-09    Atlas    Monterrey           D   
    4              1           0 2021-01-09    Atlas    Monterrey           D   
    6              1           0 2021-01-09    Atlas    Monterrey           D   
    40             0           0 2021-01-09    Atlas    Monterrey           D   
    ...          ...         ...        ...      ...          ...         ...   
    251645         1           1 2022-05-26    Atlas      Pachuca           V   
    251646         1           1 2022-05-26    Atlas      Pachuca           V   
    251647         1           1 2022-05-26    Atlas      Pachuca           V   
    251652         0           0 2022-05-29    Atlas      Pachuca           D   
    251655         0           0 2022-05-29    Atlas      Pachuca           D   

           NEquipo_x  actual_y  predicts_y   Equipo_y Adversario_y Resultado_y  \
    0          Atlas         0           1  Monterrey        Atlas           V   
    2          Atlas         1           1  Monterrey        Atlas           V   
    4          Atlas         0           0  Monterrey        Atlas           V   
    6          Atlas         1           0  Monterrey        Atlas           V   
    40         Atlas         0           1  Monterrey        Atlas           V   
    ...          ...       ...         ...        ...          ...         ...   
    251645     Atlas         0           1    Pachuca        Atlas           D   
    251646     Atlas         1           0    Pachuca        Atlas           D   
    251647     Atlas         0           0    Pachuca        Atlas           D   
    251652     Atlas         0           0    Pachuca        Atlas           V   
    251655     Atlas         1           0    Pachuca        Atlas           V   

            NEquipo_y  
    0       Monterrey  
    2       Monterrey  
    4       Monterrey  
    6       Monterrey  
    40      Monterrey  
    ...           ...  
    251645    Pachuca  
    251646    Pachuca  
    251647    Pachuca  
    251652    Pachuca  
    251655    Pachuca  

    [4914 rows x 13 columns]>

``` python
merged[(merged["predicts_x"]== 1) & (merged["predicts_y"] == 0)]["actual_x"].value_counts()
```

    1    590
    0    566
    Name: actual_x, dtype: int64


``` python
18605 / (19180 + 18605)
```

    0.49239116051343124

