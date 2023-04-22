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

::: {.output .stream .stderr}
    <ipython-input-4-bdebca18dc96>:1: DtypeWarning: Columns (19) have mixed types. Specify dtype option on import or set low_memory=False.
      loans = pd.read_csv('https://github.com/sonder-art/fdd_prim_2023/blob/main/codigo/pandas/LoansData_sample.csv.gz?raw=true', compression="gzip")
:::

::: {.output .execute_result execution_count="4"}
```{=html}

  <div id="df-f687f73c-cf5f-4d16-9721-57aa07358437">
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
      <th>Unnamed: 0</th>
      <th>id</th>
      <th>member_id</th>
      <th>loan_amnt</th>
      <th>funded_amnt</th>
      <th>funded_amnt_inv</th>
      <th>term</th>
      <th>int_rate</th>
      <th>installment</th>
      <th>grade</th>
      <th>...</th>
      <th>hardship_payoff_balance_amount</th>
      <th>hardship_last_payment_amount</th>
      <th>disbursement_method</th>
      <th>debt_settlement_flag</th>
      <th>debt_settlement_flag_date</th>
      <th>settlement_status</th>
      <th>settlement_date</th>
      <th>settlement_amount</th>
      <th>settlement_percentage</th>
      <th>settlement_term</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>38098114</td>
      <td>NaN</td>
      <td>15000.0</td>
      <td>15000.0</td>
      <td>15000.0</td>
      <td>60 months</td>
      <td>12.39</td>
      <td>336.64</td>
      <td>C</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>36805548</td>
      <td>NaN</td>
      <td>10400.0</td>
      <td>10400.0</td>
      <td>10400.0</td>
      <td>36 months</td>
      <td>6.99</td>
      <td>321.08</td>
      <td>A</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>37842129</td>
      <td>NaN</td>
      <td>21425.0</td>
      <td>21425.0</td>
      <td>21425.0</td>
      <td>60 months</td>
      <td>15.59</td>
      <td>516.36</td>
      <td>D</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>37612354</td>
      <td>NaN</td>
      <td>12800.0</td>
      <td>12800.0</td>
      <td>12800.0</td>
      <td>60 months</td>
      <td>17.14</td>
      <td>319.08</td>
      <td>D</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>37662224</td>
      <td>NaN</td>
      <td>7650.0</td>
      <td>7650.0</td>
      <td>7650.0</td>
      <td>36 months</td>
      <td>13.66</td>
      <td>260.20</td>
      <td>C</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
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
      <th>99995</th>
      <td>99995</td>
      <td>22454240</td>
      <td>NaN</td>
      <td>8400.0</td>
      <td>8400.0</td>
      <td>8400.0</td>
      <td>36 months</td>
      <td>9.17</td>
      <td>267.79</td>
      <td>B</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>99996</th>
      <td>99996</td>
      <td>11396920</td>
      <td>NaN</td>
      <td>10000.0</td>
      <td>10000.0</td>
      <td>10000.0</td>
      <td>36 months</td>
      <td>12.99</td>
      <td>336.90</td>
      <td>C</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>99997</th>
      <td>99997</td>
      <td>8556176</td>
      <td>NaN</td>
      <td>30000.0</td>
      <td>30000.0</td>
      <td>30000.0</td>
      <td>60 months</td>
      <td>20.99</td>
      <td>811.44</td>
      <td>E</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>99998</th>
      <td>99998</td>
      <td>24023408</td>
      <td>NaN</td>
      <td>8475.0</td>
      <td>8475.0</td>
      <td>8475.0</td>
      <td>36 months</td>
      <td>24.99</td>
      <td>336.92</td>
      <td>F</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>99999</th>
      <td>99999</td>
      <td>24023398</td>
      <td>NaN</td>
      <td>25000.0</td>
      <td>25000.0</td>
      <td>25000.0</td>
      <td>36 months</td>
      <td>10.15</td>
      <td>808.45</td>
      <td>B</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cash</td>
      <td>N</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>100000 rows × 151 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-f687f73c-cf5f-4d16-9721-57aa07358437')"
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
          document.querySelector('#df-f687f73c-cf5f-4d16-9721-57aa07358437 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-f687f73c-cf5f-4d16-9721-57aa07358437');
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

::: {.cell .markdown id="Diu75hO12dkE"}
## Tabla (column_name, type)
:::

::: {.cell .markdown id="XRDFGHUT2dkE"}
Revisa el metodo pd.DataFrame.dtypes.
<https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.dtypes.html>
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="smWSJeKD2dkE" outputId="25d92946-b4dc-4d82-f9b8-714c8fe6ffed"}
``` python
column_types = loans.dtypes
column_types
```

::: {.output .execute_result execution_count="5"}
    Unnamed: 0                 int64
    id                         int64
    member_id                float64
    loan_amnt                float64
    funded_amnt              float64
                              ...   
    settlement_status         object
    settlement_date           object
    settlement_amount        float64
    settlement_percentage    float64
    settlement_term          float64
    Length: 151, dtype: object
:::
:::

::: {.cell .markdown id="H17INSvU2dkF"}
## Cargar descripcion de columnas
:::

::: {.cell .markdown id="WFAH_YiD2dkF"}
La siguiente tabla tiene una descripcion del significado de cada columna
:::

::: {.cell .code id="zVi6Y_hx2dkF"}
``` python


datos_dict = pd.read_excel(
    'https://resources.lendingclub.com/LCDataDictionary.xlsx')
datos_dict.columns = ['feature', 'description']
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":423}" id="k9pSCwPc2dkF" outputId="3ab6b617-ddbf-4950-ff69-76e0753a1672"}
``` python
datos_dict
```

::: {.output .execute_result execution_count="7"}
```{=html}

  <div id="df-d320316b-f741-4848-8eef-88a1edeca0d8">
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
      <th>feature</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>acc_now_delinq</td>
      <td>The number of accounts on which the borrower i...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>acc_open_past_24mths</td>
      <td>Number of trades opened in past 24 months.</td>
    </tr>
    <tr>
      <th>2</th>
      <td>addr_state</td>
      <td>The state provided by the borrower in the loan...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>all_util</td>
      <td>Balance to credit limit on all trades</td>
    </tr>
    <tr>
      <th>4</th>
      <td>annual_inc</td>
      <td>The self-reported annual income provided by th...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>148</th>
      <td>settlement_amount</td>
      <td>The loan amount that the borrower has agreed t...</td>
    </tr>
    <tr>
      <th>149</th>
      <td>settlement_percentage</td>
      <td>The settlement amount as a percentage of the p...</td>
    </tr>
    <tr>
      <th>150</th>
      <td>settlement_term</td>
      <td>The number of months that the borrower will be...</td>
    </tr>
    <tr>
      <th>151</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>152</th>
      <td>NaN</td>
      <td>* Employer Title replaces Employer Name for al...</td>
    </tr>
  </tbody>
</table>
<p>153 rows × 2 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-d320316b-f741-4848-8eef-88a1edeca0d8')"
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
          document.querySelector('#df-d320316b-f741-4848-8eef-88a1edeca0d8 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-d320316b-f741-4848-8eef-88a1edeca0d8');
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

::: {.cell .markdown id="zCzqoWnY2dkG"}
### Pickle
:::

::: {.cell .markdown id="uWyqA9332dkG"}
Crea codigo para **guardar** y **cargar** el DataFrame de `datos_dict`
creada en las celdas anteriores en formato **pickle**
:::

::: {.cell .code id="RBbC08GD2dkH"}
``` python
import pickle
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":423}" id="Z-8CBb1x2dkH" outputId="099f2ad9-c00c-41a0-8e1a-37bb43e18c61"}
``` python
# Codigo guardar
datos_dict.to_pickle('datos_dict.pkl')

# Codigo cargar
datos_dict_pickle = pd.read_pickle('datos_dict.pkl')
datos_dict_pickle
```

::: {.output .execute_result execution_count="9"}
```{=html}

  <div id="df-796f3fe7-7f3c-4412-b6c9-fdf2eddd0430">
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
      <th>feature</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>acc_now_delinq</td>
      <td>The number of accounts on which the borrower i...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>acc_open_past_24mths</td>
      <td>Number of trades opened in past 24 months.</td>
    </tr>
    <tr>
      <th>2</th>
      <td>addr_state</td>
      <td>The state provided by the borrower in the loan...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>all_util</td>
      <td>Balance to credit limit on all trades</td>
    </tr>
    <tr>
      <th>4</th>
      <td>annual_inc</td>
      <td>The self-reported annual income provided by th...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>148</th>
      <td>settlement_amount</td>
      <td>The loan amount that the borrower has agreed t...</td>
    </tr>
    <tr>
      <th>149</th>
      <td>settlement_percentage</td>
      <td>The settlement amount as a percentage of the p...</td>
    </tr>
    <tr>
      <th>150</th>
      <td>settlement_term</td>
      <td>The number of months that the borrower will be...</td>
    </tr>
    <tr>
      <th>151</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>152</th>
      <td>NaN</td>
      <td>* Employer Title replaces Employer Name for al...</td>
    </tr>
  </tbody>
</table>
<p>153 rows × 2 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-796f3fe7-7f3c-4412-b6c9-fdf2eddd0430')"
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
          document.querySelector('#df-796f3fe7-7f3c-4412-b6c9-fdf2eddd0430 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-796f3fe7-7f3c-4412-b6c9-fdf2eddd0430');
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

::: {.cell .markdown id="7Ki9_6CN2dkH"}
## Tipos de Datos
:::

::: {.cell .markdown id="bhv7AqKK2dkH"}
Realiza las transformaciones o casteos (casting) que creas necesarios a
tus datos de tal manera que el typo de dato sea adecuado. Al terminar
recrea la tabla `column_types` con los nuevos tipos.
:::

::: {.cell .markdown id="LixYjR_02dkH"}
No olvides anotar tus justificaciones para recordar cuando te toque
explicarlo.
:::

::: {.cell .code id="KTS0prH9AWfw"}
``` python
loans.set_index("id", inplace=True)
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":692}" id="IjLZvUei-g0q" outputId="7bf74b86-f7de-4f67-fd66-a9bea0887e01"}
``` python
# Tomamos un subset con aquellas columnas de datos que pueden ser de interes, descartamos las columnas que tienen NaN y algunas otras que no necesitarían cambiar su dType

loans_sub = loans[['loan_amnt' , 'funded_amnt', 'funded_amnt_inv', 'term', 'int_rate', 'installment', 'grade', 
                      'sub_grade', 'emp_length', 'home_ownership', 'annual_inc', 'verification_status', 'loan_status',
                      'pymnt_plan', 'purpose', 'dti', 'delinq_2yrs', 'earliest_cr_line', 'open_acc', 'revol_bal', 'total_acc', 'total_pymnt', 'total_pymnt_inv', 
                      'last_pymnt_d', 'last_pymnt_amnt', 'tot_cur_bal', 'acc_open_past_24mths', 'avg_cur_bal', 'pct_tl_nvr_dlq', 'tot_hi_cred_lim']]

loans_sub
```

::: {.output .execute_result execution_count="14"}
```{=html}

  <div id="df-b3efce9d-3991-47b3-9a67-ff85cd3b66a5">
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
      <th>loan_amnt</th>
      <th>funded_amnt</th>
      <th>funded_amnt_inv</th>
      <th>term</th>
      <th>int_rate</th>
      <th>installment</th>
      <th>grade</th>
      <th>sub_grade</th>
      <th>emp_length</th>
      <th>home_ownership</th>
      <th>...</th>
      <th>total_acc</th>
      <th>total_pymnt</th>
      <th>total_pymnt_inv</th>
      <th>last_pymnt_d</th>
      <th>last_pymnt_amnt</th>
      <th>tot_cur_bal</th>
      <th>acc_open_past_24mths</th>
      <th>avg_cur_bal</th>
      <th>pct_tl_nvr_dlq</th>
      <th>tot_hi_cred_lim</th>
    </tr>
    <tr>
      <th>id</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>38098114</th>
      <td>15000.0</td>
      <td>15000.0</td>
      <td>15000.0</td>
      <td>60 months</td>
      <td>12.39</td>
      <td>336.64</td>
      <td>C</td>
      <td>C1</td>
      <td>10+ years</td>
      <td>RENT</td>
      <td>...</td>
      <td>17.0</td>
      <td>17392.370000</td>
      <td>17392.37</td>
      <td>Jun-2016</td>
      <td>12017.81</td>
      <td>149140.0</td>
      <td>5.0</td>
      <td>29828.0</td>
      <td>100.0</td>
      <td>196500.0</td>
    </tr>
    <tr>
      <th>36805548</th>
      <td>10400.0</td>
      <td>10400.0</td>
      <td>10400.0</td>
      <td>36 months</td>
      <td>6.99</td>
      <td>321.08</td>
      <td>A</td>
      <td>A3</td>
      <td>8 years</td>
      <td>MORTGAGE</td>
      <td>...</td>
      <td>36.0</td>
      <td>6611.690000</td>
      <td>6611.69</td>
      <td>Aug-2016</td>
      <td>321.08</td>
      <td>162110.0</td>
      <td>7.0</td>
      <td>9536.0</td>
      <td>83.3</td>
      <td>179407.0</td>
    </tr>
    <tr>
      <th>37842129</th>
      <td>21425.0</td>
      <td>21425.0</td>
      <td>21425.0</td>
      <td>60 months</td>
      <td>15.59</td>
      <td>516.36</td>
      <td>D</td>
      <td>D1</td>
      <td>6 years</td>
      <td>RENT</td>
      <td>...</td>
      <td>35.0</td>
      <td>25512.200000</td>
      <td>25512.20</td>
      <td>May-2016</td>
      <td>17813.19</td>
      <td>42315.0</td>
      <td>4.0</td>
      <td>4232.0</td>
      <td>91.4</td>
      <td>57073.0</td>
    </tr>
    <tr>
      <th>37612354</th>
      <td>12800.0</td>
      <td>12800.0</td>
      <td>12800.0</td>
      <td>60 months</td>
      <td>17.14</td>
      <td>319.08</td>
      <td>D</td>
      <td>D4</td>
      <td>10+ years</td>
      <td>MORTGAGE</td>
      <td>...</td>
      <td>13.0</td>
      <td>11207.670000</td>
      <td>11207.67</td>
      <td>Dec-2017</td>
      <td>319.08</td>
      <td>261815.0</td>
      <td>2.0</td>
      <td>32727.0</td>
      <td>76.9</td>
      <td>368700.0</td>
    </tr>
    <tr>
      <th>37662224</th>
      <td>7650.0</td>
      <td>7650.0</td>
      <td>7650.0</td>
      <td>36 months</td>
      <td>13.66</td>
      <td>260.20</td>
      <td>C</td>
      <td>C3</td>
      <td>&lt; 1 year</td>
      <td>RENT</td>
      <td>...</td>
      <td>20.0</td>
      <td>2281.980000</td>
      <td>2281.98</td>
      <td>Aug-2015</td>
      <td>17.70</td>
      <td>64426.0</td>
      <td>6.0</td>
      <td>5857.0</td>
      <td>100.0</td>
      <td>82331.0</td>
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
      <th>22454240</th>
      <td>8400.0</td>
      <td>8400.0</td>
      <td>8400.0</td>
      <td>36 months</td>
      <td>9.17</td>
      <td>267.79</td>
      <td>B</td>
      <td>B1</td>
      <td>2 years</td>
      <td>MORTGAGE</td>
      <td>...</td>
      <td>16.0</td>
      <td>9640.145407</td>
      <td>9640.15</td>
      <td>Aug-2017</td>
      <td>267.50</td>
      <td>152181.0</td>
      <td>2.0</td>
      <td>25364.0</td>
      <td>93.7</td>
      <td>209557.0</td>
    </tr>
    <tr>
      <th>11396920</th>
      <td>10000.0</td>
      <td>10000.0</td>
      <td>10000.0</td>
      <td>36 months</td>
      <td>12.99</td>
      <td>336.90</td>
      <td>C</td>
      <td>C1</td>
      <td>3 years</td>
      <td>RENT</td>
      <td>...</td>
      <td>17.0</td>
      <td>11685.080000</td>
      <td>11685.08</td>
      <td>Mar-2016</td>
      <td>5594.78</td>
      <td>46413.0</td>
      <td>3.0</td>
      <td>4219.0</td>
      <td>100.0</td>
      <td>64149.0</td>
    </tr>
    <tr>
      <th>8556176</th>
      <td>30000.0</td>
      <td>30000.0</td>
      <td>30000.0</td>
      <td>60 months</td>
      <td>20.99</td>
      <td>811.44</td>
      <td>E</td>
      <td>E4</td>
      <td>10+ years</td>
      <td>RENT</td>
      <td>...</td>
      <td>30.0</td>
      <td>32530.430000</td>
      <td>32530.43</td>
      <td>Dec-2017</td>
      <td>811.44</td>
      <td>345934.0</td>
      <td>5.0</td>
      <td>20349.0</td>
      <td>93.3</td>
      <td>371088.0</td>
    </tr>
    <tr>
      <th>24023408</th>
      <td>8475.0</td>
      <td>8475.0</td>
      <td>8475.0</td>
      <td>36 months</td>
      <td>24.99</td>
      <td>336.92</td>
      <td>F</td>
      <td>F4</td>
      <td>10+ years</td>
      <td>RENT</td>
      <td>...</td>
      <td>29.0</td>
      <td>2695.360000</td>
      <td>2695.36</td>
      <td>Apr-2015</td>
      <td>336.92</td>
      <td>31247.0</td>
      <td>8.0</td>
      <td>3125.0</td>
      <td>86.4</td>
      <td>43686.0</td>
    </tr>
    <tr>
      <th>24023398</th>
      <td>25000.0</td>
      <td>25000.0</td>
      <td>25000.0</td>
      <td>36 months</td>
      <td>10.15</td>
      <td>808.45</td>
      <td>B</td>
      <td>B2</td>
      <td>3 years</td>
      <td>OWN</td>
      <td>...</td>
      <td>35.0</td>
      <td>29024.061162</td>
      <td>29024.06</td>
      <td>Mar-2017</td>
      <td>4759.78</td>
      <td>233053.0</td>
      <td>5.0</td>
      <td>10133.0</td>
      <td>100.0</td>
      <td>290888.0</td>
    </tr>
  </tbody>
</table>
<p>100000 rows × 30 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-b3efce9d-3991-47b3-9a67-ff85cd3b66a5')"
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
          document.querySelector('#df-b3efce9d-3991-47b3-9a67-ff85cd3b66a5 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-b3efce9d-3991-47b3-9a67-ff85cd3b66a5');
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

::: {.cell .code id="MX-CW3sJA1Tk"}
``` python
# Obseravamos que hay algunos datos que podrían ser casteados : term, las fechas, emp_length, pyment_plan, annual_inc, loan_amnt 
```
:::

::: {.cell .code id="u4-scfkeB0-G"}
``` python
# Lo pasamos a int
loans_sub['annual_inc']=loans['annual_inc'].astype(np.int64)  
```
:::

::: {.cell .code id="yIrepLpPB6rb"}
``` python
# Lo pasamos a int
loans_sub['loan_amnt']=loans['loan_amnt'].astype(np.int64)
```
:::

::: {.cell .code id="Jrm6MUMPC2cI"}
``` python
# Reemplazamos los n y y con booleanos
loans_sub["pymnt_plan"].replace({"n":False, "y":True}, inplace=True)
```
:::

::: {.cell .code id="tSMz4UB4B82n"}
``` python
# Reemplazamos a int en lugar de tener num y letra
loans_sub["term"].replace({" 36 months":"36", " 60 months":"60"}, inplace=True)
loans_sub["term"] = loans_sub["term"].astype("int")
```
:::

::: {.cell .code id="179IVojjDEew"}
``` python
# Reemplazamos a float en lugar de tener num y letra
loans_sub["emp_length"].replace({"10+ years":"10", "8 years":"8", "6 years":"6", "< 1 year":"0", "2 years":"2"
, "9 years":"9", "7 years":"7", "5 years":"5", "3 years":"3", "1 year":"1", "4 years":"4"}, inplace=True)
loans_sub["emp_length"] = loans_sub["emp_length"].astype('float')
```
:::

::: {.cell .code id="aLHeYs6oCiVP"}
``` python
# Casteamos las fechas a datetime

loans_sub['last_pymnt_d'] = pd.to_datetime(loans_sub['last_pymnt_d'])
loans_sub['last_pymnt_d'] = loans_sub['last_pymnt_d'].dt.to_period('M')

loans_sub['earliest_cr_line'] = pd.to_datetime(loans_sub['earliest_cr_line'])
loans_sub['earliest_cr_line'] = loans_sub['earliest_cr_line'].dt.to_period('M')
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="WdQGRp312dkI" outputId="696f9342-9c02-435b-ab49-d028bccd3e12"}
``` python
column_types = loans_sub.dtypes
column_types

```

::: {.output .execute_result execution_count="32"}
    loan_amnt                   int64
    funded_amnt               float64
    funded_amnt_inv           float64
    term                        int64
    int_rate                  float64
    installment               float64
    grade                      object
    sub_grade                  object
    emp_length                float64
    home_ownership             object
    annual_inc                  int64
    verification_status        object
    loan_status                object
    pymnt_plan                   bool
    purpose                    object
    dti                       float64
    delinq_2yrs               float64
    earliest_cr_line        period[M]
    open_acc                  float64
    revol_bal                 float64
    total_acc                 float64
    total_pymnt               float64
    total_pymnt_inv           float64
    last_pymnt_d            period[M]
    last_pymnt_amnt           float64
    tot_cur_bal               float64
    acc_open_past_24mths      float64
    avg_cur_bal               float64
    pct_tl_nvr_dlq            float64
    tot_hi_cred_lim           float64
    dtype: object
:::
:::

::: {.cell .markdown id="PwCWa92N2dkI"}
## Manejo de NaNs o missings
:::

::: {.cell .markdown id="Uyf54Ygs2dkJ"}
Maneja los datos de tipos missing. Elije una estrategia adecuada
dependiendo del tipo de dato que le asignaste a la columna.
:::

::: {.cell .markdown id="skWR9qaO2dkJ"}
Crea codigo para **guardar** y **cargar** un archivo JSON en el que se
guarde la `estrategia` y `valor` que utilizaste para **imputar**. Por
ejemplo: Si hay una columna que se llama `columna 3` y utilizaste la
estrategia de imputacion de media, y existe otra llamada `columna 4` y
elegiste la palabra \'missing\' el JSON debera contener:

`{'columna 3':{'estrategia':'mean', 'valor':3.4}, 'columna 4':{'estrategia':'identificador', 'valor':'missing'}}`

De tal manera que para cada columna que tenga un metodo de imputacion
apunte a otro diccionario donde el **key** `estrategia` describa de
manera sencilla el metodo, y el **key** `valor` el valor usado. En
general:\
`{'nombre de la columna':{'estrategia':'descripcion de estrategia', 'valor':'valor utilizado'}}`.

De utilizar mas de un metodo puedes anidarlos en una lista\
`[{...},{...}]`.

Incluso si la columna utilizada no sufrio imputacion, es necesario que
la agregues al JSON.

La idea es que cualquier otra persona pueda cargar el el archivo JSON
con tu funcion, entender que hiciste y replicarlo facilmente. No existe
solo una respuesta correcta, pero tendras que justificar y explicar tus
deciciones.
:::

::: {.cell .markdown id="kT6Q9P8N2dkJ"}
### Imputacion
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="2oCo-2if2dkJ" outputId="a45389ca-7cdd-4256-de54-5edf2cebebc3"}
``` python
loans_sub.isna().sum()
```

::: {.output .execute_result execution_count="34"}
    loan_amnt                  0
    funded_amnt                0
    funded_amnt_inv            0
    term                       0
    int_rate                   0
    installment                0
    grade                      0
    sub_grade                  0
    emp_length              5259
    home_ownership             0
    annual_inc                 0
    verification_status        0
    loan_status                0
    pymnt_plan                 0
    purpose                    0
    dti                        0
    delinq_2yrs                0
    earliest_cr_line           0
    open_acc                   0
    revol_bal                  0
    total_acc                  0
    total_pymnt                0
    total_pymnt_inv            0
    last_pymnt_d              67
    last_pymnt_amnt            0
    tot_cur_bal                0
    acc_open_past_24mths       0
    avg_cur_bal                0
    pct_tl_nvr_dlq             0
    tot_hi_cred_lim            0
    dtype: int64
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="VuYtjgWh2dkK" outputId="b7215e16-c32d-4f06-b0c9-2dc4d35f6e4a"}
``` python
# Teenemos NaN en emp_length y last_pymnt_amnt
loans_sub["last_pymnt_d"].fillna("missing", inplace=True)
loans_sub["emp_length"].fillna("missing", inplace=True)
```

::: {.output .stream .stderr}
    <ipython-input-35-d8b505beb300>:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame

    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      loans_sub["last_pymnt_d"].fillna("missing", inplace=True)
    <ipython-input-35-d8b505beb300>:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame

    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      loans_sub["emp_length"].fillna("missing", inplace=True)
:::
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="XfpYi6vYFMtC" outputId="d0399fc6-7e70-4fda-f1cf-87cb1ea81763"}
``` python
loans_sub.isna().sum()
# Ya no hay NaN
```

::: {.output .execute_result execution_count="36"}
    loan_amnt               0
    funded_amnt             0
    funded_amnt_inv         0
    term                    0
    int_rate                0
    installment             0
    grade                   0
    sub_grade               0
    emp_length              0
    home_ownership          0
    annual_inc              0
    verification_status     0
    loan_status             0
    pymnt_plan              0
    purpose                 0
    dti                     0
    delinq_2yrs             0
    earliest_cr_line        0
    open_acc                0
    revol_bal               0
    total_acc               0
    total_pymnt             0
    total_pymnt_inv         0
    last_pymnt_d            0
    last_pymnt_amnt         0
    tot_cur_bal             0
    acc_open_past_24mths    0
    avg_cur_bal             0
    pct_tl_nvr_dlq          0
    tot_hi_cred_lim         0
    dtype: int64
:::
:::

::: {.cell .markdown id="Jw4h9kJ12dkK"}
### Codigo para salvar y cargar JSONs
:::

::: {.cell .code id="FVJ_1EOgFQYn"}
``` python
import json
```
:::

::: {.cell .code id="oUQ1T1PjFXVW"}
``` python
with open("columnas.json", "w") as archivo:

    d = {'loan_amnt':{} , 'funded_amnt':{}, 'funded_amnt_inv':{}, 'term':{}, 'int_rate':{}, 'installment':{}, 'sub_grade':{}, "emp_length":{}, 'home_ownership':{}, 'annual_inc':{}, 'verification_status':{}, 'loan_status':{}, 'pymnt_plan':{}, 'purpose':{}, 'dti':{}, 'delinq_2yrs':{}, 'earliest_cr_line':{}, 'open_acc':{}, 'revol_bal':{}, 'total_acc':{}, 'total_pymnt':{}, 'total_pymnt_inv':{}, "last_pymnt_d": {"estrategia":"identificador", "valor":"missing"}, 'last_pymnt_amnt':{}, 'tot_cur_bal':{}, 'acc_open_past_24mths':{}, 'avg_cur_bal':{}, 'pct_tl_nvr_dlq':{}, 'tot_hi_cred_lim':{}}
    json.dump(d, archivo, indent=2)
    
```
:::

::: {.cell .code colab="{\"base_uri\":\"https://localhost:8080/\"}" id="48ui5z2VFop6" outputId="2c7000f8-7a62-4a1c-da36-19fa94f17b1d"}
``` python
with open("columnas.json") as archivo:

    d = json.load(archivo)
    print(d)
```

::: {.output .stream .stdout}
    {'loan_amnt': {}, 'funded_amnt': {}, 'funded_amnt_inv': {}, 'term': {}, 'int_rate': {}, 'installment': {}, 'sub_grade': {}, 'emp_length': {}, 'home_ownership': {}, 'annual_inc': {}, 'verification_status': {}, 'loan_status': {}, 'pymnt_plan': {}, 'purpose': {}, 'dti': {}, 'delinq_2yrs': {}, 'earliest_cr_line': {}, 'open_acc': {}, 'revol_bal': {}, 'total_acc': {}, 'total_pymnt': {}, 'total_pymnt_inv': {}, 'last_pymnt_d': {'estrategia': 'identificador', 'valor': 'missing'}, 'last_pymnt_amnt': {}, 'tot_cur_bal': {}, 'acc_open_past_24mths': {}, 'avg_cur_bal': {}, 'pct_tl_nvr_dlq': {}, 'tot_hi_cred_lim': {}}
:::
:::
