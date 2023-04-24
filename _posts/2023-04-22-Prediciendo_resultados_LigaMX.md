---
title: "Prediciendo resultados en la Liga MX"
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

::: {.cell .code id="FRDPkaaqTTB3"}
``` python
import pandas as pd
```
:::

::: {.cell .markdown id="T_8d8DT5WkfT"}
Importamos nuestro dataset ya scrappeado
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":73}" id="ckwm7Dh3UTrG" outputId="c3992970-39bc-4b25-cab6-4fcd309f33ab"}
``` python
from google.colab import files
 
 
uploaded = files.upload()
```

::: {.output .display_data}
```{=html}

     <input type="file" id="files-8c649ff5-a32b-4bf2-aff3-a233e2271d66" name="files[]" multiple disabled
        style="border:none" />
     <output id="result-8c649ff5-a32b-4bf2-aff3-a233e2271d66">
      Upload widget is only available when the cell has been executed in the
      current browser session. Please rerun this cell to enable.
      </output>
      <script>// Copyright 2017 Google LLC
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
//      http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

/**
 * @fileoverview Helpers for google.colab Python module.
 */
(function(scope) {
function span(text, styleAttributes = {}) {
  const element = document.createElement('span');
  element.textContent = text;
  for (const key of Object.keys(styleAttributes)) {
    element.style[key] = styleAttributes[key];
  }
  return element;
}

// Max number of bytes which will be uploaded at a time.
const MAX_PAYLOAD_SIZE = 100 * 1024;

function _uploadFiles(inputId, outputId) {
  const steps = uploadFilesStep(inputId, outputId);
  const outputElement = document.getElementById(outputId);
  // Cache steps on the outputElement to make it available for the next call
  // to uploadFilesContinue from Python.
  outputElement.steps = steps;

  return _uploadFilesContinue(outputId);
}

// This is roughly an async generator (not supported in the browser yet),
// where there are multiple asynchronous steps and the Python side is going
// to poll for completion of each step.
// This uses a Promise to block the python side on completion of each step,
// then passes the result of the previous step as the input to the next step.
function _uploadFilesContinue(outputId) {
  const outputElement = document.getElementById(outputId);
  const steps = outputElement.steps;

  const next = steps.next(outputElement.lastPromiseValue);
  return Promise.resolve(next.value.promise).then((value) => {
    // Cache the last promise value to make it available to the next
    // step of the generator.
    outputElement.lastPromiseValue = value;
    return next.value.response;
  });
}

/**
 * Generator function which is called between each async step of the upload
 * process.
 * @param {string} inputId Element ID of the input file picker element.
 * @param {string} outputId Element ID of the output display.
 * @return {!Iterable<!Object>} Iterable of next steps.
 */
function* uploadFilesStep(inputId, outputId) {
  const inputElement = document.getElementById(inputId);
  inputElement.disabled = false;

  const outputElement = document.getElementById(outputId);
  outputElement.innerHTML = '';

  const pickedPromise = new Promise((resolve) => {
    inputElement.addEventListener('change', (e) => {
      resolve(e.target.files);
    });
  });

  const cancel = document.createElement('button');
  inputElement.parentElement.appendChild(cancel);
  cancel.textContent = 'Cancel upload';
  const cancelPromise = new Promise((resolve) => {
    cancel.onclick = () => {
      resolve(null);
    };
  });

  // Wait for the user to pick the files.
  const files = yield {
    promise: Promise.race([pickedPromise, cancelPromise]),
    response: {
      action: 'starting',
    }
  };

  cancel.remove();

  // Disable the input element since further picks are not allowed.
  inputElement.disabled = true;

  if (!files) {
    return {
      response: {
        action: 'complete',
      }
    };
  }

  for (const file of files) {
    const li = document.createElement('li');
    li.append(span(file.name, {fontWeight: 'bold'}));
    li.append(span(
        `(${file.type || 'n/a'}) - ${file.size} bytes, ` +
        `last modified: ${
            file.lastModifiedDate ? file.lastModifiedDate.toLocaleDateString() :
                                    'n/a'} - `));
    const percent = span('0% done');
    li.appendChild(percent);

    outputElement.appendChild(li);

    const fileDataPromise = new Promise((resolve) => {
      const reader = new FileReader();
      reader.onload = (e) => {
        resolve(e.target.result);
      };
      reader.readAsArrayBuffer(file);
    });
    // Wait for the data to be ready.
    let fileData = yield {
      promise: fileDataPromise,
      response: {
        action: 'continue',
      }
    };

    // Use a chunked sending to avoid message size limits. See b/62115660.
    let position = 0;
    do {
      const length = Math.min(fileData.byteLength - position, MAX_PAYLOAD_SIZE);
      const chunk = new Uint8Array(fileData, position, length);
      position += length;

      const base64 = btoa(String.fromCharCode.apply(null, chunk));
      yield {
        response: {
          action: 'append',
          file: file.name,
          data: base64,
        },
      };

      let percentDone = fileData.byteLength === 0 ?
          100 :
          Math.round((position / fileData.byteLength) * 100);
      percent.textContent = `${percentDone}% done`;

    } while (position < fileData.byteLength);
  }

  // All done.
  yield {
    response: {
      action: 'complete',
    }
  };
}

scope.google = scope.google || {};
scope.google.colab = scope.google.colab || {};
scope.google.colab._files = {
  _uploadFiles,
  _uploadFilesContinue,
};
})(self);
</script> 
```
:::

::: {.output .stream .stdout}
    Saving output.csv to output.csv
:::
:::

::: {.cell .code id="9uhaiDPYV5d1"}
``` python
matches = pd.read_csv("output.csv", index_col=0) 
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":641}" id="O16HIT3-WbC3" outputId="6c918241-3544-42f5-c9b4-365e02573a50"}
``` python
matches
```

::: {.output .execute_result execution_count="39"}
```{=html}

  <div id="df-49938cc0-9de3-4125-bfde-a092f9d26117">
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
      <th>Dis</th>
      <th>DaP</th>
      <th>Dist</th>
      <th>FK</th>
      <th>TP</th>
      <th>TPint</th>
      <th>Sedes_num</th>
      <th>Adversario_num</th>
      <th>Dia_num</th>
      <th>Objetivo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-07-25</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>Santos</td>
      <td>...</td>
      <td>21</td>
      <td>6</td>
      <td>21.1</td>
      <td>1.0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>13</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-07-31</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Vie</td>
      <td>Visitante</td>
      <td>E</td>
      <td>1</td>
      <td>1</td>
      <td>Puebla</td>
      <td>...</td>
      <td>24</td>
      <td>7</td>
      <td>17.3</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>11</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-08</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>León</td>
      <td>...</td>
      <td>15</td>
      <td>3</td>
      <td>17.8</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-12</td>
      <td>17</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Mié</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>1</td>
      <td>Querétaro</td>
      <td>...</td>
      <td>20</td>
      <td>4</td>
      <td>19.7</td>
      <td>2.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>12</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-08-15</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>3</td>
      <td>2</td>
      <td>FC Juárez</td>
      <td>...</td>
      <td>16</td>
      <td>6</td>
      <td>15.4</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>5</td>
      <td>1</td>
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
      <td>21</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Sáb</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>3</td>
      <td>América</td>
      <td>...</td>
      <td>4</td>
      <td>1</td>
      <td>22.1</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>2022-04-15</td>
      <td>20</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Vie</td>
      <td>Local</td>
      <td>D</td>
      <td>1</td>
      <td>2</td>
      <td>Pachuca</td>
      <td>...</td>
      <td>13</td>
      <td>3</td>
      <td>19.8</td>
      <td>2.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>10</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>2022-04-19</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Mar</td>
      <td>Visitante</td>
      <td>V</td>
      <td>1</td>
      <td>0</td>
      <td>Toluca</td>
      <td>...</td>
      <td>8</td>
      <td>3</td>
      <td>23.5</td>
      <td>1.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>15</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>32</th>
      <td>2022-04-22</td>
      <td>20</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Vie</td>
      <td>Local</td>
      <td>D</td>
      <td>0</td>
      <td>2</td>
      <td>Mazatlán</td>
      <td>...</td>
      <td>5</td>
      <td>0</td>
      <td>27.3</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>33</th>
      <td>2022-04-30</td>
      <td>17</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Sáb</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>4</td>
      <td>Querétaro</td>
      <td>...</td>
      <td>5</td>
      <td>1</td>
      <td>25.9</td>
      <td>1.0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>12</td>
      <td>5</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>2052 rows × 30 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-49938cc0-9de3-4125-bfde-a092f9d26117')"
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
          document.querySelector('#df-49938cc0-9de3-4125-bfde-a092f9d26117 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-49938cc0-9de3-4125-bfde-a092f9d26117');
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

::: {.cell .markdown id="pSY0sjCsXX8Y"}
Análisis rápido de nuestro df
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="ep8d5qjHXI6D" outputId="03ea307c-8aa1-42de-ae1a-46d31dad8f65"}
``` python
matches.shape
```

::: {.output .execute_result execution_count="14"}
    (2052, 26)
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="Xs84VVk6W3vL" outputId="7122fa8c-fcff-4798-dffd-a5be598d6d0f"}
``` python
matches["Equipo"].value_counts()
```

::: {.output .execute_result execution_count="16"}
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
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="vsEo2HCuXrv1" outputId="2e075130-ac66-45e6-b2cc-671c68115923"}
``` python
matches["Ronda"].value_counts()
```

::: {.output .execute_result execution_count="17"}
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
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="ybLEpO57X_jk" outputId="2b08f949-0560-4a63-c81f-887555eeb8f6"}
``` python
matches.dtypes
```

::: {.output .execute_result execution_count="38"}
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
:::

::: {.cell .markdown id="KIqHH0KHYMmA"}
Los algoritmos de machine learning trabajan con información númerica, no
con objetos. Necesitamos entonces limpiar nuestra base de datos para
poder usar un alg de machine learning
:::

::: {.cell .code id="_z0gn082YdZr"}
``` python
matches["Fecha"]= pd.to_datetime(matches["Fecha"])
```
:::

::: {.cell .code id="yx9UjxOeYtTL"}
``` python
matches["Sedes_num"]= matches["Sedes"].astype("category").cat.codes
```
:::

::: {.cell .code id="OHwM4-pUZYWe"}
``` python
matches["Adversario_num"] = matches["Adversario"].astype("category").cat.codes
```
:::

::: {.cell .code id="zD_r7addZ0Qz"}
``` python
matches["Hora"] = matches["Hora"].str.replace(":.+","", regex=True).astype("int")
```
:::

::: {.cell .code id="ysfq_u7RabMS"}
``` python
matches["Dia_num"] = matches["Fecha"].dt.dayofweek
```
:::

::: {.cell .code id="zv2ERuJibBHa"}
``` python
matches["Objetivo"] = (matches["Resultado"] == 'V').astype("int")
```
:::

::: {.cell .markdown id="KG-kNOYWbfau"}

------------------------------------------------------------------------
:::

::: {.cell .markdown id="Qbxy1tRhbfoP"}
Ya estamos listos para nuestro modelo de machine learning
:::

::: {.cell .code id="F2GycPs_bliZ"}
``` python
from sklearn.ensemble import RandomForestClassifier
```
:::

::: {.cell .markdown id="H8Kj913EdPxS"}
\"Imagine you have a complex problem to solve, and you gather a group of
experts from different fields to provide their input. Each expert
provides their opinion based on their expertise and experience. Then,
the experts would vote to arrive at a final decision.

In a random forest classification, multiple decision trees are created
using different random subsets of the data and features. Each decision
tree is like an expert, providing its opinion on how to classify the
data. Predictions are made by calculating the prediction for each
decision tree, then taking the most popular result\"
:::

::: {.cell .code id="2ri86Z7beIOW"}
``` python
rf = RandomForestClassifier(n_estimators= 50, min_samples_split=10,random_state=1)
```
:::

::: {.cell .code id="6LiLc4laegGN"}
``` python
train = matches[matches["Fecha"] <  '2022-01-01']
```
:::

::: {.cell .code id="u0aBBiXgexb5"}
``` python
test = matches[matches["Fecha"] > '2022-01-01']
```
:::

::: {.cell .code id="0X9ijX4ae6NV"}
``` python
predictors=["Sedes_num", "Adversario_num", "Hora", "Dia_num"]
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":74}" id="gQZel9W-fP53" outputId="8662e99f-9863-49ae-b01f-2c982684446d"}
``` python
rf.fit(train[predictors], train["Objetivo"])
```

::: {.output .execute_result execution_count="47"}
```{=html}
<style>#sk-container-id-1 {color: black;background-color: white;}#sk-container-id-1 pre{padding: 0;}#sk-container-id-1 div.sk-toggleable {background-color: white;}#sk-container-id-1 label.sk-toggleable__label {cursor: pointer;display: block;width: 100%;margin-bottom: 0;padding: 0.3em;box-sizing: border-box;text-align: center;}#sk-container-id-1 label.sk-toggleable__label-arrow:before {content: "▸";float: left;margin-right: 0.25em;color: #696969;}#sk-container-id-1 label.sk-toggleable__label-arrow:hover:before {color: black;}#sk-container-id-1 div.sk-estimator:hover label.sk-toggleable__label-arrow:before {color: black;}#sk-container-id-1 div.sk-toggleable__content {max-height: 0;max-width: 0;overflow: hidden;text-align: left;background-color: #f0f8ff;}#sk-container-id-1 div.sk-toggleable__content pre {margin: 0.2em;color: black;border-radius: 0.25em;background-color: #f0f8ff;}#sk-container-id-1 input.sk-toggleable__control:checked~div.sk-toggleable__content {max-height: 200px;max-width: 100%;overflow: auto;}#sk-container-id-1 input.sk-toggleable__control:checked~label.sk-toggleable__label-arrow:before {content: "▾";}#sk-container-id-1 div.sk-estimator input.sk-toggleable__control:checked~label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-1 div.sk-label input.sk-toggleable__control:checked~label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-1 input.sk-hidden--visually {border: 0;clip: rect(1px 1px 1px 1px);clip: rect(1px, 1px, 1px, 1px);height: 1px;margin: -1px;overflow: hidden;padding: 0;position: absolute;width: 1px;}#sk-container-id-1 div.sk-estimator {font-family: monospace;background-color: #f0f8ff;border: 1px dotted black;border-radius: 0.25em;box-sizing: border-box;margin-bottom: 0.5em;}#sk-container-id-1 div.sk-estimator:hover {background-color: #d4ebff;}#sk-container-id-1 div.sk-parallel-item::after {content: "";width: 100%;border-bottom: 1px solid gray;flex-grow: 1;}#sk-container-id-1 div.sk-label:hover label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-1 div.sk-serial::before {content: "";position: absolute;border-left: 1px solid gray;box-sizing: border-box;top: 0;bottom: 0;left: 50%;z-index: 0;}#sk-container-id-1 div.sk-serial {display: flex;flex-direction: column;align-items: center;background-color: white;padding-right: 0.2em;padding-left: 0.2em;position: relative;}#sk-container-id-1 div.sk-item {position: relative;z-index: 1;}#sk-container-id-1 div.sk-parallel {display: flex;align-items: stretch;justify-content: center;background-color: white;position: relative;}#sk-container-id-1 div.sk-item::before, #sk-container-id-1 div.sk-parallel-item::before {content: "";position: absolute;border-left: 1px solid gray;box-sizing: border-box;top: 0;bottom: 0;left: 50%;z-index: -1;}#sk-container-id-1 div.sk-parallel-item {display: flex;flex-direction: column;z-index: 1;position: relative;background-color: white;}#sk-container-id-1 div.sk-parallel-item:first-child::after {align-self: flex-end;width: 50%;}#sk-container-id-1 div.sk-parallel-item:last-child::after {align-self: flex-start;width: 50%;}#sk-container-id-1 div.sk-parallel-item:only-child::after {width: 0;}#sk-container-id-1 div.sk-dashed-wrapped {border: 1px dashed gray;margin: 0 0.4em 0.5em 0.4em;box-sizing: border-box;padding-bottom: 0.4em;background-color: white;}#sk-container-id-1 div.sk-label label {font-family: monospace;font-weight: bold;display: inline-block;line-height: 1.2em;}#sk-container-id-1 div.sk-label-container {text-align: center;}#sk-container-id-1 div.sk-container {/* jupyter's `normalize.less` sets `[hidden] { display: none; }` but bootstrap.min.css set `[hidden] { display: none !important; }` so we also need the `!important` here to be able to override the default hidden behavior on the sphinx rendered scikit-learn.org. See: https://github.com/scikit-learn/scikit-learn/issues/21755 */display: inline-block !important;position: relative;}#sk-container-id-1 div.sk-text-repr-fallback {display: none;}</style><div id="sk-container-id-1" class="sk-top-container"><div class="sk-text-repr-fallback"><pre>RandomForestClassifier(min_samples_split=10, n_estimators=50, random_state=1)</pre><b>In a Jupyter environment, please rerun this cell to show the HTML representation or trust the notebook. <br />On GitHub, the HTML representation is unable to render, please try loading this page with nbviewer.org.</b></div><div class="sk-container" hidden><div class="sk-item"><div class="sk-estimator sk-toggleable"><input class="sk-toggleable__control sk-hidden--visually" id="sk-estimator-id-1" type="checkbox" checked><label for="sk-estimator-id-1" class="sk-toggleable__label sk-toggleable__label-arrow">RandomForestClassifier</label><div class="sk-toggleable__content"><pre>RandomForestClassifier(min_samples_split=10, n_estimators=50, random_state=1)</pre></div></div></div></div></div>
```
:::
:::

::: {.cell .code id="eJTyAUdpfo15"}
``` python
predictions = rf.predict(test[predictors])
```
:::

::: {.cell .markdown id="NfMNEVXdf9BC"}
Ahora, ¿Cómo determinaremos la exactitud o fiabilidad de nuestro modelo?
:::

::: {.cell .code id="1fOMBGNSgDsZ"}
``` python
from sklearn.metrics import accuracy_score
```
:::

::: {.cell .markdown id="DyE8M-V2gOaa"}
Accuracy_score es una metrica que nos dice si predecimos una victoria,
cuál es el porcentaje de veces en que realmente existe una victoria, al
igual que una derrota
:::

::: {.cell .code id="a-GOe_cWgcoD"}
``` python
acc = accuracy_score(test["Objetivo"], predictions)
```
:::

::: {.cell .markdown id="2eZTARghg6fe"}
Vamos a explorar más a fondo este resultado. Crearemos una base de datos
que combine nuestras predicciones con la realidad
:::

::: {.cell .code id="QtbTD8hphCWE"}
``` python
combined = pd.DataFrame(dict(actual=test["Objetivo"], predicts = predictions))
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":143}" id="OxHWcqz2hgIK" outputId="a3ca11b8-fbc4-4aab-d012-d7e929cb5aeb"}
``` python
pd.crosstab(index=combined["actual"], columns= combined["predicts"])
```

::: {.output .execute_result execution_count="54"}
```{=html}

  <div id="df-533bdaaa-169b-4148-be62-2b412b40c80a">
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
      <th>predicts</th>
      <th>0</th>
      <th>1</th>
    </tr>
    <tr>
      <th>actual</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>159</td>
      <td>57</td>
    </tr>
    <tr>
      <th>1</th>
      <td>90</td>
      <td>36</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-533bdaaa-169b-4148-be62-2b412b40c80a')"
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
          document.querySelector('#df-533bdaaa-169b-4148-be62-2b412b40c80a button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-533bdaaa-169b-4148-be62-2b412b40c80a');
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

::: {.cell .markdown id="lsrwR4cUiDfa"}
Nos damos cuenta que cuando predecimos una derrota o empate por lo
general lo hacemos bien (159). Sin embargo, cuando predecimos una
victoria nos equivocamos más de lo que acertamos. Hay que revisar de
nuevo con otra metrica
:::

::: {.cell .code id="ETI2ZIVLiWkJ"}
``` python
from sklearn.metrics import precision_score
```
:::

::: {.cell .markdown id="1f6M-Jmkie1_"}
Esta metrica lo que nos dira es cuando predecimos una victoria cual
porcentaje de las veces acertamos
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="TLREKFJsicV-" outputId="aaa13b7b-152b-4701-91ae-d790c8ac8bbc"}
``` python
precision_score(test["Objetivo"], predictions)
```

::: {.output .execute_result execution_count="56"}
    0.3870967741935484
:::
:::

::: {.cell .markdown id="dq3GODd6jCq1"}
Necesitamos mejorar
:::

::: {.cell .markdown id="02QCCckzjZAz"}
Crearemos un dataset por equipo para tener un analisis mas profundo
:::

::: {.cell .code id="tNCxU544jE7J"}
``` python
grouped_matches = matches.groupby("Equipo")
```
:::

::: {.cell .code id="Cecx1cBBjouJ"}
``` python
group = grouped_matches.get_group("Guadalajara")
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":641}" id="qO12ZmP-j_Fw" outputId="11417138-27ef-47cf-86ba-327e3f4330c8"}
``` python
group
```

::: {.output .execute_result execution_count="85"}
```{=html}

  <div id="df-3414b12d-7eed-4642-a018-8223f5bf5347">
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
      <th>Dis</th>
      <th>DaP</th>
      <th>Dist</th>
      <th>FK</th>
      <th>TP</th>
      <th>TPint</th>
      <th>Sedes_num</th>
      <th>Adversario_num</th>
      <th>Dia_num</th>
      <th>Objetivo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-07-25</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>E</td>
      <td>0</td>
      <td>0</td>
      <td>León</td>
      <td>...</td>
      <td>11</td>
      <td>4</td>
      <td>20.6</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-08-02</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Dom</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>2</td>
      <td>Santos</td>
      <td>...</td>
      <td>13</td>
      <td>4</td>
      <td>19.6</td>
      <td>1.0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>13</td>
      <td>6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-08</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>D</td>
      <td>0</td>
      <td>1</td>
      <td>Puebla</td>
      <td>...</td>
      <td>12</td>
      <td>2</td>
      <td>21.2</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>11</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-12</td>
      <td>18</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Mié</td>
      <td>Visitante</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>FC Juárez</td>
      <td>...</td>
      <td>6</td>
      <td>2</td>
      <td>16.6</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-08-15</td>
      <td>17</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>1</td>
      <td>Atlético</td>
      <td>...</td>
      <td>15</td>
      <td>6</td>
      <td>18.2</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>5</td>
      <td>1</td>
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
      <th>33</th>
      <td>2022-04-23</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>3</td>
      <td>1</td>
      <td>UNAM</td>
      <td>...</td>
      <td>9</td>
      <td>6</td>
      <td>20.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>17</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>34</th>
      <td>2022-04-29</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Vie</td>
      <td>Visitante</td>
      <td>V</td>
      <td>1</td>
      <td>0</td>
      <td>Necaxa</td>
      <td>...</td>
      <td>9</td>
      <td>3</td>
      <td>19.9</td>
      <td>1.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>35</th>
      <td>2022-05-08</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Reclasificación</td>
      <td>Dom</td>
      <td>Local</td>
      <td>V</td>
      <td>4</td>
      <td>1</td>
      <td>UNAM</td>
      <td>...</td>
      <td>9</td>
      <td>7</td>
      <td>16.2</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>17</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>36</th>
      <td>2022-05-12</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Cuartos de final</td>
      <td>Jue</td>
      <td>Local</td>
      <td>D</td>
      <td>1</td>
      <td>2</td>
      <td>Atlas</td>
      <td>...</td>
      <td>12</td>
      <td>4</td>
      <td>21.3</td>
      <td>1.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2022-05-15</td>
      <td>18</td>
      <td>Liga MX</td>
      <td>Cuartos de final</td>
      <td>Dom</td>
      <td>Visitante</td>
      <td>E</td>
      <td>1</td>
      <td>1</td>
      <td>Atlas</td>
      <td>...</td>
      <td>19</td>
      <td>4</td>
      <td>20.4</td>
      <td>3.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>6</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>118 rows × 30 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-3414b12d-7eed-4642-a018-8223f5bf5347')"
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
          document.querySelector('#df-3414b12d-7eed-4642-a018-8223f5bf5347 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-3414b12d-7eed-4642-a018-8223f5bf5347');
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

::: {.cell .markdown id="jXn-niAkkENw"}
Queremos hacer \"roling averages\", por ejemplo si chivas ganó en la
semana 5, como le fue en sus partidos proximos anteriores, y asi le
pasamos esta informacion a nuestro modelo de rf y lo podra usar para
mejorar la calidad de predicciones
:::

::: {.cell .code id="ric7dpcpkDrf"}
``` python
def rolling_avgs(group, cols, new_cols):
  group = group.sort_values("Fecha")
  rolling_stats = group[cols].rolling(3, closed='left').mean()
  group[new_cols] = rolling_stats
  group=group.dropna(subset=new_cols)
  return group
```
:::

::: {.cell .markdown id="RY8-lZX5mKKI"}
Aqui es donde son útiles esas variables extras que añadimos en el
scrapping para tener un analisis mas exhaustivo
:::

::: {.cell .code id="j_a41_m4lTfE"}
``` python
cols= ["Dis", "DaP", "Dist","FK", "TP","TPint"]
```
:::

::: {.cell .code id="aOFhco2MmdXS"}
``` python
new_cols=[f"{c}_rolling" for c in cols]
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":869}" id="zhX9kBmRmxa0" outputId="9e007886-93b6-4861-dcea-d96637ff210d"}
``` python
rolling_avgs(group,cols, new_cols)
```

::: {.output .execute_result execution_count="94"}
```{=html}

  <div id="df-2a2cf461-5b89-493c-a2ba-cac944a1b826">
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
      <th>Sedes_num</th>
      <th>Adversario_num</th>
      <th>Dia_num</th>
      <th>Objetivo</th>
      <th>Dis_rolling</th>
      <th>DaP_rolling</th>
      <th>Dist_rolling</th>
      <th>FK_rolling</th>
      <th>TP_rolling</th>
      <th>TPint_rolling</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2020-08-02</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Dom</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>2</td>
      <td>Santos</td>
      <td>...</td>
      <td>1</td>
      <td>13</td>
      <td>6</td>
      <td>0</td>
      <td>11.666667</td>
      <td>4.000000</td>
      <td>20.266667</td>
      <td>0.333333</td>
      <td>0.000000</td>
      <td>0.333333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-08</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>D</td>
      <td>0</td>
      <td>1</td>
      <td>Puebla</td>
      <td>...</td>
      <td>0</td>
      <td>11</td>
      <td>5</td>
      <td>0</td>
      <td>12.333333</td>
      <td>4.000000</td>
      <td>19.933333</td>
      <td>0.666667</td>
      <td>0.000000</td>
      <td>0.666667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-08</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>D</td>
      <td>0</td>
      <td>1</td>
      <td>Puebla</td>
      <td>...</td>
      <td>0</td>
      <td>11</td>
      <td>5</td>
      <td>0</td>
      <td>12.666667</td>
      <td>3.333333</td>
      <td>20.133333</td>
      <td>0.666667</td>
      <td>0.000000</td>
      <td>0.666667</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-12</td>
      <td>18</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Mié</td>
      <td>Visitante</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>FC Juárez</td>
      <td>...</td>
      <td>1</td>
      <td>4</td>
      <td>2</td>
      <td>1</td>
      <td>12.333333</td>
      <td>2.666667</td>
      <td>20.666667</td>
      <td>0.333333</td>
      <td>0.000000</td>
      <td>0.333333</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-12</td>
      <td>18</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Mié</td>
      <td>Visitante</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>FC Juárez</td>
      <td>...</td>
      <td>1</td>
      <td>4</td>
      <td>2</td>
      <td>1</td>
      <td>10.000000</td>
      <td>2.000000</td>
      <td>19.666667</td>
      <td>0.333333</td>
      <td>0.333333</td>
      <td>0.333333</td>
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
      <th>33</th>
      <td>2022-04-23</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>3</td>
      <td>1</td>
      <td>UNAM</td>
      <td>...</td>
      <td>0</td>
      <td>17</td>
      <td>5</td>
      <td>1</td>
      <td>13.000000</td>
      <td>4.000000</td>
      <td>21.433333</td>
      <td>0.333333</td>
      <td>0.333333</td>
      <td>0.333333</td>
    </tr>
    <tr>
      <th>34</th>
      <td>2022-04-29</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Vie</td>
      <td>Visitante</td>
      <td>V</td>
      <td>1</td>
      <td>0</td>
      <td>Necaxa</td>
      <td>...</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>1</td>
      <td>12.666667</td>
      <td>5.333333</td>
      <td>20.133333</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>35</th>
      <td>2022-05-08</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Reclasificación</td>
      <td>Dom</td>
      <td>Local</td>
      <td>V</td>
      <td>4</td>
      <td>1</td>
      <td>UNAM</td>
      <td>...</td>
      <td>0</td>
      <td>17</td>
      <td>6</td>
      <td>1</td>
      <td>11.333333</td>
      <td>4.666667</td>
      <td>19.700000</td>
      <td>0.333333</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>36</th>
      <td>2022-05-12</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Cuartos de final</td>
      <td>Jue</td>
      <td>Local</td>
      <td>D</td>
      <td>1</td>
      <td>2</td>
      <td>Atlas</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>9.000000</td>
      <td>5.333333</td>
      <td>18.700000</td>
      <td>0.333333</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2022-05-15</td>
      <td>18</td>
      <td>Liga MX</td>
      <td>Cuartos de final</td>
      <td>Dom</td>
      <td>Visitante</td>
      <td>E</td>
      <td>1</td>
      <td>1</td>
      <td>Atlas</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>6</td>
      <td>0</td>
      <td>10.000000</td>
      <td>4.666667</td>
      <td>19.133333</td>
      <td>0.666667</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>115 rows × 36 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-2a2cf461-5b89-493c-a2ba-cac944a1b826')"
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
          document.querySelector('#df-2a2cf461-5b89-493c-a2ba-cac944a1b826 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-2a2cf461-5b89-493c-a2ba-cac944a1b826');
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

::: {.cell .code id="oXP_EKP-qrCN"}
``` python
matches_rolling = matches.groupby("Equipo").apply(lambda x: rolling_avgs(x, cols, new_cols))
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":904}" id="t40UDMqmq--A" outputId="fca092e9-b16c-444c-9d4e-5f8acace0b81"}
``` python
matches_rolling
```

::: {.output .execute_result execution_count="109"}
```{=html}

  <div id="df-c1d2b630-1429-4bdb-948e-6818eeb78167">
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
      <th>Sedes_num</th>
      <th>Adversario_num</th>
      <th>Dia_num</th>
      <th>Objetivo</th>
      <th>Dis_rolling</th>
      <th>DaP_rolling</th>
      <th>Dist_rolling</th>
      <th>FK_rolling</th>
      <th>TP_rolling</th>
      <th>TPint_rolling</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2020-08-01</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Sáb</td>
      <td>Local</td>
      <td>V</td>
      <td>4</td>
      <td>0</td>
      <td>Tijuana</td>
      <td>...</td>
      <td>0</td>
      <td>14</td>
      <td>5</td>
      <td>1</td>
      <td>15.333333</td>
      <td>7.000000</td>
      <td>20.966667</td>
      <td>1.333333</td>
      <td>0.666667</td>
      <td>0.666667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-07</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Vie</td>
      <td>Visitante</td>
      <td>E</td>
      <td>1</td>
      <td>1</td>
      <td>Necaxa</td>
      <td>...</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>0</td>
      <td>18.666667</td>
      <td>8.000000</td>
      <td>20.833333</td>
      <td>1.666667</td>
      <td>0.333333</td>
      <td>0.333333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-08-07</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Vie</td>
      <td>Visitante</td>
      <td>E</td>
      <td>1</td>
      <td>1</td>
      <td>Necaxa</td>
      <td>...</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>0</td>
      <td>17.333333</td>
      <td>6.666667</td>
      <td>17.666667</td>
      <td>1.333333</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-13</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Jue</td>
      <td>Local</td>
      <td>V</td>
      <td>3</td>
      <td>1</td>
      <td>Santos</td>
      <td>...</td>
      <td>0</td>
      <td>13</td>
      <td>3</td>
      <td>1</td>
      <td>12.666667</td>
      <td>4.333333</td>
      <td>14.633333</td>
      <td>0.666667</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-08-13</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Temporada regular del Guardianes 2020</td>
      <td>Jue</td>
      <td>Local</td>
      <td>V</td>
      <td>3</td>
      <td>1</td>
      <td>Santos</td>
      <td>...</td>
      <td>0</td>
      <td>13</td>
      <td>3</td>
      <td>1</td>
      <td>10.333333</td>
      <td>3.666667</td>
      <td>13.300000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
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
      <th>35</th>
      <td>2022-04-17</td>
      <td>12</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Dom</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>Monterrey</td>
      <td>...</td>
      <td>0</td>
      <td>8</td>
      <td>6</td>
      <td>1</td>
      <td>18.333333</td>
      <td>6.333333</td>
      <td>19.133333</td>
      <td>0.666667</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>36</th>
      <td>2022-04-20</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Mié</td>
      <td>Visitante</td>
      <td>D</td>
      <td>0</td>
      <td>2</td>
      <td>Atlético</td>
      <td>...</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>16.333333</td>
      <td>6.666667</td>
      <td>21.266667</td>
      <td>0.666667</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2022-04-23</td>
      <td>21</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Sáb</td>
      <td>Visitante</td>
      <td>D</td>
      <td>1</td>
      <td>3</td>
      <td>Guadalajara</td>
      <td>...</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>0</td>
      <td>14.000000</td>
      <td>4.333333</td>
      <td>22.000000</td>
      <td>0.333333</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>38</th>
      <td>2022-05-01</td>
      <td>12</td>
      <td>Liga MX</td>
      <td>Clausura 2022 - Temporada regular</td>
      <td>Dom</td>
      <td>Local</td>
      <td>V</td>
      <td>2</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>...</td>
      <td>0</td>
      <td>10</td>
      <td>6</td>
      <td>1</td>
      <td>13.000000</td>
      <td>4.333333</td>
      <td>24.300000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>39</th>
      <td>2022-05-08</td>
      <td>19</td>
      <td>Liga MX</td>
      <td>Reclasificación</td>
      <td>Dom</td>
      <td>Visitante</td>
      <td>D</td>
      <td>1</td>
      <td>4</td>
      <td>Guadalajara</td>
      <td>...</td>
      <td>1</td>
      <td>5</td>
      <td>6</td>
      <td>0</td>
      <td>13.333333</td>
      <td>4.333333</td>
      <td>22.500000</td>
      <td>0.333333</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>1981 rows × 36 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-c1d2b630-1429-4bdb-948e-6818eeb78167')"
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
          document.querySelector('#df-c1d2b630-1429-4bdb-948e-6818eeb78167 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-c1d2b630-1429-4bdb-948e-6818eeb78167');
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

::: {.cell .code id="bVjrvWWjrFMr"}
``` python
matches_rolling =matches_rolling.droplevel("Equipo")
```
:::

::: {.cell .code id="fz2r0vP-rbK6"}
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
:::

::: {.cell .code id="D8_vg1cyr_1V"}
``` python
combined, precision = make_predictions(matches_rolling, predictors + new_cols)
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="GmmwH1pisigX" outputId="2a6dcb09-ab57-4906-dd7d-4b5833330ab5"}
``` python
precision
```

::: {.output .execute_result execution_count="105"}
    0.48333333333333334
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":423}" id="zDFYyPO3sjDN" outputId="7c32998d-37d0-4597-8395-7c8da5431c4b"}
``` python
combined
```

::: {.output .execute_result execution_count="106"}
```{=html}

  <div id="df-133a8e82-57c8-4743-873a-c6e37ea66557">
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
      <th>actual</th>
      <th>predicts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>19</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>21</th>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>22</th>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>35</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>36</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>336 rows × 2 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-133a8e82-57c8-4743-873a-c6e37ea66557')"
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
          document.querySelector('#df-133a8e82-57c8-4743-873a-c6e37ea66557 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-133a8e82-57c8-4743-873a-c6e37ea66557');
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

::: {.cell .markdown id="GcRJMRcpsqB1"}
Logramos mejorar, pero aún podemos indagar más
:::

::: {.cell .code id="P36ujFn_sycm"}
``` python
combined= combined.merge(matches_rolling[["Fecha", "Equipo","Adversario", "Resultado"]], left_index= True, right_index=True)
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":833}" id="rROiwzSWtEmi" outputId="ab5589c7-a45d-4731-fa9a-c4aabf5ebf9b"}
``` python
combined
```

::: {.output .execute_result execution_count="110"}
```{=html}

  <div id="df-7779e3e5-ee55-4fd6-b4c9-6c9ff1b10965">
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
      <th>actual</th>
      <th>predicts</th>
      <th>Fecha</th>
      <th>Equipo</th>
      <th>Adversario</th>
      <th>Resultado</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2020-11-25</td>
      <td>America</td>
      <td>Guadalajara</td>
      <td>D</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2020-11-25</td>
      <td>America</td>
      <td>Guadalajara</td>
      <td>D</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2021-11-24</td>
      <td>America</td>
      <td>UNAM</td>
      <td>E</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>43</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-21</td>
      <td>Atlas</td>
      <td>UANL</td>
      <td>D</td>
    </tr>
    <tr>
      <th>43</th>
      <td>0</td>
      <td>0</td>
      <td>2021-05-30</td>
      <td>Cruz Azul</td>
      <td>Santos</td>
      <td>E</td>
    </tr>
    <tr>
      <th>43</th>
      <td>0</td>
      <td>0</td>
      <td>2021-05-30</td>
      <td>Cruz Azul</td>
      <td>Santos</td>
      <td>E</td>
    </tr>
    <tr>
      <th>44</th>
      <td>1</td>
      <td>1</td>
      <td>2022-05-26</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>V</td>
    </tr>
    <tr>
      <th>45</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
    </tr>
  </tbody>
</table>
<p>16219 rows × 6 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-7779e3e5-ee55-4fd6-b4c9-6c9ff1b10965')"
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
          document.querySelector('#df-7779e3e5-ee55-4fd6-b4c9-6c9ff1b10965 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-7779e3e5-ee55-4fd6-b4c9-6c9ff1b10965');
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

::: {.cell .code id="e1kEh6tNuRt4"}
``` python
class MissingDict(dict):
  __missing__ =lambda self, key:key

map_values ={
    "León":"Leon",
    "FC Juárez": "FC Juarez",
    "Queretáro":"Queretaro",
    "América":"America",
    "Atlético": "Atletico",
    "Mazatlán":"Mazatlan"
}

mapping= MissingDict(**map_values)
```
:::

::: {.cell .code id="8dbHEoNiwAaV"}
``` python
combined["NEquipo"]= combined["Equipo"].map(mapping)
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":423}" id="9Ca63WHmwQSx" outputId="6d061bc5-0628-4e3a-f650-1ec860a64a10"}
``` python
combined
```

::: {.output .execute_result execution_count="122"}
```{=html}

  <div id="df-0e8921fa-bb27-45c4-8e79-5c80ea44cfcc">
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
      <th>actual</th>
      <th>predicts</th>
      <th>Fecha</th>
      <th>Equipo</th>
      <th>Adversario</th>
      <th>Resultado</th>
      <th>NEquipo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2020-11-25</td>
      <td>America</td>
      <td>Guadalajara</td>
      <td>D</td>
      <td>America</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2020-11-25</td>
      <td>America</td>
      <td>Guadalajara</td>
      <td>D</td>
      <td>America</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2021-11-24</td>
      <td>America</td>
      <td>UNAM</td>
      <td>E</td>
      <td>America</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
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
    </tr>
    <tr>
      <th>43</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-21</td>
      <td>Atlas</td>
      <td>UANL</td>
      <td>D</td>
      <td>Atlas</td>
    </tr>
    <tr>
      <th>43</th>
      <td>0</td>
      <td>0</td>
      <td>2021-05-30</td>
      <td>Cruz Azul</td>
      <td>Santos</td>
      <td>E</td>
      <td>Cruz Azul</td>
    </tr>
    <tr>
      <th>43</th>
      <td>0</td>
      <td>0</td>
      <td>2021-05-30</td>
      <td>Cruz Azul</td>
      <td>Santos</td>
      <td>E</td>
      <td>Cruz Azul</td>
    </tr>
    <tr>
      <th>44</th>
      <td>1</td>
      <td>1</td>
      <td>2022-05-26</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>V</td>
      <td>Atlas</td>
    </tr>
    <tr>
      <th>45</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
    </tr>
  </tbody>
</table>
<p>16219 rows × 7 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-0e8921fa-bb27-45c4-8e79-5c80ea44cfcc')"
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
          document.querySelector('#df-0e8921fa-bb27-45c4-8e79-5c80ea44cfcc button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-0e8921fa-bb27-45c4-8e79-5c80ea44cfcc');
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

::: {.cell .code id="0C3m1OqfwSD9"}
``` python
merged = combined.merge(combined, left_on=["Fecha", "NEquipo"], right_on= ["Fecha", "Adversario"])
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":423}" id="88ViVlVvwnKw" outputId="05dc96b1-b3d4-421a-fc6b-4a67af96be24"}
``` python
merged
```

::: {.output .execute_result execution_count="124"}
```{=html}

  <div id="df-0effc142-bdd3-4359-b98c-d06891e64224">
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
      <th>actual_x</th>
      <th>predicts_x</th>
      <th>Fecha</th>
      <th>Equipo_x</th>
      <th>Adversario_x</th>
      <th>Resultado_x</th>
      <th>NEquipo_x</th>
      <th>actual_y</th>
      <th>predicts_y</th>
      <th>Equipo_y</th>
      <th>Adversario_y</th>
      <th>Resultado_y</th>
      <th>NEquipo_y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>1</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>1</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>1</td>
      <td>1</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>1</td>
      <td>1</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
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
    </tr>
    <tr>
      <th>251654</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251655</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
      <td>1</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251656</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251657</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251658</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Pachuca</td>
    </tr>
  </tbody>
</table>
<p>251659 rows × 13 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-0effc142-bdd3-4359-b98c-d06891e64224')"
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
          document.querySelector('#df-0effc142-bdd3-4359-b98c-d06891e64224 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-0effc142-bdd3-4359-b98c-d06891e64224');
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

::: {.cell .code id="AVTUizy9yRXD"}
``` python
merged = merged.drop_duplicates()
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="o8HOOrjRyujz" outputId="6959b769-cd25-43ac-9ae2-f33d94109946"}
``` python
merged.droplevel
```

::: {.output .execute_result execution_count="140"}
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
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="pNkrH5lpy5tR" outputId="87e63232-7df2-4322-bd96-c85898db7340"}
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
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":423}" id="tfSn18qhztUJ" outputId="546c5fdc-cf4f-44b8-8428-df3f094abb7a"}
``` python
merged.drop_duplicates()
```

::: {.output .execute_result execution_count="148"}
```{=html}

  <div id="df-0f633e98-a7a8-40b5-ab35-93ece0fbcbc4">
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
      <th>actual_x</th>
      <th>predicts_x</th>
      <th>Fecha</th>
      <th>Equipo_x</th>
      <th>Adversario_x</th>
      <th>Resultado_x</th>
      <th>NEquipo_x</th>
      <th>actual_y</th>
      <th>predicts_y</th>
      <th>Equipo_y</th>
      <th>Adversario_y</th>
      <th>Resultado_y</th>
      <th>NEquipo_y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>1</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>1</td>
      <td>1</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>1</td>
      <td>0</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
    </tr>
    <tr>
      <th>40</th>
      <td>0</td>
      <td>0</td>
      <td>2021-01-09</td>
      <td>Atlas</td>
      <td>Monterrey</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>1</td>
      <td>Monterrey</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Monterrey</td>
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
    </tr>
    <tr>
      <th>251645</th>
      <td>1</td>
      <td>1</td>
      <td>2022-05-26</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>V</td>
      <td>Atlas</td>
      <td>0</td>
      <td>1</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>D</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251646</th>
      <td>1</td>
      <td>1</td>
      <td>2022-05-26</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>V</td>
      <td>Atlas</td>
      <td>1</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>D</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251647</th>
      <td>1</td>
      <td>1</td>
      <td>2022-05-26</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>V</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>D</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251652</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
      <td>0</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Pachuca</td>
    </tr>
    <tr>
      <th>251655</th>
      <td>0</td>
      <td>0</td>
      <td>2022-05-29</td>
      <td>Atlas</td>
      <td>Pachuca</td>
      <td>D</td>
      <td>Atlas</td>
      <td>1</td>
      <td>0</td>
      <td>Pachuca</td>
      <td>Atlas</td>
      <td>V</td>
      <td>Pachuca</td>
    </tr>
  </tbody>
</table>
<p>4914 rows × 13 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-0f633e98-a7a8-40b5-ab35-93ece0fbcbc4')"
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
          document.querySelector('#df-0f633e98-a7a8-40b5-ab35-93ece0fbcbc4 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-0f633e98-a7a8-40b5-ab35-93ece0fbcbc4');
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

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="AXGQFdl4xEWf" outputId="4d93fd94-9456-4b8e-c8aa-e3007ede1f6a"}
``` python
merged[(merged["predicts_x"]== 1) & (merged["predicts_y"] == 0)]["actual_x"].value_counts()
```

::: {.output .execute_result execution_count="149"}
    1    590
    0    566
    Name: actual_x, dtype: int64
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="CIUQIBDjx2lS" outputId="0948dd16-7c7b-498b-93d7-a8b4cc65b750"}
``` python
18605 / (19180 + 18605)
```

::: {.output .execute_result execution_count="150"}
    0.49239116051343124
:::
:::
