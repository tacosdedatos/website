---
layout: post
current: post
cover:  assets/tutoriales/eda_01.png
navigation: True
title: Introducción al análisis exploratorio de datos
date: 2020-05-24 10:00:00
tags: [tutoriales, python, eda]
class: post-template
subclass: 'post tag-tutoriales'
author: io_exception
---

En este post les voy a dar una pequeñísima introducción al análisis exploratorio de datos o EDA por sus siglas en inglés (Exploratory Data Analysis). 

Como lo mencioné en mi [video de YouTube sobre el tema](https://youtu.be/s7A1OxEeT6o), El AED una forma de realizar análisis de datos, es usualmente uno de los primeros pasos que uno debe realizar antes de aventurarse utilizar el scikit learn o tensoflow para hacer machine learning. Usualmente este proceso de análisis involucra muchas ayudas visuales, es decir gráficas, que nos ayudan a encontrar información que de otra forma sería difícil de conocer acerca de nuestros datos; mediante este proceso estamos tratando de encontrar:

- La estructura y distribución de nuestros datos,
- Encontrar las relaciones entre las variables explicatorias,
- Encontrar la relación que tienen las variables explicatorias con la variable respuesta,
- Encontrar posibles errores, puntos extremos y anomalías en los datos,
- Refinar nuestras hipótesis, o generar nuevas preguntas sobre los datos que tenemos

No existe una técnica formal sobre cómo llevar a cabo este tipo de análisis, sino que más dependen de lo que vayamos encontrando en los datos, así como de la experiencia y conocimiento específico del problema con el que contemos.

Para este post usaré algunas de las librerías más comunes para el análisis de datos en el ecosistema de Python, todas las puedes instalar desde PyPI:

```shell
pip install numpy pandas matplotlib seaborn scikit-learn
```  

Y sobre los datos que vamos a analizar, serán los datos de [Red Wine Quality](https://www.kaggle.com/uciml/red-wine-quality-cortez-et-al-2009). Por cierto, [puedes ver el notebook completo](https://www.kaggle.com/ioexception/vino-eda) en Kaggle, si accedes al notebook ahí, no tienes que preocuparte por instalar nada ni descargar nada, todo está listo. Ahora si, comenzamos con nuestros imports de regla:

```python
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import matplotlib.style as style
from contextlib import contextmanager
from sklearn.model_selection import train_test_split

style.use('ggplot')
```

Ahora sí, leemos nuestros a un `DataFrame` datos usando *pandas*, una vez leídos mostramos qué tan grande es nuestro `DataFrame`, así como una muestra de los datos:

```python
wine_quality = pd.read_csv("/kaggle/input/red-wine-quality-cortez-et-al-2009/winequality-red.csv")
print("Dataset length", len(wine_quality))
wine_quality.head()
```

 > Dataset length 1599

 <table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fixed acidity</th>
      <th>volatile acidity</th>
      <th>citric acid</th>
      <th>residual sugar</th>
      <th>chlorides</th>
      <th>free sulfur dioxide</th>
      <th>total sulfur dioxide</th>
      <th>density</th>
      <th>pH</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7.4</td>
      <td>0.70</td>
      <td>0.00</td>
      <td>1.9</td>
      <td>0.076</td>
      <td>11.0</td>
      <td>34.0</td>
      <td>0.9978</td>
      <td>3.51</td>
      <td>0.56</td>
      <td>9.4</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7.8</td>
      <td>0.88</td>
      <td>0.00</td>
      <td>2.6</td>
      <td>0.098</td>
      <td>25.0</td>
      <td>67.0</td>
      <td>0.9968</td>
      <td>3.20</td>
      <td>0.68</td>
      <td>9.8</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7.8</td>
      <td>0.76</td>
      <td>0.04</td>
      <td>2.3</td>
      <td>0.092</td>
      <td>15.0</td>
      <td>54.0</td>
      <td>0.9970</td>
      <td>3.26</td>
      <td>0.65</td>
      <td>9.8</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11.2</td>
      <td>0.28</td>
      <td>0.56</td>
      <td>1.9</td>
      <td>0.075</td>
      <td>17.0</td>
      <td>60.0</td>
      <td>0.9980</td>
      <td>3.16</td>
      <td>0.58</td>
      <td>9.8</td>
      <td>6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7.4</td>
      <td>0.70</td>
      <td>0.00</td>
      <td>1.9</td>
      <td>0.076</td>
      <td>11.0</td>
      <td>34.0</td>
      <td>0.9978</td>
      <td>3.51</td>
      <td>0.56</td>
      <td>9.4</td>
      <td>5</td>
    </tr>
  </tbody>
</table>

De a poco vamos conociendo nuestros datos... ahora la siguiente pregunta **¿Qué variables tenemos?**.  


Lo primero que hay que hacer es tratar de identificar el tipo de variables que tenemos a la mano, en algunos *datasets* esto es posible con tan solo leer los nombres de las columnas (como es el caso de el *dataset* con el que estamos trabajando). Sin embargo, hay algunos casos en los que los nombres no son provistos (o están ofuscados) por diversas razones.

Aprovechando que nuestro *dataset* sí tiene nombres, podemos verlos con:

```python
wine_quality.columns
```

 > Index(['fixed acidity', 'volatile acidity', 'citric acid', 'residual sugar',
 >   'chlorides', 'free sulfur dioxide', 'total sulfur dioxide', 'density',
 >   'pH', 'sulphates', 'alcohol', 'quality'],
 >   dtype='object')

Probablemente si no eres un conocedor de vinos, las variables tengan poco o nada de sentido para ti. **Idealmente nosotros deberíamos tener conocimiento del tema sobre el que vamos a trabajar (o podemos conseguir un experto que nos guíe)**... pero por ahora pretendamos que sabemos lo que hacemos. Visita este link si quieres <a href="https://waterhouse.ucdavis.edu/whats-in-wine" target="_blank">una ligera introducción a los componentes del vino</a>.

## Antes de continuar ⚠️  

 > Si lo que estás esperando hacer con la información es crear un modelo predictivo, lo primero que hay que hacer es separar los datos en conjuntos de prueba, entrenamiento y, si puedes, validación. El análisis exploratorio de datos se debe conducir únicamente sobre los datos de entrenamiento, ya que realizar el análisis en todo el conjunto de datos nos llevaría a tomar decisiones teniendo en cuenta datos a los que, en teoría, tu modelo no tendría acceso en producción. Es decir, este es un problema de filtración de datos.
 
Teniendo esto en mente, vamos a separar nuestros datos con:

```python
wine_train, wine_test = train_test_split(wine_quality)
```

## Completitud en los datos  
Antes de comenzar cualquier análisis, es bueno revisar los datos para buscar información faltante; y en caso de que la haya, es nuestra tarea decidir qué es lo que podemos hacer con esos registros faltantes. Con los dataframes de `pandas` podemos usar `info` para encontrar los datos faltantes: También podríamos haber usado [missingno](https://github.com/ResidentMario/missingno) para tener una representación visual de esta información.

```python
wine_train.info()
wine_test.info()
```

Y así es cómo se ve a la salida:

```text  
<class 'pandas.core.frame.DataFrame'>
Int64Index: 1199 entries, 1060 to 418
Data columns (total 12 columns):
fixed acidity           1199 non-null float64
volatile acidity        1199 non-null float64
citric acid             1199 non-null float64
residual sugar          1199 non-null float64
chlorides               1199 non-null float64
free sulfur dioxide     1199 non-null float64
total sulfur dioxide    1199 non-null float64
density                 1199 non-null float64
pH                      1199 non-null float64
sulphates               1199 non-null float64
alcohol                 1199 non-null float64
quality                 1199 non-null int64
dtypes: float64(11), int64(1)
memory usage: 121.8 KB
<class 'pandas.core.frame.DataFrame'>
Int64Index: 400 entries, 1186 to 1163
Data columns (total 12 columns):
fixed acidity           400 non-null float64
volatile acidity        400 non-null float64
citric acid             400 non-null float64
residual sugar          400 non-null float64
chlorides               400 non-null float64
free sulfur dioxide     400 non-null float64
total sulfur dioxide    400 non-null float64
density                 400 non-null float64
pH                      400 non-null float64
sulphates               400 non-null float64
alcohol                 400 non-null float64
quality                 400 non-null int64
dtypes: float64(11), int64(1)
memory usage: 40.6 KB
```

Y pues no, no hay datos faltantes... sin embargo si faltaran, debes saber que existe toda una metodología para decidir cómo actuar ante datos faltantes en nuestro *dataset*. Pero de eso podemos hablar en otro momento.

## Estadísticas descriptivas  

El segundo paso a dar, es ver las estadísticas descriptivas de nuestra información, esto nos ayudará a darnos una idea de los posibles valores de nuestro dataset. El paquete `pandas` ofrece el método `describe` para obtener una vista detallada y completa de algunas de las estadísticas más comunes:

```python
wine_train.describe()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fixed acidity</th>
      <th>volatile acidity</th>
      <th>citric acid</th>
      <th>residual sugar</th>
      <th>chlorides</th>
      <th>free sulfur dioxide</th>
      <th>total sulfur dioxide</th>
      <th>density</th>
      <th>pH</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
      <td>1199.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>8.309174</td>
      <td>0.525847</td>
      <td>0.269867</td>
      <td>2.574604</td>
      <td>0.088136</td>
      <td>15.820267</td>
      <td>46.194329</td>
      <td>0.996755</td>
      <td>3.312035</td>
      <td>0.664120</td>
      <td>10.436614</td>
      <td>5.637198</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.758086</td>
      <td>0.177889</td>
      <td>0.194103</td>
      <td>1.448308</td>
      <td>0.049179</td>
      <td>10.559120</td>
      <td>32.648521</td>
      <td>0.001879</td>
      <td>0.156249</td>
      <td>0.177114</td>
      <td>1.075123</td>
      <td>0.802324</td>
    </tr>
    <tr>
      <th>min</th>
      <td>4.600000</td>
      <td>0.120000</td>
      <td>0.000000</td>
      <td>0.900000</td>
      <td>0.012000</td>
      <td>1.000000</td>
      <td>6.000000</td>
      <td>0.990070</td>
      <td>2.740000</td>
      <td>0.330000</td>
      <td>8.400000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>7.100000</td>
      <td>0.390000</td>
      <td>0.090000</td>
      <td>1.900000</td>
      <td>0.070000</td>
      <td>7.000000</td>
      <td>22.000000</td>
      <td>0.995600</td>
      <td>3.210000</td>
      <td>0.560000</td>
      <td>9.500000</td>
      <td>5.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>7.900000</td>
      <td>0.520000</td>
      <td>0.260000</td>
      <td>2.200000</td>
      <td>0.079000</td>
      <td>13.000000</td>
      <td>37.000000</td>
      <td>0.996800</td>
      <td>3.310000</td>
      <td>0.620000</td>
      <td>10.200000</td>
      <td>6.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>9.200000</td>
      <td>0.637500</td>
      <td>0.420000</td>
      <td>2.600000</td>
      <td>0.091000</td>
      <td>22.000000</td>
      <td>61.500000</td>
      <td>0.997820</td>
      <td>3.400000</td>
      <td>0.735000</td>
      <td>11.100000</td>
      <td>6.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>15.900000</td>
      <td>1.330000</td>
      <td>1.000000</td>
      <td>15.500000</td>
      <td>0.611000</td>
      <td>72.000000</td>
      <td>278.000000</td>
      <td>1.003690</td>
      <td>4.010000</td>
      <td>2.000000</td>
      <td>14.900000</td>
      <td>8.000000</td>
    </tr>
  </tbody>
</table>

De este resumen estadístico, una de las primeras cosas que podrían resultar extrañas es que la variable **quality** únicamente toma valores entre $3$ y $8$ (a pesar de que en la descripción original dice que los valores van de $0$ a $10$. Lo cual representa un problema puesto que nuestro modelo predictivo no tendrá ejemplos de vinos con una calidad de $0$ o de $10$, por ejemplo. Pero por el momento, no nos vamos a preocupar mucho por eso.

También tengo que decir que algun conocedor del tema podría tener opiniones acerca de los rangos de valores que cubren ciertas variables... pero nosotros vamos a pasar al análisis gráfico.

```python
@contextmanager
def plot(title=None, xlabel=None, ylabel=None, figsize=(9,5)):
    fig = plt.figure(figsize=figsize)
    ax = fig.gca()
    yield ax
    if title:
        ax.set_title(title)
    ax.set_xlabel(xlabel, size=15)
    ax.set_ylabel(ylabel, size=15)
```

### Histogramas  
Primero podemos echarle un ojo a la distribución de la variable `quality`, que como ya sabemos que es una variable discreta y que los valores van, en teoría de 0 a 10, podemos simplemente usar `countplot` del módulo *seaborn*:

```python
with plot(title="Counts of `qualuty`", xlabel="Quality", ylabel="Count") as ax:
    sns.countplot(x="quality", palette=("Accent"), data=wine_train, ax=ax)
``` 

<img src="https://i.imgur.com/FFFtlJm.png" />

Como puedes ver, nuestro conjunto de datos está desbalanceado, con muchos mas $5$ y $6$ que cualquier otro valor.

Ahora lo que podemos hacer es revisar algunas otras distribuciones, ahora sí, usando histogramas:

```python
with plot(title="Fixed Acidity distribution", xlabel="Acidity") as ax:
    sns.distplot(wine_train["volatile acidity"], ax=ax)
```

<img src="https://i.imgur.com/QnXb6u0.png" />

Podemos usar un poco de código para visualizar más de una variable a la vez:

```python
variables = ['fixed acidity', 'volatile acidity', 'citric acid', 'residual sugar',
       'chlorides', 'free sulfur dioxide', 'total sulfur dioxide', 'density',
       'pH', 'sulphates', 'alcohol', 'quality']

columns = 4

fig, axes = plt.subplots(len(variables) //columns, columns, figsize=(15,8))

for current_idx, variable in enumerate(variables):
    i = current_idx // columns
    j = current_idx % columns
    sns.distplot(wine_train[variable], ax=axes[i][j])
    axes[i][j].set_title(variable)
    axes[i][j].set_xlabel("")
    
plt.tight_layout()
```

<img src="https://i.imgur.com/ZoAJMQa.png" />

De esta gráfica podemos ver que muchas de las variables tienen una distribución asimétrica (`fixed acidity`, `residual sugar`, `chlorides`, por ejemplo), además de que al parecer algunos valores tienen valores extremos (`residual sugar`, `sulphates`, `total sulfur dioxide`). Tal vez merezcan más exploración...

### Boxplots  
Como mencioné anteriormente, existen algunas variables que merecen un poco más de exploración ya que parecen tener valores extremos, las *boxplots* nos permiten encontrar precisamente estos valores extremos. Es fácil graficar *boxplots* con *seaborn*.

```python
variables = ['fixed acidity', 'residual sugar',
       'chlorides', 'free sulfur dioxide', 
             'total sulfur dioxide','sulphates', 'alcohol']

fig, axes = plt.subplots(1, len(variables), figsize=(15,6))

for ax, variable in zip(axes, variables):
    ax = sns.boxplot( y=variable, data=wine_train, ax=ax)
plt.tight_layout()
```

<img src="https://i.imgur.com/zAGmjNX.png" />

Como ya sabemos, los puntos fuera de las líneas horizontales son los famosos *outliers* o "valores atípicos", dependiendo de la aplicación podemos reaccionar de diversas maneras frente a ellos... a veces los *outliers* se eliminan, a veces se transforman, o a veces se dejan porque tienen alto valor predictivo.

### Scatterplots  
El siguiente paso es tratar de identificar relaciones entre variables, podríamos por ejemplo usar un *scatterplot* para ver qué tipo de relación existe entre la cantidad de alcohol y la calidad de un vino:

```python
with plot(title="Alcohol - Quality scatterplot", xlabel="Alcohol", ylabel="Quality") as ax:
    sns.scatterplot(x="alcohol", y="quality", data=wine_train, ax=ax) 
```

<img src="https://i.imgur.com/GRULpvb.png" />

Tal vez esta gráfica no sea tan reveladora, ya que nuestra variable `quality` es más bien del tipo categórico y es difícil identificar una tendencia. Otra cosa a notar es que las correlaciones también se pueden y, en la mayoría de los casos, se deben identificar entre las variables independientes también, no solo entre una de ellas y la variable dependiente. Por ejemplo, entre `free sulfur dioxide` y `total sulfur dioxide`: 

```python
with plot(title="Free Sulfur Dioxide - Total Sulfur Dioxide scatterplot", xlabel="Free Sulfur Dioxide", ylabel="Total Sulfur Dioxide") as ax:
    sns.scatterplot(x="free sulfur dioxide", y="total sulfur dioxide", data=wine_train, ax=ax) 
``` 

<img src="https://i.imgur.com/HfdtXwC.png" />

En esta gráfica si se puede observar una clara relación entre las variables, ¿cierto?

Si quieres ver solamente el grado de correlación (sin necesidad de tratar de encontrarlo tu mismo desde una *scatterplot*) también podemos hacer uso de las matrices de correlación.

### Matrices de correlación  
Una matriz de correlación no es más que una matriz de números (cada número va de -1 a 1) que nos indican qué tan relacionadas están una variable con otra. Existen [3 métodos](https://datascience.stackexchange.com/a/64261) para calcular esta correlación. Para calcularla en nuestro dataframe de vinos, podemos simplemente usar el método `corr` de un *dataframe*:  

```python
correlation = wine_train.corr(method="pearson")
correlation.head()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fixed acidity</th>
      <th>volatile acidity</th>
      <th>citric acid</th>
      <th>residual sugar</th>
      <th>chlorides</th>
      <th>free sulfur dioxide</th>
      <th>total sulfur dioxide</th>
      <th>density</th>
      <th>pH</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>fixed acidity</th>
      <td>1.000000</td>
      <td>-0.237044</td>
      <td>0.675747</td>
      <td>0.164493</td>
      <td>0.091213</td>
      <td>-0.167011</td>
      <td>-0.102002</td>
      <td>0.681653</td>
      <td>-0.692635</td>
      <td>0.152724</td>
      <td>-0.075869</td>
      <td>0.128048</td>
    </tr>
    <tr>
      <th>volatile acidity</th>
      <td>-0.237044</td>
      <td>1.000000</td>
      <td>-0.543960</td>
      <td>-0.011115</td>
      <td>0.063904</td>
      <td>-0.015815</td>
      <td>0.088772</td>
      <td>0.031415</td>
      <td>0.220483</td>
      <td>-0.248910</td>
      <td>-0.209193</td>
      <td>-0.389654</td>
    </tr>
    <tr>
      <th>citric acid</th>
      <td>0.675747</td>
      <td>-0.543960</td>
      <td>1.000000</td>
      <td>0.149428</td>
      <td>0.225910</td>
      <td>-0.078811</td>
      <td>0.004797</td>
      <td>0.376603</td>
      <td>-0.545874</td>
      <td>0.314076</td>
      <td>0.109625</td>
      <td>0.241375</td>
    </tr>
    <tr>
      <th>residual sugar</th>
      <td>0.164493</td>
      <td>-0.011115</td>
      <td>0.149428</td>
      <td>1.000000</td>
      <td>0.059603</td>
      <td>0.170785</td>
      <td>0.162121</td>
      <td>0.390897</td>
      <td>-0.114692</td>
      <td>0.021992</td>
      <td>0.037932</td>
      <td>0.026566</td>
    </tr>
    <tr>
      <th>chlorides</th>
      <td>0.091213</td>
      <td>0.063904</td>
      <td>0.225910</td>
      <td>0.059603</td>
      <td>1.000000</td>
      <td>0.003828</td>
      <td>0.061718</td>
      <td>0.192486</td>
      <td>-0.284091</td>
      <td>0.391324</td>
      <td>-0.222979</td>
      <td>-0.133884</td>
    </tr>
  </tbody>
</table>

Luego, para graficar estos números podemos usar un `heatmap` de *seaborn*:

```python
with plot(title="Free Sulfur Dioxide - Total Sulfur Dioxide scatterplot") as ax:
    sns.heatmap(correlation, vmin=-1,cmap= 'coolwarm', annot=True, ax=ax) 
``` 

<img src="https://i.imgur.com/oKpJmEe.png" />

Los colores más intensos corresponden con números cercanos a -1 o 1, que indican que tienen mayor relación entre ellas, por ejemplo, la variable `pH` con `fixed acidity` (que tienen una relación inversa) o `density` con `fixed acidity` que de igual manera, presenta una relación, pero en este caso, directa.

Y pues, eso es todo por este post, sí, es bastante sencillo y aún hay mucho qué discutir sobre el análisis exploratorio de datos, aquí hay algunos de los recursos que yo consulté para preparar este video, y aún así hay mucho más por explorar:  

 - <a href="https://www.itl.nist.gov/div898/handbook/eda/section1/eda1.htm" target="_blank">EDA Introduction</a>
 - <a href="https://towardsdatascience.com/a-gentle-introduction-to-exploratory-data-analysis-f11d843b8184" target="_blank">A Gentle Introduction to Exploratory Data Analysis</a>
 - <a href="https://r4ds.had.co.nz/exploratory-data-analysis.html" target="_blank">Exploratory Data Analysis</a>

No olvides seguirme en twitter en [@io_exception](https://twitter.com/io_exception).
