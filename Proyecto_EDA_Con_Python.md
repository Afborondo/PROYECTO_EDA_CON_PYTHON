# OBJETIVO DEL PROYECTO

Realizar un análisis exploratorio de los datos que puedes encontrar que te aportamos. Para realizar este análisis es obligatorio realizarlo con Python.

Requisitos del proyecto.
A lo largo del proyecto tienes que cubrir los siguientes puntos:
- Transformación y limpieza de los datos.
- Análisis descriptivo de los datos.
- Visualización de los datos.
- Informe explicativo del análisis.

Herramientas para realizar el proyecto.
Tendrás que realizar el proyecto usando estás herramientas:
- Python
- Pandas
- Visual Studio Code




# RESUMEN DEL ÁNALISIS EDA

Si bien en el análisis EDA datallado se amplian cada uno de los puntos, a modo de resumen, las principales acciones y conclusiones en su caso son las siguientes:

- Carga de datos. Se realizan las cargas y los datos se almacenan en 4 dataframes, 1 para el data set de campañas, y uno para cada dataset de clientes correspondientes a los años 2012, 2013, 2014

-  Identificación de variables: el nombre de las variables es según se indica en el enunciado del ejercicio, salvo que no se incluyen "contact_month" y "contact_year". Dado que el nombre de las variables es explicativo, los mantenemos. Existen dos columnnas en los dos datasets. Por otro lado, hay dos columnas en ambos dataset con todos los valores únicos('id_" en campañas de marketing, e "ID" en clientes), que la elegimos como índice (set_index), pero que mantenemos la columna (para hacer futuras combinaciones). Se renombra la columna "id_" como "ID". Hay una columna sin nombre que no aporta información, es sólo un contador, la eliminamos.  Las variables son de tipo numérico, categórico y fecha, siendo necesaria una transformación posterior, ya que los datos iniciales según están cargados son numéricos int, foat y object, según la ejecución (pej la variable "date" en el dataset de campañas no está como tipo fecha). Respecto a la distribución y rangos de valores, a priori en los valores numéricos pueden existir outliers en edad, duration, campaign, income, dado que presentan valores min o max alejados de la media. Los dataset de clientes de 2012, 2013, 2014 se pueden fusionar en único dataset, df_clientes, con un concat, no tiene duplicados. 

- Datos nulos. Una vez analizado, no se hace nada. Existen algunos valores nulos de algunos campos pero con información en otros campos por sí que aportan valor.

- Duplicados. No hay datos duplicados.

- Consistencia de los datos. Los datos son consistentes.

- Formateo de datos. En el dataset de campañas hay una variables que es de fechas, "date".  "date", es un tipo string con la fecha en formato "dd-mes(nombre)-año", procedemos a convertirlo a tipo date y se añaden cuatro columnas, "date_c" fecha completa, "year_c", año, "day_c", día, y "weekday_c", día de la semana, para poder realizar análisis por series temporales y tendencias estacionales.

- Agrupación. Se agrupar datos numéricos continuos en categorías, en edades e ingresos. Se intenta agrupar las latitutes y longitudes en país, estado/provincia y ciudad para poder identificar patrones por geografía. He utilizado  la librería geopy (la he encontrado por internet) Se crean dos columnas adicionales con el país 'Country_c", estado 'State/Province_c", ciudad 'City_c'. Sin embargo, se aborta porque tarda demasiado para las 30.000 coordenadas.

- Manejo de outliers. Existen outliers en "age", "campaign", "pdays", "duration". En "age" existen 447 outliers de 37880 valores no nulos, un 1,16% por lo que la decisión es eliminar todas las filas de los outliers. En "campaign" existen 3529 outliers de 43000 valores no nulos, por tanto se deciden mantener estos valores. En "pdays" hay 43000 valores no nulos y 41412 valores que son 999 que parecen outliers. Al ser pdays el número de días que han pasado desde la última vez que se contactó con el cliente durante esta campaña, se pone este valor a null para que no interfiera en valores agregados, mantiéndose las filas. En "duration", hay 42559 valores no nulos y 3085 outliers, se deciden eliminar.

- Creación de variables categóricas. No se crean.

- Creación de variables derivadas. Se introduce una variable adicional en el dataset de campañas de marketing para incluir la antiguedad de cliente en el momento de la acción de marketing. Para ello se realiza un merge entre las campañas y los clientes para después se añade un campo al dataset de campañas con "antiguedad cliente", "aging_client_c" con la diferencia. Este merge sea realiza con el campo ID de ambos dataset (identificación de clientes) de campañas coincide con el campo ID del dataset de clientes, denominado df_clientes.

- Distribución de cada una de las variables. Se analizan todas la variables.

- Relaciones entre variables. A través de gráficos se muestra el comportamiento de la variable fundamental "y" (venta) con el restos de las variables fundamentales. Los gráficos son boxplot para las variables numéricas y piechart para las variables categóricas. Se aprecia que la variable "y".

- Respecto a relación de esta variable "venta" con el resto de variables, lo que se aprecia es que hay variaciones significativas con las siguientes (% con venta, % sin venta):
- ocupación: hay ligeramente más ventas en puestos administrativos (33% vs. 23%)
- estado civil: hay más ventas entre solteros (39% vs. 21%)
- estudios: hay más ventas en universitarios (43% vs. 30%)
- medio de contacto: hay más ventas por móvil (87% vs. 61%)
- número de contactos: hay más ventas al primero contacto (53% vs. 43%)
- duración del contacto: hay mucha más duración del contacto
- entre clientes que ya compraron anteriormente (25% vs. 1% )





# ANÁLISIS EDA DETALLADO

## 1. TRANSFORMACIÓN Y LIMPIEZA DE LOS DATOS

### 1.1 CARGA DE LOS DATOS

En esta fase, se abren los archivos y se revisa la estructura básica del conjunto de datos, obteniendo una vista preliminar del mismo para verificar si ha sido cargado correctamente. Esta vista inicial nos permite asegurar que las columnas y las filas sean visibles y estén en el formato adecuado, sin problemas de codificación o estructura, que podrían interferir en análisis posteriores.

Los datos se encuentran en dos ficheros:

- El primer dataset, 'bank-additional.csv', contiene los datos de las campañas de marketing directo de una institución bancaria portuguesa. Las campañas de marketing se basaron en llamadas telefónicas

- El segundo dataset, 'customer-details.xlsx' es un archivo Excel que nos da información sobre las características demográficas y comportamiento de compra de los clientes del banco. Este Excel consta de 3 hojas de trabajo diferentes, en cada una de ellas tenemos los clientes que entraron en el banco en diferentes años.


```python
#Se abre el primer dataset en el fichero CSV 'bank-additional.csv', se asigna a un dataframe para su procesado, "df_campañas"
import pandas as pd
pd.set_option('display.max_columns', None)
df_campañas = pd.read_csv(r'C:\Users\afbor\OneDrive\Escritorio\TPANALYTICS\PYTHON\PYTHON_FOR_DATA\DatosProyecto\bank-additional.csv')
df_campañas.describe(include='all')
```




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
      <th>age</th>
      <th>job</th>
      <th>marital</th>
      <th>education</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>contact</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>poutcome</th>
      <th>emp.var.rate</th>
      <th>cons.price.idx</th>
      <th>cons.conf.idx</th>
      <th>euribor3m</th>
      <th>nr.employed</th>
      <th>y</th>
      <th>date</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>id_</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>43000.000000</td>
      <td>37880.000000</td>
      <td>42655</td>
      <td>42915</td>
      <td>41193</td>
      <td>34019.000000</td>
      <td>41974.000000</td>
      <td>41974.000000</td>
      <td>43000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000</td>
      <td>43000.000000</td>
      <td>42529</td>
      <td>43000</td>
      <td>33744</td>
      <td>43000</td>
      <td>43000</td>
      <td>42752</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>11</td>
      <td>3</td>
      <td>7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>NaN</td>
      <td>26</td>
      <td>26</td>
      <td>309</td>
      <td>11</td>
      <td>2</td>
      <td>1825</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>43000</td>
    </tr>
    <tr>
      <th>top</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>cellular</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NONEXISTENT</td>
      <td>NaN</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5228,1</td>
      <td>no</td>
      <td>28-febrero-2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>993bbbd6-4dbc-4a40-a408-f91f8462bee6</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>10873</td>
      <td>25999</td>
      <td>12722</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>27396</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>37103</td>
      <td>NaN</td>
      <td>7938</td>
      <td>8020</td>
      <td>2287</td>
      <td>16980</td>
      <td>38156</td>
      <td>95</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>20651.099047</td>
      <td>39.977112</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000088</td>
      <td>0.535998</td>
      <td>0.155620</td>
      <td>NaN</td>
      <td>257.739279</td>
      <td>2.567233</td>
      <td>962.330953</td>
      <td>0.174023</td>
      <td>NaN</td>
      <td>0.077128</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>36.856697</td>
      <td>-95.939067</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>std</th>
      <td>11868.770913</td>
      <td>10.437957</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.009390</td>
      <td>0.498708</td>
      <td>0.362499</td>
      <td>NaN</td>
      <td>258.666033</td>
      <td>2.772294</td>
      <td>187.260394</td>
      <td>0.497366</td>
      <td>NaN</td>
      <td>1.573898</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.225948</td>
      <td>16.752282</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>17.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>-3.400000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>24.396000</td>
      <td>-124.997000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>10381.750000</td>
      <td>32.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>102.000000</td>
      <td>1.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>-1.800000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>30.614750</td>
      <td>-110.494250</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>20642.500000</td>
      <td>38.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>179.000000</td>
      <td>2.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>1.100000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>36.761000</td>
      <td>-95.899500</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>30930.250000</td>
      <td>47.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>319.000000</td>
      <td>3.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>1.400000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>43.113250</td>
      <td>-81.427750</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>max</th>
      <td>41187.000000</td>
      <td>98.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>NaN</td>
      <td>4918.000000</td>
      <td>56.000000</td>
      <td>999.000000</td>
      <td>7.000000</td>
      <td>NaN</td>
      <td>1.400000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>49.384000</td>
      <td>-66.937000</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_campañas.head(5)

```




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
      <th>age</th>
      <th>job</th>
      <th>marital</th>
      <th>education</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>contact</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>poutcome</th>
      <th>emp.var.rate</th>
      <th>cons.price.idx</th>
      <th>cons.conf.idx</th>
      <th>euribor3m</th>
      <th>nr.employed</th>
      <th>y</th>
      <th>date</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>id_</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>NaN</td>
      <td>housemaid</td>
      <td>MARRIED</td>
      <td>basic.4y</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>261</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5191</td>
      <td>no</td>
      <td>2-agosto-2019</td>
      <td>41.495</td>
      <td>-71.233</td>
      <td>089b39d8-e4d0-461b-87d4-814d71e0e079</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>57.0</td>
      <td>services</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>149</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>NaN</td>
      <td>5191</td>
      <td>no</td>
      <td>14-septiembre-2016</td>
      <td>34.601</td>
      <td>-83.923</td>
      <td>e9d37224-cb6f-4942-98d7-46672963d097</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>37.0</td>
      <td>services</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>226</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5191</td>
      <td>no</td>
      <td>15-febrero-2019</td>
      <td>34.939</td>
      <td>-94.847</td>
      <td>3f9f49b5-e410-4948-bf6e-f9244f04918b</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>40.0</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>basic.6y</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>151</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>NaN</td>
      <td>5191</td>
      <td>no</td>
      <td>29-noviembre-2015</td>
      <td>49.041</td>
      <td>-70.308</td>
      <td>9991fafb-4447-451a-8be2-b0df6098d13e</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>56.0</td>
      <td>services</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>telephone</td>
      <td>307</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>NaN</td>
      <td>5191</td>
      <td>no</td>
      <td>29-enero-2017</td>
      <td>38.033</td>
      <td>-104.463</td>
      <td>eca60b76-70b6-4077-80ba-bc52e8ebb0eb</td>
    </tr>
  </tbody>
</table>
</div>




Se procede a la carga del segundo data set 'customer-details.xlsx' . Son 3 hojas de excel, para los años 2012, 2013, 2014, respectivamente.
Se carga cada año (hoja) en tres dataframes, df_clientes_2012, df_clientes_2013, df_2014
Se leen las primeras filas y se verifica que en enfecto se trata de los datos esperados.
Se da por finalizada la fase del EDA 1.1 Carga de datos


```python
df_clientes_2012 = pd.read_excel(r"C:\Users\afbor\OneDrive\Escritorio\TPANALYTICS\PYTHON\PYTHON_FOR_DATA\DatosProyecto\customer-details.xlsx", sheet_name="2012")
df_clientes_2013 = pd.read_excel(r"C:\Users\afbor\OneDrive\Escritorio\TPANALYTICS\PYTHON\PYTHON_FOR_DATA\DatosProyecto\customer-details.xlsx", sheet_name="2013")
df_clientes_2014 = pd.read_excel(r"C:\Users\afbor\OneDrive\Escritorio\TPANALYTICS\PYTHON\PYTHON_FOR_DATA\DatosProyecto\customer-details.xlsx", sheet_name="2014")


df_clientes_2012.head(5)
df_clientes_2013.head(5)
df_clientes_2014.head(5)

```




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
      <th>Income</th>
      <th>Kidhome</th>
      <th>Teenhome</th>
      <th>Dt_Customer</th>
      <th>NumWebVisitsMonth</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>168812</td>
      <td>1</td>
      <td>1</td>
      <td>2014-06-10</td>
      <td>27</td>
      <td>4d6c811c-0f24-4a73-b374-2ebd1f80d46e</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>108615</td>
      <td>2</td>
      <td>1</td>
      <td>2014-04-05</td>
      <td>24</td>
      <td>eef0f24f-cbdb-4c67-b371-09d7bc59695e</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>179041</td>
      <td>0</td>
      <td>2</td>
      <td>2014-09-22</td>
      <td>4</td>
      <td>bbfdcedd-199d-4927-94bb-5dad051ff471</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>116639</td>
      <td>2</td>
      <td>2</td>
      <td>2014-08-16</td>
      <td>3</td>
      <td>e8ebb67d-4ee1-4615-b701-eefe3af607eb</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>49537</td>
      <td>0</td>
      <td>1</td>
      <td>2014-09-05</td>
      <td>28</td>
      <td>f7f79ace-0024-4505-a401-f340946f0942</td>
    </tr>
  </tbody>
</table>
</div>



### 1.2 IDENTIFICACIÓN DE VARIABLES

Durante esta fase, se identifican: (1) Nombre de las columnas, (2) Tipos de datos (3) Distribución y rangos de valores 

Utilizamos el métodos dataframe.describe() y dataframe.describe que aplicamos a los dos datasets.


```python
df_campañas.describe(include='all')
```




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
      <th>age</th>
      <th>job</th>
      <th>marital</th>
      <th>education</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>contact</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>poutcome</th>
      <th>emp.var.rate</th>
      <th>cons.price.idx</th>
      <th>cons.conf.idx</th>
      <th>euribor3m</th>
      <th>nr.employed</th>
      <th>y</th>
      <th>date</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>id_</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>43000.000000</td>
      <td>37880.000000</td>
      <td>42655</td>
      <td>42915</td>
      <td>41193</td>
      <td>34019.000000</td>
      <td>41974.000000</td>
      <td>41974.000000</td>
      <td>43000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000</td>
      <td>43000.000000</td>
      <td>42529</td>
      <td>43000</td>
      <td>33744</td>
      <td>43000</td>
      <td>43000</td>
      <td>42752</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>11</td>
      <td>3</td>
      <td>7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>NaN</td>
      <td>26</td>
      <td>26</td>
      <td>309</td>
      <td>11</td>
      <td>2</td>
      <td>1825</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>43000</td>
    </tr>
    <tr>
      <th>top</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>cellular</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NONEXISTENT</td>
      <td>NaN</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5228,1</td>
      <td>no</td>
      <td>28-febrero-2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>993bbbd6-4dbc-4a40-a408-f91f8462bee6</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>10873</td>
      <td>25999</td>
      <td>12722</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>27396</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>37103</td>
      <td>NaN</td>
      <td>7938</td>
      <td>8020</td>
      <td>2287</td>
      <td>16980</td>
      <td>38156</td>
      <td>95</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>20651.099047</td>
      <td>39.977112</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000088</td>
      <td>0.535998</td>
      <td>0.155620</td>
      <td>NaN</td>
      <td>257.739279</td>
      <td>2.567233</td>
      <td>962.330953</td>
      <td>0.174023</td>
      <td>NaN</td>
      <td>0.077128</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>36.856697</td>
      <td>-95.939067</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>std</th>
      <td>11868.770913</td>
      <td>10.437957</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.009390</td>
      <td>0.498708</td>
      <td>0.362499</td>
      <td>NaN</td>
      <td>258.666033</td>
      <td>2.772294</td>
      <td>187.260394</td>
      <td>0.497366</td>
      <td>NaN</td>
      <td>1.573898</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.225948</td>
      <td>16.752282</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>17.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>-3.400000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>24.396000</td>
      <td>-124.997000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>10381.750000</td>
      <td>32.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>102.000000</td>
      <td>1.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>-1.800000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>30.614750</td>
      <td>-110.494250</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>20642.500000</td>
      <td>38.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>179.000000</td>
      <td>2.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>1.100000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>36.761000</td>
      <td>-95.899500</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>30930.250000</td>
      <td>47.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>319.000000</td>
      <td>3.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>1.400000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>43.113250</td>
      <td>-81.427750</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>max</th>
      <td>41187.000000</td>
      <td>98.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>NaN</td>
      <td>4918.000000</td>
      <td>56.000000</td>
      <td>999.000000</td>
      <td>7.000000</td>
      <td>NaN</td>
      <td>1.400000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>49.384000</td>
      <td>-66.937000</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_clientes_2012.describe(include='all')
df_clientes_2013.describe(include='all')
df_clientes_2013.describe(include='all')
```




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
      <th>Income</th>
      <th>Kidhome</th>
      <th>Teenhome</th>
      <th>Dt_Customer</th>
      <th>NumWebVisitsMonth</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>8965.000000</td>
      <td>8965.000000</td>
      <td>8965.000000</td>
      <td>8965.000000</td>
      <td>8965</td>
      <td>8965.000000</td>
      <td>8965</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8965</td>
    </tr>
    <tr>
      <th>top</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ba7fb638-c6cb-4dc8-aad4-75456c3818c5</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>4482.000000</td>
      <td>92788.951255</td>
      <td>0.991523</td>
      <td>0.995761</td>
      <td>2013-07-02 11:23:37.110987008</td>
      <td>16.575683</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>5848.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2013-01-01 00:00:00</td>
      <td>1.000000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2241.000000</td>
      <td>49866.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2013-04-01 00:00:00</td>
      <td>9.000000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>4482.000000</td>
      <td>92315.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>2013-07-03 00:00:00</td>
      <td>17.000000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>6723.000000</td>
      <td>136063.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>2013-10-03 00:00:00</td>
      <td>25.000000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>max</th>
      <td>8964.000000</td>
      <td>180787.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>2013-12-31 00:00:00</td>
      <td>32.000000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>std</th>
      <td>2588.116915</td>
      <td>50188.145448</td>
      <td>0.810005</td>
      <td>0.814981</td>
      <td>NaN</td>
      <td>9.259737</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



Una vez analizados los dataset, se observa lo siguiente:
- hay dos columnas en ambos dataset con todos los valores únicos('id_" en campañas de marketing, e "ID" en clientes), que la elegimos como índice (set_index), pero que mantenemos la columna (para hacer futuras combinaciones). Se renombra la columna "id_" como "ID"
- hay una columna sin nombre que no aporta información, es sólo un contador, la eliminamos 
- el nombre de las variables es según descrito en la información del ejercicio, y dado que son explicativos, los mantemos. Sin embargo, no aparecen los campos "contact_month" ni "contact_year", en el dataset de campañas de marketing, que se indican en el enunciado, pero se pueden obtener a partir del campo "date"
- Las variables son de tipo numérico, categórico y fecha, siendo necesaria una transformación posterior, ya que los datos iniciales según están cargados son numéricos int, foat y object, según la ejecución (pej la variable "date" en el dataset de campañas no está como tipo fecha)
- Respecto a la distribución y rangos de valores, a priori en los valores numéricos pueden existir outliers en edad, duration, campaign, income, dado que presentan valores min o max alejados de la media
- los dataset de clientes de 2012, 2013, 2014 se pueden fusionar en único dataset, df_clientes, con un concat, no tiene duplicados y puede facilitar las combinaciones entre los dos datasets, campañas y clientes

Damos por terminado 1.2 Identificación de variables.


```python
df_campañas.rename(columns={'id_':'ID'}, inplace=True)
df_campañas.set_index('ID', drop=False)

df_clientes_2012.set_index('ID', drop=False)
df_clientes_2013.set_index('ID', drop=False)
df_clientes_2014.set_index('ID', drop=False)
```




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
      <th>Income</th>
      <th>Kidhome</th>
      <th>Teenhome</th>
      <th>Dt_Customer</th>
      <th>NumWebVisitsMonth</th>
      <th>ID</th>
    </tr>
    <tr>
      <th>ID</th>
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
      <th>4d6c811c-0f24-4a73-b374-2ebd1f80d46e</th>
      <td>0</td>
      <td>168812</td>
      <td>1</td>
      <td>1</td>
      <td>2014-06-10</td>
      <td>27</td>
      <td>4d6c811c-0f24-4a73-b374-2ebd1f80d46e</td>
    </tr>
    <tr>
      <th>eef0f24f-cbdb-4c67-b371-09d7bc59695e</th>
      <td>1</td>
      <td>108615</td>
      <td>2</td>
      <td>1</td>
      <td>2014-04-05</td>
      <td>24</td>
      <td>eef0f24f-cbdb-4c67-b371-09d7bc59695e</td>
    </tr>
    <tr>
      <th>bbfdcedd-199d-4927-94bb-5dad051ff471</th>
      <td>2</td>
      <td>179041</td>
      <td>0</td>
      <td>2</td>
      <td>2014-09-22</td>
      <td>4</td>
      <td>bbfdcedd-199d-4927-94bb-5dad051ff471</td>
    </tr>
    <tr>
      <th>e8ebb67d-4ee1-4615-b701-eefe3af607eb</th>
      <td>3</td>
      <td>116639</td>
      <td>2</td>
      <td>2</td>
      <td>2014-08-16</td>
      <td>3</td>
      <td>e8ebb67d-4ee1-4615-b701-eefe3af607eb</td>
    </tr>
    <tr>
      <th>f7f79ace-0024-4505-a401-f340946f0942</th>
      <td>4</td>
      <td>49537</td>
      <td>0</td>
      <td>1</td>
      <td>2014-09-05</td>
      <td>28</td>
      <td>f7f79ace-0024-4505-a401-f340946f0942</td>
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
      <th>5f432048-d515-4bb5-9c94-62db451f88d4</th>
      <td>14085</td>
      <td>156980</td>
      <td>1</td>
      <td>2</td>
      <td>2014-05-17</td>
      <td>7</td>
      <td>5f432048-d515-4bb5-9c94-62db451f88d4</td>
    </tr>
    <tr>
      <th>993bbbd6-4dbc-4a40-a408-f91f8462bee6</th>
      <td>14086</td>
      <td>139820</td>
      <td>0</td>
      <td>0</td>
      <td>2014-09-29</td>
      <td>1</td>
      <td>993bbbd6-4dbc-4a40-a408-f91f8462bee6</td>
    </tr>
    <tr>
      <th>d6271666-319d-42c8-a741-cb22bf2c2093</th>
      <td>14087</td>
      <td>78470</td>
      <td>2</td>
      <td>2</td>
      <td>2014-10-28</td>
      <td>12</td>
      <td>d6271666-319d-42c8-a741-cb22bf2c2093</td>
    </tr>
    <tr>
      <th>f67fbfa8-6573-414d-a805-b26a2f1b1ceb</th>
      <td>14088</td>
      <td>144218</td>
      <td>0</td>
      <td>1</td>
      <td>2014-07-01</td>
      <td>9</td>
      <td>f67fbfa8-6573-414d-a805-b26a2f1b1ceb</td>
    </tr>
    <tr>
      <th>9727bc06-c11a-461a-a5bb-3d210467cc2a</th>
      <td>14089</td>
      <td>115155</td>
      <td>0</td>
      <td>2</td>
      <td>2014-12-25</td>
      <td>7</td>
      <td>9727bc06-c11a-461a-a5bb-3d210467cc2a</td>
    </tr>
  </tbody>
</table>
<p>14090 rows × 7 columns</p>
</div>




```python
df_clientes_2012.drop(df_clientes_2012.columns[0], axis=1, inplace=True)
df_clientes_2013.drop(df_clientes_2013.columns[0], axis=1, inplace=True)
df_clientes_2014.drop(df_clientes_2014.columns[0], axis=1, inplace=True)

```


```python
df_campañas.drop(df_campañas.columns[0], axis=1, inplace=True)
```


```python
df_clientes_2012.info()
df_clientes_2013.info()
df_clientes_2014.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 20115 entries, 0 to 20114
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype         
    ---  ------             --------------  -----         
     0   Income             20115 non-null  int64         
     1   Kidhome            20115 non-null  int64         
     2   Teenhome           20115 non-null  int64         
     3   Dt_Customer        20115 non-null  datetime64[ns]
     4   NumWebVisitsMonth  20115 non-null  int64         
     5   ID                 20115 non-null  object        
    dtypes: datetime64[ns](1), int64(4), object(1)
    memory usage: 943.0+ KB
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 8965 entries, 0 to 8964
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype         
    ---  ------             --------------  -----         
     0   Income             8965 non-null   int64         
     1   Kidhome            8965 non-null   int64         
     2   Teenhome           8965 non-null   int64         
     3   Dt_Customer        8965 non-null   datetime64[ns]
     4   NumWebVisitsMonth  8965 non-null   int64         
     5   ID                 8965 non-null   object        
    dtypes: datetime64[ns](1), int64(4), object(1)
    memory usage: 420.4+ KB
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 14090 entries, 0 to 14089
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype         
    ---  ------             --------------  -----         
     0   Income             14090 non-null  int64         
     1   Kidhome            14090 non-null  int64         
     2   Teenhome           14090 non-null  int64         
     3   Dt_Customer        14090 non-null  datetime64[ns]
     4   NumWebVisitsMonth  14090 non-null  int64         
     5   ID                 14090 non-null  object        
    dtypes: datetime64[ns](1), int64(4), object(1)
    memory usage: 660.6+ KB
    


```python
df_clientes_2012.describe(include='all')
df_clientes=pd.concat([df_clientes_2014, df_clientes_2013], axis=0, join='outer')
df_clientes=pd.concat([df_clientes, df_clientes_2012], axis=0, join='outer')
df_clientes.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 43170 entries, 0 to 20114
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype         
    ---  ------             --------------  -----         
     0   Income             43170 non-null  int64         
     1   Kidhome            43170 non-null  int64         
     2   Teenhome           43170 non-null  int64         
     3   Dt_Customer        43170 non-null  datetime64[ns]
     4   NumWebVisitsMonth  43170 non-null  int64         
     5   ID                 43170 non-null  object        
    dtypes: datetime64[ns](1), int64(4), object(1)
    memory usage: 2.3+ MB
    

## 2. TRANSFORMACIÓN Y LIMPIEZA


### 2.1.LIMPIEZA
#### 2.1.2 LIMPIEZA DE DATOS FALTANTES

Eln el dataset de campañas de marketing. Consideramos tres tipos de variables:

- las demográficas del cliente: age, job, marital, education: existen muchos valores null, pero puede que no estén disponibles, por tanto, se mantienen todos los datos. El mayor porcentaje no es nulo.

1   age             37880 non-null  float64
 2   job             42655 non-null  object 
 3   marital         42915 non-null  object 
 4   education       41193 non-null  object 
 18  nr.employed     43000 non-null  object 

 - las variables de previos productos del banco: default, housing, loan. La mayoría de los datos son no nulos por tanto, los mantenemos.

5   default         34019 non-null  float64
 6   housing         41974 non-null  float64
 7   loan            41974 non-null  float64
 
 - las variables macro en el momento de la operación: cons.price.idx, cons.conf.idx, euribor3m. Faltan, pero quizá se pueden completar a partir de la fecha de la transacción, tomándola de otras tuplas. Se filtran las tuplas que tienen cons.price.idx a cero y para esa misma fecha hay distintos valores de cons.price.idx por lo que se decide no sustituir y mantenerlos a cero tal cual.

14  emp.var.rate    43000 non-null  float64
 15  cons.price.idx  42529 non-null  object 
 16  cons.conf.idx   43000 non-null  object 
 17  euribor3m       33744 non-null  object 

 - las variables que se refieren al contacto: contact, duration, campaign, pdays, previous, pdays, poutcome, y, date, latitude, longitude, id_. Son todas no nulas, salvo date. La duda sería quitar las tuplas nulas de date, que es la fecha en que se produjo la llamada. Haciendo un filtro en las tuplas que tienen null en date, se ve que existen datos en general en el resto de la tupla, por lo que se mantienen.

8   contact         43000 non-null  object 
 9   duration        43000 non-null  int64  
 10  campaign        43000 non-null  int64  
 11  pdays           43000 non-null  int64  
 12  previous        43000 non-null  int64  
 13  poutcome        43000 non-null  object 
 19  y               43000 non-null  object 
 20  date            42752 non-null  object 
 21  latitude        43000 non-null  float64
 22  longitude       43000 non-null  float64
 23  id_             43000 non-null  object 

Dataset de clientes: no tienen valores a null

Por tanto, no se hace nada con los valores nulos.


```python
df_campañas[df_campañas['cons.price.idx'].isnull()].date
df_campañas[df_campañas['date']=='20-septiembre-2017'].sample(5)
```




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
      <th>age</th>
      <th>job</th>
      <th>marital</th>
      <th>education</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>contact</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>poutcome</th>
      <th>emp.var.rate</th>
      <th>cons.price.idx</th>
      <th>cons.conf.idx</th>
      <th>euribor3m</th>
      <th>nr.employed</th>
      <th>y</th>
      <th>date</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>20538</th>
      <td>58.0</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>cellular</td>
      <td>225</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.4</td>
      <td>93,444</td>
      <td>-36,1</td>
      <td>4,965</td>
      <td>5228,1</td>
      <td>no</td>
      <td>20-septiembre-2017</td>
      <td>38.904</td>
      <td>-124.342</td>
      <td>6916793d-ab5d-49ad-80d4-441189a310c8</td>
    </tr>
    <tr>
      <th>12895</th>
      <td>32.0</td>
      <td>services</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>cellular</td>
      <td>302</td>
      <td>3</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.4</td>
      <td>93,918</td>
      <td>-42,7</td>
      <td>NaN</td>
      <td>5228,1</td>
      <td>no</td>
      <td>20-septiembre-2017</td>
      <td>38.158</td>
      <td>-107.311</td>
      <td>3757228c-039a-492c-9e43-887ffc7bd49a</td>
    </tr>
    <tr>
      <th>8636</th>
      <td>38.0</td>
      <td>blue-collar</td>
      <td>MARRIED</td>
      <td>basic.6y</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>382</td>
      <td>3</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.4</td>
      <td>94,465</td>
      <td>-41,8</td>
      <td>4,864</td>
      <td>5228,1</td>
      <td>no</td>
      <td>20-septiembre-2017</td>
      <td>45.867</td>
      <td>-85.985</td>
      <td>5d92dc4e-e479-4eb9-8627-16bb3f05a84d</td>
    </tr>
    <tr>
      <th>22136</th>
      <td>NaN</td>
      <td>management</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>cellular</td>
      <td>137</td>
      <td>2</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.4</td>
      <td>93,444</td>
      <td>-36,1</td>
      <td>4,963</td>
      <td>5228,1</td>
      <td>no</td>
      <td>20-septiembre-2017</td>
      <td>40.838</td>
      <td>-96.981</td>
      <td>59a2345b-e4d4-4921-bbd7-9c81997184c5</td>
    </tr>
    <tr>
      <th>2392</th>
      <td>58.0</td>
      <td>retired</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>104</td>
      <td>2</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,856</td>
      <td>5191</td>
      <td>no</td>
      <td>20-septiembre-2017</td>
      <td>32.293</td>
      <td>-93.603</td>
      <td>591e48c2-f153-4ea8-9506-515871e4aaaa</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_campañas[df_campañas['date'].isnull()]
```




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
      <th>age</th>
      <th>job</th>
      <th>marital</th>
      <th>education</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>contact</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>poutcome</th>
      <th>emp.var.rate</th>
      <th>cons.price.idx</th>
      <th>cons.conf.idx</th>
      <th>euribor3m</th>
      <th>nr.employed</th>
      <th>y</th>
      <th>date</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>350</th>
      <td>49.0</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>telephone</td>
      <td>18</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5191</td>
      <td>no</td>
      <td>NaN</td>
      <td>29.423</td>
      <td>-99.684</td>
      <td>f3061d03-2c60-481b-a72a-1b3388b09235</td>
    </tr>
    <tr>
      <th>518</th>
      <td>51.0</td>
      <td>blue-collar</td>
      <td>MARRIED</td>
      <td>basic.9y</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>162</td>
      <td>2</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5191</td>
      <td>no</td>
      <td>NaN</td>
      <td>45.855</td>
      <td>-81.951</td>
      <td>6e59d0e9-010f-45df-aa8f-0903010b8581</td>
    </tr>
    <tr>
      <th>688</th>
      <td>30.0</td>
      <td>services</td>
      <td>DIVORCED</td>
      <td>basic.9y</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>telephone</td>
      <td>126</td>
      <td>2</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5191</td>
      <td>no</td>
      <td>NaN</td>
      <td>32.879</td>
      <td>-69.037</td>
      <td>9a9b1b2b-a043-48cc-9adb-1c59f77006a6</td>
    </tr>
    <tr>
      <th>878</th>
      <td>NaN</td>
      <td>admin.</td>
      <td>SINGLE</td>
      <td>basic.6y</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>telephone</td>
      <td>92</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,856</td>
      <td>5191</td>
      <td>no</td>
      <td>NaN</td>
      <td>24.580</td>
      <td>-116.124</td>
      <td>f57f2938-1da9-44d2-b6b0-5b405e209185</td>
    </tr>
    <tr>
      <th>1058</th>
      <td>28.0</td>
      <td>services</td>
      <td>SINGLE</td>
      <td>high.school</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>137</td>
      <td>2</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,855</td>
      <td>5191</td>
      <td>no</td>
      <td>NaN</td>
      <td>47.076</td>
      <td>-104.801</td>
      <td>40c0f959-d092-4144-9bb7-8ea20a917b19</td>
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
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>41912</th>
      <td>32.0</td>
      <td>self-employed</td>
      <td>MARRIED</td>
      <td>professional.course</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>telephone</td>
      <td>65</td>
      <td>5</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>-1.8</td>
      <td>92,893</td>
      <td>-46,2</td>
      <td>1,25</td>
      <td>5099,1</td>
      <td>no</td>
      <td>NaN</td>
      <td>35.335</td>
      <td>-92.796</td>
      <td>eb69d433-47e5-4c7b-ad87-409e6454d42e</td>
    </tr>
    <tr>
      <th>42722</th>
      <td>31.0</td>
      <td>blue-collar</td>
      <td>MARRIED</td>
      <td>basic.9y</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>115</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>-0.1</td>
      <td>93,2</td>
      <td>-42</td>
      <td>4,474</td>
      <td>5195,8</td>
      <td>yes</td>
      <td>NaN</td>
      <td>36.020</td>
      <td>-117.945</td>
      <td>e3523c55-7944-4d57-8a07-376ddd95a834</td>
    </tr>
    <tr>
      <th>42816</th>
      <td>NaN</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>cellular</td>
      <td>242</td>
      <td>2</td>
      <td>7</td>
      <td>1</td>
      <td>SUCCESS</td>
      <td>-1.8</td>
      <td>92,893</td>
      <td>-46,2</td>
      <td>1,27</td>
      <td>5099,1</td>
      <td>no</td>
      <td>NaN</td>
      <td>47.396</td>
      <td>-79.839</td>
      <td>05256efd-1e16-4972-b3fd-0ea300537a35</td>
    </tr>
    <tr>
      <th>42847</th>
      <td>NaN</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>cellular</td>
      <td>68</td>
      <td>2</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.4</td>
      <td>93,444</td>
      <td>-36,1</td>
      <td>NaN</td>
      <td>5228,1</td>
      <td>no</td>
      <td>NaN</td>
      <td>24.446</td>
      <td>-93.241</td>
      <td>a156e227-1b1a-4caa-80cc-7722ea4a34ce</td>
    </tr>
    <tr>
      <th>42967</th>
      <td>29.0</td>
      <td>technician</td>
      <td>SINGLE</td>
      <td>professional.course</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>cellular</td>
      <td>202</td>
      <td>1</td>
      <td>999</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.4</td>
      <td>93,444</td>
      <td>-36,1</td>
      <td>4,964</td>
      <td>5228,1</td>
      <td>no</td>
      <td>NaN</td>
      <td>41.329</td>
      <td>-104.864</td>
      <td>dc5c48ff-1553-4c9a-8be0-a6870f9c7a03</td>
    </tr>
  </tbody>
</table>
<p>248 rows × 23 columns</p>
</div>




```python
df_clientes_2012.isnull().sum()
df_clientes_2013.isnull().sum()
df_clientes_2014.isnull().sum()
```




    Income               0
    Kidhome              0
    Teenhome             0
    Dt_Customer          0
    NumWebVisitsMonth    0
    ID                   0
    dtype: int64



#### 2.1.2 ELIMINACIÓN DE DUPLICADOS

En el dataset de las campañas de marketing no hay duplicados (la columa ID tiene siempre valores distintos).

En el dataset de Clientes tampoco existen duplicados (la columna ID tiene valores distintos.)

Sin embargo, los dataset de clientes, se refieren a datos de clientes en tres años. Por tanto se podrían consolidar en un único dataset con la información más actualizada de cada cliente.


#### 2.1.3 CORRRECIÓN DE INCONSISTENCIAS

Para cada  dataset, se analizan los valores de cada variable (métdo dataframe_value_conts)

En el dataset de las campañas de marketing.

- "age": son valores correctos entre 31 y 94 años (aparecen unos outliers a descartar en fase posteriores)

(edad/cantidad)
31.0    1810
33.0    1704
32.0    1672
36.0    1649
34.0    1608
        ... 
91.0       2
98.0       2
95.0       1
87.0       1
94.0       1

- "default", tiene valores correctos, 0 o 1

(valor/cantidad)
0.0    34016
1.0        3
- "housing",  tiene valores correctos, 0 o 1

(valor/cantidad)

1.0    22498
0.0    19476
- "loan", tiene valores correctos, 0 o 1

(valor/cantidad)
0.0    35442
1.0     6532

- "duration", tiene valores correctos, pero tiene algunos outliers parece (duraciones de más de 1400 s )

90      183
136     177
85      177
73      172
111     170
       ... 
1416      1
1603      1
1361      1
2219      1
1048      1

- campaign, tiene valores correctos, pero tiene algunos outliers, más de 50 contactos
1     18404
2     11048
3      5584
4      2777
5      1658
6      1025
7       658
8       418
9       289
10      236
11      183
12      130
13       94
14       73
17       61
16       51
15       51
18       34
20       30
19       26
21       26
23       20
24       18
22       17
...
56        1
37        1
41        1

- "pdays", tiene un valor de 999, parece un outlier, dado que la escala es de 3-20, se sugiere dejarlo a nulo

999    41412
3        457
6        428
4        121
9         67
7         66
2         63
12        61
10        55
5         50
13        38
11        30
15        26
1         26
14        22
8         20
0         15
16        13
17        10
18         7
22         4
19         3
21         2
25         1
26         1
27         1
20         1
- "previous",  tiene sentido,

0    37103
1     4778
2      786
3      232
4       75
5       20
6        5
7        1

- "poutcome", tiene sentido,

NONEXISTENT    37103
FAILURE         4461
SUCCESS         1436

- "emp.var.rate", parece que tiene sentido,

1.4    16980
-1.8     9620
 1.1     8020
-0.1     3830
-2.9     1748
-3.4     1133
-1.7      812
-1.1      665
-3.0      181
-0.2       11

- cons.price.idx, parece que tiene sentido,

93,994    7938
93,918    6937
92,893    5985
93,444    5349
94,465    4522
93,2      3731
93,075    2552
92,201     803
92,963     742
92,431     471
92,649     375
94,215     320
94,199     316
92,843     298
92,379     274
93,369     271
94,027     242
94,055     236
93,876     224
94,601     212
92,469     184
92,713     179
93,749     179
94,767     130
93,798      48
92,756      11

- cons.conf.idx, igualmente parece que tiene sentido,

-36,4    8020
-42,7    7004
-46,2    6057
-36,1    5408
-41,8    4568
-42      3782
-47,1    2581
-31,4     815
-40,8     748
-26,9     477
-30,1     379
-40,3     328
-37,5     318
-50       299
-29,8     277
-34,8     275
-38,3     245
-39,8     239
-40       225
-49,5     214
-33,6     185
-34,6     183
-33       181
-50,8     133
-40,4      48
-45,9      11

- euribor3m, parece que tiene sentido

4,857    2287
4,962    2124
4,963    2019
4,961    1594
4,964     964
         ... 
5,045       1
4,921       1
0,895       1
0,956       1
0,953       1

- nr.employed, parece que igualmente tiene sentido

5228,1    16980
5099,1     8937
5191       8020
5195,8     3830
5076,2     1748
5017,5     1133
4991,6      812
5008,7      683
4963,6      665
5023,5      181
5176,3       11

- y, tiene sentido
no     38156
yes     4844

- date, igualmente tiene sentido

28-febrero-2018      95
28-febrero-2016      94
28-febrero-2015      92
28-febrero-2017      89
28-febrero-2019      85
                     ..
17-diciembre-2018    11
21-diciembre-2019    10
14-abril-2015        10
4-abril-2017         10
20-noviembre-2015    10
-latitude, tiene sentido
44.675    10
36.826     9
30.579     8
44.335     8
32.888     8
          ..
28.986     1
47.455     1
45.137     1
34.651     1
32.875     1

- longitude, tiene sentido

 -80.000     6
-84.358     6
-101.604    6
-88.922     6
-105.981    6
           ..
-109.835    1
-70.852     1
-85.121     1
-90.902     1
-85.811     1

- id, que es el id único, y tampoco tiene duplicados

993bbbd6-4dbc-4a40-a408-f91f8462bee6    1
089b39d8-e4d0-461b-87d4-814d71e0e079    1
e9d37224-cb6f-4942-98d7-46672963d097    1
3f9f49b5-e410-4948-bf6e-f9244f04918b    1
9991fafb-4447-451a-8be2-b0df6098d13e    1
                                       ..
66a752e2-b2f1-440c-9a8f-cc3b10d74dd0    1
ca5c0d7a-8bbd-42b3-8351-1074c5be011a    1
8d700df5-0c33-4517-8cf8-92e1c92c9c12    1
ea6b7d04-9271-4c0a-a01f-07795d164aba    1
87b79988-2be5-419d-88f4-56655852c565    1


En el dataset de clientes:

- Income: es correcto

70422     4
65309     3
45659     3
66865     3
47863     3
         ..
85356     1
118851    1
54609     1
75488     1
28737     1

- Kidhome, tiene sentido

2    6752
1    6695
0    6668
- Teenhome, tiene sentido

1    6745
0    6690
2    6680

- Dt_Customer, tiene sentido,

2012-08-10    79
2012-01-11    79
2012-12-28    75
2012-08-13    72
2012-11-25    72
              ..
2012-07-08    40
2012-02-03    40
2012-01-10    39
2012-01-31    37
2012-05-02    35

- NumWebVisitsMonth, tiene sentido

12    663
8     657
26    653
3     652
32    651
4     650
22    650
10    649
28    646
20    643
14    643
30    636
21    636
13    631
23    629
15    629
6     629
29    628
25    627
11    626
31    621
9     617
27    617
5     617
...
16    596
1     588
19    588

-id, tiene sentido y no tiene duplicados,
8299f0a6-e4c0-49fc-87ab-81308b70f80f    1
089b39d8-e4d0-461b-87d4-814d71e0e079    1
e9d37224-cb6f-4942-98d7-46672963d097    1
71100141-53d0-4913-9750-f730875080c7    1
afd96190-4abd-404c-87a6-74cc40caebec    1
                                       ..
87fdc08b-30ae-4dab-803f-561ecdf27ff0    1
5e3483e5-236d-437d-8351-541f9d09b9dd    1
d63ede72-0b6d-45b1-8872-385ac6897f65    1
eca60b76-70b6-4077-80ba-bc52e8ebb0eb    1
9991fafb-4447-451a-8be2-b0df6098d13e    1

### 2. 2 TRANSFORMACIÓN DE DATOS

#### 2.2.1 Formateo de fechas

En el dataset de campañas hay una variables que es de fechas, "date".  "date", es un tipo string con la fecha en formato "dd-mes(nombre)-año", procedemos a convertirlo a tipo date y se añaden cuatro columnas, "date_c" fecha completa, "year_c", año, "day_c", día, y "weekday_c", día de la semana, para poder realizar análisis por series temporables y tendencias estacionales.
Lo primero se importa el módulo datetime que tiene los objetos y métodos de fecha.
Creamos una función de conversión "convertir_fecha" que toma como parámetro un string en formato fecha tal y como está en fichero y se convierte a objeto date de datetime.
Después con el método apply se añaden las tres columnas. Importante gestionar la excepción de los valores nulos del campo "date".



```python
print(df_campañas['date'].dtype)
```

    object
    


```python
import datetime as dt

def convertir_date(fecha_a_convertir):

      
    meses={"enero":1, "febrero":2, "marzo":3, "abril":4 , "mayo":5, "junio":6, "julio":7, "agosto":8, "septiembre":9, "octubre":10, "noviembre":11, "diciembre":12 }
    
    try:
        fecha_str_split=str(fecha_a_convertir).split('-')
        return dt.date(int(fecha_str_split[2]),int(meses.get(fecha_str_split[1])), int(fecha_str_split[0]))
    except ValueError:
        return None
    except TypeError:
        return None
    except IndexError:
        return None
       

df_campañas['date_c']=df_campañas['date'].apply(convertir_date) 

df_campañas['year_c']=df_campañas['date_c'].apply(lambda x: None if x==None else x.year) 

df_campañas['month_c']=df_campañas['date_c'].apply(lambda x: None if x==None else x.month) 

df_campañas['quarter_c']=df_campañas['month_c'].apply(lambda x: None if x==None else round((x/3)+0.5,0))

df_campañas['day_c']=df_campañas['date_c'].apply(lambda x: None if x==None else x.day) 

weekday_nombre=["lunes","martes","miércoles","jueves","viernes","sábado","domingo"]

df_campañas['weekday_c']=df_campañas['date_c'].apply(lambda x: None if x==None else weekday_nombre[int(x.weekday())])



```

En el dataset de clientes hay un campo fecha, "Dt_Customer", que ya es de tipo fecha y no tiene valores nulos.
Tan sólo se añaden los campos (columnas), año, mes, trimestre, día, y día de la semana para poder realizar análisis por series temporales o  tendencias estacionales.
Para normalizar el nombre de estas nuevas variables el nombre es concatenar el nombre original con el literal year_c, month_c, quarter_c, day_c, weekday_c.



```python
df_clientes_2012['Dt_Customer'].info()

df_clientes_2012['Dt_Customer_year_c']=df_clientes_2012['Dt_Customer'].apply(lambda x: x.year)

df_clientes_2012['Dt_Customer_month_c']=df_clientes_2012['Dt_Customer'].apply(lambda x: x.month)

df_clientes_2012['Dt_Customer_quarter_c']=df_clientes_2012['Dt_Customer_month_c'].apply(lambda x: None if x==None else round((x/3)+0.5,0))

df_clientes_2012['Dt_Customer_day_c']=df_clientes_2012['Dt_Customer'].apply(lambda x: x.day)

df_clientes_2012['Dt_Customer_weekday_c']=df_clientes_2012['Dt_Customer'].apply(lambda x:  weekday_nombre[int(x.weekday())])


df_clientes_2013['Dt_Customer'].info()

df_clientes_2013['Dt_Customer_year_c']=df_clientes_2013['Dt_Customer'].apply(lambda x: x.year)

df_clientes_2013['Dt_Customer_month_c']=df_clientes_2013['Dt_Customer'].apply(lambda x: x.month)

df_clientes_2013['Dt_Customer_quarter_c']=df_clientes_2013['Dt_Customer_month_c'].apply(lambda x: None if x==None else round((x/3)+0.5,0))

df_clientes_2013['Dt_Customer_day_c']=df_clientes_2013['Dt_Customer'].apply(lambda x: x.day)

df_clientes_2013['Dt_Customer_weekday_c']=df_clientes_2013['Dt_Customer'].apply(lambda x:  weekday_nombre[int(x.weekday())])


df_clientes_2014['Dt_Customer'].info()

df_clientes_2014['Dt_Customer_year_c']=df_clientes_2014['Dt_Customer'].apply(lambda x: x.year)

df_clientes_2014['Dt_Customer_month_c']=df_clientes_2014['Dt_Customer'].apply(lambda x: x.month)

df_clientes_2014['Dt_Customer_quarter_c']=df_clientes_2014['Dt_Customer_month_c'].apply(lambda x: None if x==None else round((x/3)+0.5,0))

df_clientes_2014['Dt_Customer_day_c']=df_clientes_2014['Dt_Customer'].apply(lambda x: x.day)

df_clientes_2014['Dt_Customer_weekday_c']=df_clientes_2014['Dt_Customer'].apply(lambda x:  weekday_nombre[int(x.weekday())])

df_clientes['Dt_Customer'].info()

df_clientes['Dt_Customer_year_c']=df_clientes['Dt_Customer'].apply(lambda x: x.year)

df_clientes['Dt_Customer_month_c']=df_clientes['Dt_Customer'].apply(lambda x: x.month)

df_clientes['Dt_Customer_quarter_c']=df_clientes['Dt_Customer_month_c'].apply(lambda x: None if x==None else round((x/3)+0.5,0))

df_clientes['Dt_Customer_day_c']=df_clientes['Dt_Customer'].apply(lambda x: x.day)

df_clientes['Dt_Customer_weekday_c']=df_clientes['Dt_Customer'].apply(lambda x:  weekday_nombre[int(x.weekday())])

```

    <class 'pandas.core.series.Series'>
    RangeIndex: 20115 entries, 0 to 20114
    Series name: Dt_Customer
    Non-Null Count  Dtype         
    --------------  -----         
    20115 non-null  datetime64[ns]
    dtypes: datetime64[ns](1)
    memory usage: 157.3 KB
    <class 'pandas.core.series.Series'>
    RangeIndex: 8965 entries, 0 to 8964
    Series name: Dt_Customer
    Non-Null Count  Dtype         
    --------------  -----         
    8965 non-null   datetime64[ns]
    dtypes: datetime64[ns](1)
    memory usage: 70.2 KB
    <class 'pandas.core.series.Series'>
    RangeIndex: 14090 entries, 0 to 14089
    Series name: Dt_Customer
    Non-Null Count  Dtype         
    --------------  -----         
    14090 non-null  datetime64[ns]
    dtypes: datetime64[ns](1)
    memory usage: 110.2 KB
    <class 'pandas.core.series.Series'>
    Index: 43170 entries, 0 to 20114
    Series name: Dt_Customer
    Non-Null Count  Dtype         
    --------------  -----         
    43170 non-null  datetime64[ns]
    dtypes: datetime64[ns](1)
    memory usage: 674.5 KB
    

#### 2.2.2 Agrupación

Se van a agrupar datos numéricos continuos en categorías, por ejemplo edades e ingresos.

agrupar edades en rangos (“18-25”, “26-35”, etc.) o clasificar ingresos en niveles (“bajo”, “medio”, “alto”).

En el primer dataset de campañas de marketing, creamos una columna nueva que es la categoría de edad "age_category_c"

● Jóvenes adultos: De 18 a 28 años.
● Adultos jóvenes: De 29 a 39 años.
● Mediana edad: De 40 a 50.
● Adultos mayores: Por encima de los 51 años.
 




```python
print(df_campañas.groupby('age')['age'].count())

age_category={18: "menor edad", 29: "jóvenes adultos", 40: "adultos jóvenes", 51: "media edad", 100:"adultos mayores" }

def categoría_edad(edad):
    for n in age_category.keys():
        if edad<n:
            return age_category.get(n)

df_campañas['age_category_c']=df_campañas['age'].apply(lambda x: categoría_edad(x))


```

    age
    17.0     5
    18.0    28
    19.0    40
    20.0    63
    21.0    97
            ..
    91.0     2
    92.0     4
    94.0     1
    95.0     1
    98.0     2
    Name: age, Length: 78, dtype: int64
    

Igualmente, se categorizan los ingresos en función de la mediana de ingresos entre en bajos(0.5 media), promedio bajo(0.5 a mediana), promedio alto (1 y 2 mediana), y altos (>2 mediana). Se crea la columna, "income_category_c"




```python
mediana_income= df_clientes_2012['Income'].median()

income_category={0.5: "ingreso bajo", 1: "ingreso promedo bajo", 1.5: "ingreso promedio alto", 2:"ingreso alto" }

def categoría_ingreso(ingreso):
    
    for n in income_category.keys():
        umbral=float(n)*float(mediana_income)
        if float(ingreso)<umbral:
            return income_category.get(n)
    return "ingreso_alto"

df_clientes_2012['Income_category_c']=df_clientes_2012['Income'].apply(lambda x: categoría_ingreso(x))

df_clientes_2013['Income_category_c']=df_clientes_2013['Income'].apply(lambda x: categoría_ingreso(x))

df_clientes_2014['Income_category_c']=df_clientes_2014['Income'].apply(lambda x: categoría_ingreso(x))

df_clientes['Income_category_c']=df_clientes['Income'].apply(lambda x: categoría_ingreso(x))

print(f"la media de ingresos es {mediana_income}")
print(df_clientes_2014.groupby("Income_category_c")['Income'].median())
```

    la media de ingresos es 92962.0
    Income_category_c
    ingreso alto             160431.0
    ingreso bajo              25948.0
    ingreso promedio alto    116561.0
    ingreso promedo bajo      69612.0
    Name: Income, dtype: float64
    


```python
df_campañas.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 43000 entries, 0 to 42999
    Data columns (total 24 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   Unnamed: 0      43000 non-null  int64  
     1   age             37880 non-null  float64
     2   job             42655 non-null  object 
     3   marital         42915 non-null  object 
     4   education       41193 non-null  object 
     5   default         34019 non-null  float64
     6   housing         41974 non-null  float64
     7   loan            41974 non-null  float64
     8   contact         43000 non-null  object 
     9   duration        43000 non-null  int64  
     10  campaign        43000 non-null  int64  
     11  pdays           43000 non-null  int64  
     12  previous        43000 non-null  int64  
     13  poutcome        43000 non-null  object 
     14  emp.var.rate    43000 non-null  float64
     15  cons.price.idx  42529 non-null  object 
     16  cons.conf.idx   43000 non-null  object 
     17  euribor3m       33744 non-null  object 
     18  nr.employed     43000 non-null  object 
     19  y               43000 non-null  object 
     20  date            42752 non-null  object 
     21  latitude        43000 non-null  float64
     22  longitude       43000 non-null  float64
     23  id_             43000 non-null  object 
    dtypes: float64(7), int64(5), object(12)
    memory usage: 7.9+ MB
    

Se agrupan las latitutes y longitudes en país, estado/provincia y ciudad para poder identificar patrones por geografía.
Se utilizar la librería geopy (la he encontrado por internet)
Se crean dos columnas adicionales con el país 'Country_c",  estado 'State/Province_c", ciudad 'City_c',




```python
from geopy.geocoders import Nominatim
geolocator = Nominatim(user_agent="my_app")



def localiza(coordenadas):
    try:
        address = str(geolocator.reverse(coordenadas)).split(',')
        return address[-1], address[-2], address[-3]
    except IndexError:
        return None
    except TypeError:
        return None
    except TimeoutError:
        return None
            
df_campañas['latitude_c']=df_campañas['latitude'].apply(lambda x: None if x==None else str(x)+', ')

df_campañas['longitude_c']=df_campañas['longitude'].apply(lambda x: None if x==None else str(x))

df_campañas['latitude+longitude']=df_campañas['latitude_c']+df_campañas['longitude_c']

print(df_campañas['latitude+longitude'].head(1))

print(localiza(df_campañas['latitude+longitude'][1]))

coordenadas=list(df_campañas.loc[:,'latitude+longitude'])

localización=[]

for n in coordenadas:
    localización.append(localiza(n))


df_campañas['Country_c']=localización[0]

df_campañas['State/Province_c']=localización[1]

df_campañas['City_c']=localización[2]


```

    0    41.495, -71.233
    Name: latitude+longitude, dtype: object
    (' United States', ' Georgia', ' Lumpkin County')
    

### 2.3 Manejo de Outliers

Identificamos outliers en el primer DataSet, campañas de marketing, como se vió anteriormente:

Si el número total de outliers es relativamente bajo comparado con el número total de datos, se eliminan.

Se considera outlier a un valor por debajo del valor q1 menos 1,5 el IQR , y por encima del q3 más 1,5 el IQR.


```python
df_campañas.describe()



```




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
      <th>age</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>emp.var.rate</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>year_c</th>
      <th>month_c</th>
      <th>quarter_c</th>
      <th>day_c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>37880.000000</td>
      <td>34019.000000</td>
      <td>41974.000000</td>
      <td>41974.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>43000.000000</td>
      <td>42752.000000</td>
      <td>42752.000000</td>
      <td>42752.000000</td>
      <td>42752.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>39.977112</td>
      <td>0.000088</td>
      <td>0.535998</td>
      <td>0.155620</td>
      <td>257.739279</td>
      <td>2.567233</td>
      <td>962.330953</td>
      <td>0.174023</td>
      <td>0.077128</td>
      <td>36.856697</td>
      <td>-95.939067</td>
      <td>2017.001310</td>
      <td>6.502058</td>
      <td>2.668764</td>
      <td>16.022853</td>
    </tr>
    <tr>
      <th>std</th>
      <td>10.437957</td>
      <td>0.009390</td>
      <td>0.498708</td>
      <td>0.362499</td>
      <td>258.666033</td>
      <td>2.772294</td>
      <td>187.260394</td>
      <td>0.497366</td>
      <td>1.573898</td>
      <td>7.225948</td>
      <td>16.752282</td>
      <td>1.414312</td>
      <td>3.448718</td>
      <td>1.105257</td>
      <td>8.922815</td>
    </tr>
    <tr>
      <th>min</th>
      <td>17.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-3.400000</td>
      <td>24.396000</td>
      <td>-124.997000</td>
      <td>2015.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>32.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>102.000000</td>
      <td>1.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>-1.800000</td>
      <td>30.614750</td>
      <td>-110.494250</td>
      <td>2016.000000</td>
      <td>4.000000</td>
      <td>2.000000</td>
      <td>8.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>38.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>179.000000</td>
      <td>2.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>1.100000</td>
      <td>36.761000</td>
      <td>-95.899500</td>
      <td>2017.000000</td>
      <td>7.000000</td>
      <td>3.000000</td>
      <td>16.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>47.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>319.000000</td>
      <td>3.000000</td>
      <td>999.000000</td>
      <td>0.000000</td>
      <td>1.400000</td>
      <td>43.113250</td>
      <td>-81.427750</td>
      <td>2018.000000</td>
      <td>10.000000</td>
      <td>4.000000</td>
      <td>24.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>98.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>4918.000000</td>
      <td>56.000000</td>
      <td>999.000000</td>
      <td>7.000000</td>
      <td>1.400000</td>
      <td>49.384000</td>
      <td>-66.937000</td>
      <td>2019.000000</td>
      <td>12.000000</td>
      <td>4.000000</td>
      <td>31.000000</td>
    </tr>
  </tbody>
</table>
</div>


Existen outliers en "age", "campaign", "pdays", "duration" hay que analizarlo.

En "age" existen 447 outliers de 37880 valores no nulos, un 1,16% por lo que la decisión es eliminar todas las filas de los outliers.

En "campaign" existen 3529 outliers de 43000 valores no nulos, por tanto se deciden mantener estos valores.

En "pdays" hay 43000 valores no nulos y 41412 valores que son 999 que parecen outliers. Al ser pdays el número de días que han pasado desde la última vez que se contactó con el cliente durante esta campaña, se pone este valor a null para que no interfiera en valores agregados, mantiéndose las filas.

En "duration", hay 42559 valores no nulos y 3085 outliers, se deciden eliminar.




```python
quantiles_age=list(df_campañas['age'].quantile([0.25, 0.50, 0.75]))

condicion_outliers_age= (df_campañas['age']<=quantiles_age[0]-1.5*(quantiles_age[2]-quantiles_age[0]))  | (df_campañas['age']>=quantiles_age[2]+1.5*(quantiles_age[2]-quantiles_age[0]))

df_campañas['age'].info()

df_campañas[condicion_outliers_age]['age'].count()

```

    <class 'pandas.core.series.Series'>
    RangeIndex: 43000 entries, 0 to 42999
    Series name: age
    Non-Null Count  Dtype  
    --------------  -----  
    37880 non-null  float64
    dtypes: float64(1)
    memory usage: 336.1 KB
    




    np.int64(441)




```python
quantiles_campaign=list(df_campañas['campaign'].quantile([0.25, 0.50, 0.75]))

print(quantiles_campaign)

condicion_outliers_campaign= (df_campañas['campaign']<=quantiles_campaign[0]-(1.5*(quantiles_campaign[2]-quantiles_campaign[0])))  | (df_campañas['campaign']>=quantiles_campaign[2]+(1.5*(quantiles_campaign[2]-quantiles_campaign[0])))

#df_campañas['campaign'].info()

df_campañas[condicion_outliers_campaign]['campaign'].count()

```

    [1.0, 2.0, 3.0]
    




    np.int64(3529)




```python
df_campañas['pdays'].value_counts()
```




    pdays
    999    41412
    3        457
    6        428
    4        121
    9         67
    7         66
    2         63
    12        61
    10        55
    5         50
    13        38
    11        30
    15        26
    1         26
    14        22
    8         20
    0         15
    16        13
    17        10
    18         7
    22         4
    19         3
    21         2
    25         1
    26         1
    27         1
    20         1
    Name: count, dtype: int64




```python
df_campañas.drop(df_campañas[condicion_outliers_age].index, axis=0, inplace=True)

df_campañas['age'].value_counts()

```




    age
    31.0    1810
    33.0    1704
    32.0    1672
    36.0    1649
    34.0    1608
    30.0    1605
    35.0    1600
    29.0    1350
    37.0    1343
    39.0    1323
    38.0    1295
    41.0    1174
    40.0    1067
    42.0    1043
    45.0    1026
    43.0     954
    28.0     941
    44.0     929
    46.0     926
    48.0     891
    47.0     876
    50.0     795
    27.0     779
    49.0     764
    52.0     707
    51.0     687
    53.0     682
    56.0     638
    26.0     636
    54.0     625
    57.0     592
    55.0     589
    25.0     566
    58.0     519
    24.0     430
    59.0     424
    60.0     251
    23.0     200
    22.0     131
    21.0      97
    61.0      69
    20.0      63
    62.0      55
    64.0      54
    66.0      53
    63.0      52
    19.0      40
    65.0      40
    69.0      33
    18.0      28
    68.0      28
    67.0      21
    17.0       5
    Name: count, dtype: int64




```python
df_campañas['pdays']=df_campañas['pdays'].apply(lambda x:  None if x==999 else x)
```


```python
quantiles_duration=list(df_campañas['duration'].quantile([0.25, 0.50, 0.75]))

print(quantiles_duration)

condicion_outliers_duration= (df_campañas['duration']<=quantiles_duration[0]-(1.5*(quantiles_duration[2]-quantiles_duration[0])))  | (df_campañas['duration']>=quantiles_duration[2]+(1.5*(quantiles_duration[2]-quantiles_duration[0])))

print(df_campañas['campaign'].info())

df_campañas[condicion_outliers_duration]['duration'].count()
```

    [102.0, 179.0, 318.0]
    <class 'pandas.core.series.Series'>
    Index: 42559 entries, 0 to 42999
    Series name: campaign
    Non-Null Count  Dtype
    --------------  -----
    42559 non-null  int64
    dtypes: int64(1)
    memory usage: 665.0 KB
    None
    




    np.int64(3085)




```python
df_campañas.drop(df_campañas[condicion_outliers_duration].index, axis=0, inplace=True)

df_campañas['duration'].value_counts()
```




    duration
    90     180
    136    177
    85     175
    73     171
    111    169
          ... 
    621      4
    3        4
    0        4
    1        2
    2        1
    Name: count, Length: 642, dtype: int64



### 2.4 Conversión de variables categóricas

Del análisis inicial no se considera realizar reasignar categorías o convertir variables.


### 2.5 Creación de variables derivadas

Se introduce una variable adicional en el dataset de campañas de marketing para incluir la antiguedad de cliente en el momento de la acción de marketing. 
Para ello se va a hacer un merge entre las campañas y los clientes para después se añade un campo al dataset de campañas con "antiguedad cliente", "aging_client_c" con la diferencia.

En el merge entre los dos dataset:
- se realiza un concat de los ficheros de clientes, se verifica que no hay ningún duplicado de ID
- se verifica que el campo ID de dataset de campañas coincide con el campo ID del dataset de clientes, denominado df_clientes
- se hace el merge, resultando campañas_clientes
- sobre este merge, se crea la variable derivada "aging_client_c" como diferencia entre los campos "date_c" (fecha normalizada de la realización de la campaña) y "Dt_Customer" (fecha normalizada de alta del cliente). El resultado es una variable de tipo interval. "Dt_Customer" se cambio de tipo a datetime para poder ser restada de "date_c".




```python
df_campañas['ID']=df_campañas['ID'].apply(lambda x: str(x))
df_clientes['ID']=df_clientes['ID'].apply(lambda x: str(x))
```


```python
campaña_clientes=df_campañas.merge(df_clientes,  on='ID', how='left')
```


```python
campaña_clientes.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 39474 entries, 0 to 39473
    Data columns (total 41 columns):
     #   Column                 Non-Null Count  Dtype         
    ---  ------                 --------------  -----         
     0   age                    34726 non-null  float64       
     1   job                    39158 non-null  object        
     2   marital                39399 non-null  object        
     3   education              37844 non-null  object        
     4   default                31177 non-null  float64       
     5   housing                38532 non-null  float64       
     6   loan                   38532 non-null  float64       
     7   contact                39474 non-null  object        
     8   duration               39474 non-null  int64         
     9   campaign               39474 non-null  int64         
     10  pdays                  1354 non-null   float64       
     11  previous               39474 non-null  int64         
     12  poutcome               39474 non-null  object        
     13  emp.var.rate           39474 non-null  float64       
     14  cons.price.idx         39053 non-null  object        
     15  cons.conf.idx          39474 non-null  object        
     16  euribor3m              30949 non-null  object        
     17  nr.employed            39474 non-null  object        
     18  y                      39474 non-null  object        
     19  date                   39242 non-null  object        
     20  latitude               39474 non-null  float64       
     21  longitude              39474 non-null  float64       
     22  ID                     39474 non-null  object        
     23  date_c                 39242 non-null  object        
     24  year_c                 39242 non-null  float64       
     25  month_c                39242 non-null  float64       
     26  quarter_c              39242 non-null  float64       
     27  day_c                  39242 non-null  float64       
     28  weekday_c              39242 non-null  object        
     29  age_category_c         34726 non-null  object        
     30  Income                 39474 non-null  int64         
     31  Kidhome                39474 non-null  int64         
     32  Teenhome               39474 non-null  int64         
     33  Dt_Customer            39474 non-null  datetime64[ns]
     34  NumWebVisitsMonth      39474 non-null  int64         
     35  Dt_Customer_year_c     39474 non-null  int64         
     36  Dt_Customer_month_c    39474 non-null  int64         
     37  Dt_Customer_quarter_c  39474 non-null  float64       
     38  Dt_Customer_day_c      39474 non-null  int64         
     39  Dt_Customer_weekday_c  39474 non-null  object        
     40  Income_category_c      39474 non-null  object        
    dtypes: datetime64[ns](1), float64(13), int64(10), object(17)
    memory usage: 12.3+ MB
    


```python
campaña_clientes.head(5)
```




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
      <th>age</th>
      <th>job</th>
      <th>marital</th>
      <th>education</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>contact</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>poutcome</th>
      <th>emp.var.rate</th>
      <th>cons.price.idx</th>
      <th>cons.conf.idx</th>
      <th>euribor3m</th>
      <th>nr.employed</th>
      <th>y</th>
      <th>date</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>ID</th>
      <th>date_c</th>
      <th>year_c</th>
      <th>month_c</th>
      <th>quarter_c</th>
      <th>day_c</th>
      <th>weekday_c</th>
      <th>age_category_c</th>
      <th>Income</th>
      <th>Kidhome</th>
      <th>Teenhome</th>
      <th>Dt_Customer</th>
      <th>NumWebVisitsMonth</th>
      <th>Dt_Customer_year_c</th>
      <th>Dt_Customer_month_c</th>
      <th>Dt_Customer_quarter_c</th>
      <th>Dt_Customer_day_c</th>
      <th>Dt_Customer_weekday_c</th>
      <th>Income_category_c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>housemaid</td>
      <td>MARRIED</td>
      <td>basic.4y</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>261</td>
      <td>1</td>
      <td>NaN</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5191</td>
      <td>no</td>
      <td>2-agosto-2019</td>
      <td>41.495</td>
      <td>-71.233</td>
      <td>089b39d8-e4d0-461b-87d4-814d71e0e079</td>
      <td>2019-08-02</td>
      <td>2019.0</td>
      <td>8.0</td>
      <td>3.0</td>
      <td>2.0</td>
      <td>viernes</td>
      <td>None</td>
      <td>161770</td>
      <td>1</td>
      <td>0</td>
      <td>2012-04-04</td>
      <td>29</td>
      <td>2012</td>
      <td>4</td>
      <td>2.0</td>
      <td>4</td>
      <td>miércoles</td>
      <td>ingreso alto</td>
    </tr>
    <tr>
      <th>1</th>
      <td>57.0</td>
      <td>services</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>149</td>
      <td>1</td>
      <td>NaN</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>NaN</td>
      <td>5191</td>
      <td>no</td>
      <td>14-septiembre-2016</td>
      <td>34.601</td>
      <td>-83.923</td>
      <td>e9d37224-cb6f-4942-98d7-46672963d097</td>
      <td>2016-09-14</td>
      <td>2016.0</td>
      <td>9.0</td>
      <td>4.0</td>
      <td>14.0</td>
      <td>miércoles</td>
      <td>adultos mayores</td>
      <td>85477</td>
      <td>1</td>
      <td>1</td>
      <td>2012-12-30</td>
      <td>7</td>
      <td>2012</td>
      <td>12</td>
      <td>4.0</td>
      <td>30</td>
      <td>domingo</td>
      <td>ingreso promedo bajo</td>
    </tr>
    <tr>
      <th>2</th>
      <td>37.0</td>
      <td>services</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>226</td>
      <td>1</td>
      <td>NaN</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>4,857</td>
      <td>5191</td>
      <td>no</td>
      <td>15-febrero-2019</td>
      <td>34.939</td>
      <td>-94.847</td>
      <td>3f9f49b5-e410-4948-bf6e-f9244f04918b</td>
      <td>2019-02-15</td>
      <td>2019.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>15.0</td>
      <td>viernes</td>
      <td>adultos jóvenes</td>
      <td>147233</td>
      <td>1</td>
      <td>1</td>
      <td>2012-02-02</td>
      <td>5</td>
      <td>2012</td>
      <td>2</td>
      <td>1.0</td>
      <td>2</td>
      <td>jueves</td>
      <td>ingreso alto</td>
    </tr>
    <tr>
      <th>3</th>
      <td>40.0</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>basic.6y</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>telephone</td>
      <td>151</td>
      <td>1</td>
      <td>NaN</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>NaN</td>
      <td>5191</td>
      <td>no</td>
      <td>29-noviembre-2015</td>
      <td>49.041</td>
      <td>-70.308</td>
      <td>9991fafb-4447-451a-8be2-b0df6098d13e</td>
      <td>2015-11-29</td>
      <td>2015.0</td>
      <td>11.0</td>
      <td>4.0</td>
      <td>29.0</td>
      <td>domingo</td>
      <td>media edad</td>
      <td>121393</td>
      <td>1</td>
      <td>2</td>
      <td>2012-12-21</td>
      <td>29</td>
      <td>2012</td>
      <td>12</td>
      <td>4.0</td>
      <td>21</td>
      <td>viernes</td>
      <td>ingreso promedio alto</td>
    </tr>
    <tr>
      <th>4</th>
      <td>56.0</td>
      <td>services</td>
      <td>MARRIED</td>
      <td>high.school</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>telephone</td>
      <td>307</td>
      <td>1</td>
      <td>NaN</td>
      <td>0</td>
      <td>NONEXISTENT</td>
      <td>1.1</td>
      <td>93,994</td>
      <td>-36,4</td>
      <td>NaN</td>
      <td>5191</td>
      <td>no</td>
      <td>29-enero-2017</td>
      <td>38.033</td>
      <td>-104.463</td>
      <td>eca60b76-70b6-4077-80ba-bc52e8ebb0eb</td>
      <td>2017-01-29</td>
      <td>2017.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>29.0</td>
      <td>domingo</td>
      <td>adultos mayores</td>
      <td>63164</td>
      <td>1</td>
      <td>2</td>
      <td>2012-06-20</td>
      <td>20</td>
      <td>2012</td>
      <td>6</td>
      <td>2.0</td>
      <td>20</td>
      <td>miércoles</td>
      <td>ingreso promedo bajo</td>
    </tr>
  </tbody>
</table>
</div>




```python
campaña_clientes['Dt_Customer']=campaña_clientes['Dt_Customer'].apply(lambda x: None if x==None else x.date())
```


```python
campaña_clientes['aging_c']=(campaña_clientes['date_c']-campaña_clientes['Dt_Customer'])
```

## 3 EDA: Patrones e Identificación de Anomalías

### 3.1. Identificación de patrones

#### 3.1.1 Tendencias generales de cada variable

  Distribuciones de frecuencia.
  - Edad
  'age': edad del cliente.  Media 39.54 años, desviación típica 9.6 años, mínima 17 y máxima 69 (se eliminaron los outliers), con quantiles en 32, 38, y 47.
  Se grafica con matplotlib en histograma y se aprecia que la edad se concentra entre los 30-50 años y particularmente entre los 30-40 años.
  Con la variable auxilliar categoría de edad "age_category_c', se aprecia que adultos jóvenes tiene el 45.3% de los clientes.
  - Ocupación
  'job', ocupación. El 25% son adminstrativos, el 22% son blue-collar, y el 16,6% technician. 
- Estado civil
'marital'. El 60.5% está casado, el 28.5% soltero y el 11% divorciado. Sobre todo casados.
- Nivel de estudios
"education', el 31% tiene estudios universitarios, el 24.3% tiene bachillerato (high school), y el 13.3% tiene formación profesional, aunque un 15% tiene la educación secundaria.
- Default
"default" no tienen historial de impagos.
- Hipotecas
'housing' si el cliente tiene algún crédito hipotecario.  53.6% tiene crédito hipotecario.
- Préstamos personales
84.5% tienen créditos personales
- Método de contacto
'contact'. 63,5% móvil y el 36.5% es con teléfono fijo
- Duración de la última interacción
'duration'. La media es 257 segundos y la desviación estándar son 259 segundos, con los cuartiles en 102, 179, 318 segundos. En el histograma se aprecia que la inmensa mayoría están entre 0-500 segundos.
- Número de contactos de la campaña para este cliente
'campaign". La media son 2.57 contactos y la desviación estándard son 2.78 segundos, con quartiles en 1, 2, y 3 contactos. En el pie chart, se aprecia que 42.7% tiene un contacto, 25.7% tiene dos contactos, 13.0% tres contactos, como principales categorías
- Número de veces que se ha contactado con el cliente antes de esta campaña.
'pdays', la media son 6,12 veces, la variación estándard son 3,9 y cuartiles en 25%, 50%, 75%. 
- Resultado de la campaña de marketing anterior.
'poutcome', no hay datos para un 86%, éxito 10,2% y fracaso 3,2%
- La tasa de variación del empleo.
emp.var.rate:  El índice de precios al consumidor. La media es un 0,10, la desviación 1,5
- El número de empleados
nr.employed: media 5160 empleados, desviación estándard 71
- Si el cliente ha suscrito un producto o servicio (Sí/No).
y: 10.9% sí, resto no
- La fecha 'date' es un dato de la campaña por lo que debe ser analizado con las variables de la campaña
- Ingreso anual del cliente en términos monetarios
Income: media 93200, 50k desviación estándard, cuartiles en 49 mil 93 mil y 136mil. Se realiza boxploT
- Número de niños en el hogar del cliente.
Kidhome: media 1, y desviación estándard 0.8. Se realiza pie chart. 
- número de adolescentes en el hogar del cliente Se realiza pie chart.
Teenhome: media 0,9 y desviación estándard 0,8. Se realiza pie chart.
- Cantidad de visitas mensuales del cliente al sitio web de la empresa.
NumWebVisitsMonth: media 16, desviación típica 9, cuartiles en 17, 25 y 32. Se realiza bloxplot.

     
        


```python
campaña_clientes['cons.price.idx']=campaña_clientes['cons.price.idx'].apply(lambda x: None if x==None else str(x).replace(',','.'))

campaña_clientes['cons.price.idx']=campaña_clientes['cons.price.idx'].apply(lambda x: None if x==None else float(x))
```


```python
campaña_clientes['cons.conf.idx']=campaña_clientes['cons.conf.idx'].apply(lambda x: None if x==None else str(x).replace(',','.'))

campaña_clientes['cons.conf.idx']=campaña_clientes['cons.conf.idx'].apply(lambda x: None if x==None else float(x))
```


```python
campaña_clientes['euribor3m']=campaña_clientes['euribor3m'].apply(lambda x: None if x==None else str(x).replace(',','.'))

campaña_clientes['euribor3m']=campaña_clientes['euribor3m'].apply(lambda x: None if x==None else float(x))
```


```python
campaña_clientes.dtypes
```




    age                      float64
    job                       object
    marital                   object
    education                 object
    default                  float64
    housing                  float64
    loan                     float64
    contact                   object
    duration                   int64
    campaign                   int64
    pdays                      int64
    previous                   int64
    poutcome                  object
    emp.var.rate             float64
    cons.price.idx           float64
    cons.conf.idx            float64
    euribor3m                float64
    nr.employed               object
    y                         object
    date                      object
    latitude                 float64
    longitude                float64
    ID                        object
    date_c                    object
    year_c                   float64
    month_c                  float64
    quarter_c                float64
    day_c                    float64
    weekday_c                 object
    age_category_c            object
    Income                     int64
    Kidhome                    int64
    Teenhome                   int64
    Dt_Customer               object
    NumWebVisitsMonth          int64
    Dt_Customer_year_c         int64
    Dt_Customer_month_c        int64
    Dt_Customer_quarter_c    float64
    Dt_Customer_day_c          int64
    Dt_Customer_weekday_c     object
    Income_category_c         object
    aging_c                   object
    dtype: object




```python
import matplotlib.pyplot as plt
campaña_clientes.describe(include='all')
```

    C:\Users\afbor\AppData\Local\Temp\ipykernel_22788\3329613580.py:2: FutureWarning: The behavior of value_counts with object-dtype is deprecated. In a future version, this will *not* perform dtype inference on the resulting index. To retain the old behavior, use `result.index = result.index.infer_objects()`
      campaña_clientes.describe(include='all')
    




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
      <th>age</th>
      <th>job</th>
      <th>marital</th>
      <th>education</th>
      <th>default</th>
      <th>housing</th>
      <th>loan</th>
      <th>contact</th>
      <th>duration</th>
      <th>campaign</th>
      <th>pdays</th>
      <th>previous</th>
      <th>poutcome</th>
      <th>emp.var.rate</th>
      <th>cons.price.idx</th>
      <th>cons.conf.idx</th>
      <th>euribor3m</th>
      <th>nr.employed</th>
      <th>y</th>
      <th>date</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>ID</th>
      <th>date_c</th>
      <th>year_c</th>
      <th>month_c</th>
      <th>quarter_c</th>
      <th>day_c</th>
      <th>weekday_c</th>
      <th>age_category_c</th>
      <th>Income</th>
      <th>Kidhome</th>
      <th>Teenhome</th>
      <th>Dt_Customer</th>
      <th>NumWebVisitsMonth</th>
      <th>Dt_Customer_year_c</th>
      <th>Dt_Customer_month_c</th>
      <th>Dt_Customer_quarter_c</th>
      <th>Dt_Customer_day_c</th>
      <th>Dt_Customer_weekday_c</th>
      <th>Income_category_c</th>
      <th>aging_c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>34726.000000</td>
      <td>39158</td>
      <td>39399</td>
      <td>37844</td>
      <td>31177.000000</td>
      <td>38532.000000</td>
      <td>38532.000000</td>
      <td>39474</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>1354.000000</td>
      <td>39474.000000</td>
      <td>39474</td>
      <td>39474.000000</td>
      <td>39053.000000</td>
      <td>39474.000000</td>
      <td>30949.000000</td>
      <td>39474</td>
      <td>39474</td>
      <td>39242</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>39474</td>
      <td>39242</td>
      <td>39242.000000</td>
      <td>39242.000000</td>
      <td>39242.000000</td>
      <td>39242.000000</td>
      <td>39242</td>
      <td>34726</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>39474</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>39474.000000</td>
      <td>39474</td>
      <td>39474</td>
      <td>39242</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>NaN</td>
      <td>11</td>
      <td>3</td>
      <td>7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>11</td>
      <td>2</td>
      <td>1825</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>39474</td>
      <td>1825</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>5</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1096</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>4</td>
      <td>2841</td>
    </tr>
    <tr>
      <th>top</th>
      <td>NaN</td>
      <td>admin.</td>
      <td>MARRIED</td>
      <td>university.degree</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>cellular</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NONEXISTENT</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5228,1</td>
      <td>no</td>
      <td>28-febrero-2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>993bbbd6-4dbc-4a40-a408-f91f8462bee6</td>
      <td>2018-02-28</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>viernes</td>
      <td>adultos jóvenes</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2012-08-10</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>lunes</td>
      <td>ingreso promedo bajo</td>
      <td>1647 days 00:00:00</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>NaN</td>
      <td>10116</td>
      <td>23863</td>
      <td>11751</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>24940</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>34156</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15719</td>
      <td>36405</td>
      <td>91</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>91</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5705</td>
      <td>15723</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>76</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5710</td>
      <td>10584</td>
      <td>36</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>39.565858</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000096</td>
      <td>0.536333</td>
      <td>0.155274</td>
      <td>NaN</td>
      <td>202.430765</td>
      <td>2.582561</td>
      <td>6.072378</td>
      <td>0.169301</td>
      <td>NaN</td>
      <td>0.102371</td>
      <td>93.577835</td>
      <td>-40.530369</td>
      <td>3.647202</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>36.859135</td>
      <td>-95.972196</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2017.000586</td>
      <td>6.505683</td>
      <td>2.669436</td>
      <td>16.031752</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>93242.552820</td>
      <td>1.004585</td>
      <td>0.998936</td>
      <td>NaN</td>
      <td>16.581953</td>
      <td>2012.852283</td>
      <td>6.534124</td>
      <td>2.676572</td>
      <td>15.749937</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>std</th>
      <td>9.666909</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.009809</td>
      <td>0.498685</td>
      <td>0.362170</td>
      <td>NaN</td>
      <td>140.342936</td>
      <td>2.823216</td>
      <td>3.889732</td>
      <td>0.488138</td>
      <td>NaN</td>
      <td>1.560397</td>
      <td>0.576369</td>
      <td>4.586215</td>
      <td>1.722377</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.231268</td>
      <td>16.746922</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.414637</td>
      <td>3.449443</td>
      <td>1.105319</td>
      <td>8.921213</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>50510.198528</td>
      <td>0.815858</td>
      <td>0.816444</td>
      <td>NaN</td>
      <td>9.245640</td>
      <td>0.876420</td>
      <td>3.443470</td>
      <td>1.097550</td>
      <td>8.769832</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>min</th>
      <td>17.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>-3.400000</td>
      <td>92.201000</td>
      <td>-50.800000</td>
      <td>0.634000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>24.396000</td>
      <td>-124.997000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2015.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5841.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>1.000000</td>
      <td>2012.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>32.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>97.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>-1.800000</td>
      <td>93.075000</td>
      <td>-42.700000</td>
      <td>1.344000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>30.602000</td>
      <td>-110.533750</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2016.000000</td>
      <td>4.000000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>49630.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>9.000000</td>
      <td>2012.000000</td>
      <td>4.000000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>38.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>166.000000</td>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>1.100000</td>
      <td>93.749000</td>
      <td>-41.800000</td>
      <td>4.857000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>36.770500</td>
      <td>-95.952000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2017.000000</td>
      <td>7.000000</td>
      <td>3.000000</td>
      <td>16.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>92932.500000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>NaN</td>
      <td>17.000000</td>
      <td>2013.000000</td>
      <td>7.000000</td>
      <td>3.000000</td>
      <td>16.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>47.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>276.000000</td>
      <td>3.000000</td>
      <td>7.000000</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>1.400000</td>
      <td>93.994000</td>
      <td>-36.400000</td>
      <td>4.961000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>43.116500</td>
      <td>-81.459000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018.000000</td>
      <td>10.000000</td>
      <td>4.000000</td>
      <td>24.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>136853.750000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>NaN</td>
      <td>25.000000</td>
      <td>2014.000000</td>
      <td>10.000000</td>
      <td>4.000000</td>
      <td>23.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>max</th>
      <td>69.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>NaN</td>
      <td>641.000000</td>
      <td>56.000000</td>
      <td>27.000000</td>
      <td>7.000000</td>
      <td>NaN</td>
      <td>1.400000</td>
      <td>94.767000</td>
      <td>-26.900000</td>
      <td>5.045000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>49.384000</td>
      <td>-66.937000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2019.000000</td>
      <td>12.000000</td>
      <td>4.000000</td>
      <td>31.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>180802.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>NaN</td>
      <td>32.000000</td>
      <td>2014.000000</td>
      <td>12.000000</td>
      <td>4.000000</td>
      <td>31.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

Relación entre variables

Se realiza en base a gráficos una análisis de variación de la variable "y" (venta) con respecto de las demás variables:
- edad ('age')
- ocupación ('job')
- estado marital
- estudios
- si ha tenido impago
- si tiene hipoteca
- si tiene préstamos
- medio de contacto
- duración del contacto
- veces que se ha contactado
- tiempo desde el último contacto
- empleados de la empresa
- ingresos
- niños y adolescentes en casa
- número de visitas al mes a la página web

Respecto a relación de esta variable con todas las anteriores, lo que se aprecia es que hay variaciones significativas con las siguientes (% con venta, % sin venta):
- ocupación: hay ligeramente más ventas en puestos administrativos (33% vs. 23%)
- estado civil: hay más ventas entre solteros (39% vs. 21%)
- estudios: hay más ventas en universitarios (43% vs. 30%)
- medio de contacto: hay más ventas por móvil (87% vs. 61%)
- número de contactos: hay más ventas al primero contacto (53% vs. 43%)
- duración del contacto: hay mucha más duración del contacto
- entre clientes que ya compraron anteriormente (25% vs. 1% )


```python
campaña_clientes.boxplot('age', by='y')

```




    <Axes: title={'center': 'age'}, xlabel='y'>




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_59_1.png)
    



```python
a=campaña_clientes.pivot_table(index='job', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_60_1.png)
    



```python
a=campaña_clientes.pivot_table(index='marital', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_61_1.png)
    



```python
a=campaña_clientes.pivot_table(index='education', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_62_1.png)
    



```python
a=campaña_clientes.pivot_table(index='default', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_63_1.png)
    



```python
a=campaña_clientes.pivot_table(index='housing', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_64_1.png)
    



```python
a=campaña_clientes.pivot_table(index='loan', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_65_1.png)
    



```python
a=campaña_clientes.pivot_table(index='contact', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_66_1.png)
    



```python
campaña_clientes.boxplot(column='duration', by='y')
```




    <Axes: title={'center': 'duration'}, xlabel='y'>




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_67_1.png)
    



```python
a=campaña_clientes.pivot_table(index='campaign', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_68_1.png)
    



```python
campaña_clientes.boxplot('pdays', by='y')
```




    <Axes: title={'center': 'pdays'}, xlabel='y'>




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_69_1.png)
    



```python
a=campaña_clientes.pivot_table(index='poutcome', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_70_1.png)
    



```python
datos=campaña_clientes['poutcome'].value_counts()
plt.pie(datos.values, labels=datos.index, autopct="%0.1f %%")
plt.title("Resultado de la campaña de marketing anterior")
plt.ylabel("% Resultado")
plt.xlabel("Resultados")
plt.plot()
```




    []




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_71_1.png)
    



```python
campaña_clientes['nr.employed']=campaña_clientes['nr.employed'].apply(lambda x: None if x==None else str(x).replace(',','.'))

campaña_clientes['nr.employed']=campaña_clientes['nr.employed'].apply(lambda x: None if x==None else float(x))
```


```python
campaña_clientes.boxplot(column='nr.employed', by='y')
```




    <Axes: title={'center': 'nr.employed'}, xlabel='y'>




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_73_1.png)
    



```python
campaña_clientes.boxplot(column='Income', by='y')
```




    <Axes: title={'center': 'Income'}, xlabel='y'>




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_74_1.png)
    



```python
a=campaña_clientes.pivot_table(index='Kidhome', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_75_1.png)
    



```python
a=campaña_clientes.pivot_table(index='Teenhome', columns=['y'], aggfunc='size')
a=a.rename(columns={'no':'Distribución Clientes SIN venta', 'yes':'Distribución Clientes CON venta'})
a.plot.pie(subplots=True, title=list(a.columns), ylabel='', legend=False, autopct='%.2f%%', figsize=(12, 5))
```




    array([<Axes: title={'center': 'Distribución Clientes SIN venta'}>,
           <Axes: title={'center': 'Distribución Clientes CON venta'}>],
          dtype=object)




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_76_1.png)
    



```python
campaña_clientes['NumWebVisitsMonth']=campaña_clientes['NumWebVisitsMonth'].apply(lambda x: None if x==None else str(x).replace(',','.'))

campaña_clientes['NumWebVisitsMonth']=campaña_clientes['NumWebVisitsMonth'].apply(lambda x: None if x==None else float(x))
```


```python
campaña_clientes.boxplot(column='NumWebVisitsMonth', by='y')
```




    <Axes: title={'center': 'NumWebVisitsMonth'}, xlabel='y'>




    
![png](Proyecto_EDA_Con_Python_files/Proyecto_EDA_Con_Python_78_1.png)
    



```python
campaña_clientes['aging_c'][1].days
```




    1354


