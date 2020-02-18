---
layout: post
current: post
cover:  assets/blogposts/013.png
navigation: True
title: Visualizando temblores con ridgeline plots
date: 2020-02-18 10:00:00
tags: [blog, sismos, ridgeline plots, matplotlib, python]
class: post-template
subclass: 'post tag-blog'
author: io_exception
---
Seguramente has visto todas esas gráficas parecieran sacadas de portadas de álbums... pues bien, en este post te voy a enseñar cómo crearla usando matplotlib, python y los datos del sistema sismológico nacional mexicano.

Lo primero que vamos a hacer es descargar la información de sobre los temblores de la [página del Sistema Sismológico Nacional](http://www2.ssn.unam.mx:8080/catalogo/), en este caso yo elegí los datos del 1 de enero de 2017 al 1 de enero de 2018, pero tu puedes elegir otros. Después vamos a descargar un archivo con las siluetas de los estados de México, por ejemplo el [sitio de Carlos Efraín Porto Tapiquén](https://tapiquen-sig.jimdofree.com/descargas-gratuitas/m%C3%A9xico/). 

Una vez hecho esto, vamos a instalar unos cuantos paquetes que nos van a ayudar con la visualización:  
 - matplotlib 
 - numpy
 - pandas  
 - geopandas

```python
import matplotlib.pyplot as plt
import geopandas as gpd
import datetime as dt
import pandas as pd
import numpy as np
```

Leemos los datos de los temblores, tenemos que saltarnos las primeras cuatro filas porque el Sismológico Nacional decidió que tener un archivo CSV normal no valía la pena.

```python
earthquakes = pd.read_csv("SSNMX_catalogo_20170101_20180101.csv", skiprows=4)
earthquakes.head()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Fecha</th>
      <th>Hora</th>
      <th>Magnitud</th>
      <th>Latitud</th>
      <th>Longitud</th>
      <th>Profundidad</th>
      <th>Referencia de localizacion</th>
      <th>Fecha UTC</th>
      <th>Hora UTC</th>
      <th>Estatus</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2017-01-01</td>
      <td>00:15:59</td>
      <td>3.5</td>
      <td>15.2218</td>
      <td>-93.1245</td>
      <td>81.5</td>
      <td>35 km al SUROESTE de MAPASTEPEC, CHIS</td>
      <td>2017-01-01</td>
      <td>06:15:59</td>
      <td>revisado</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2017-01-01</td>
      <td>00:16:06</td>
      <td>3.2</td>
      <td>16.9610</td>
      <td>-99.5348</td>
      <td>36.7</td>
      <td>24 km al NOROESTE de SAN MARCOS, GRO</td>
      <td>2017-01-01</td>
      <td>06:16:06</td>
      <td>revisado</td>
    </tr>
  </tbody>
</table>

Leemos también los datos del archivo `.shp`, que son los datos que podemos usar para dibujar el mapa de México:

```python
map_df = gpd.read_file("México_Estados.shp")
map_df.head()
```  

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CODIGO</th>
      <th>ESTADO</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MX02</td>
      <td>Baja California</td>
      <td>MULTIPOLYGON (((-113.13972 29.01778, -113.51195 29.30305, -113.60028 29.43916, -113.57390 29.50583, -113.58862 29.58361, -113.40529 29.482 (...)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>MX03</td>
      <td>Baja California Sur</td>
      <td>MULTIPOLYGON (((-111.20612 25.80278, -111.23029 25.83416, -111.19139 26.03889,  (...)</td>
    </tr>
  </tbody>
</table>

## Histograma 2D  

La columna vertebral de nuestra visualización es un histograma en dos dimensiones, muy parecido al que realicé hace unos días con mis datos de Spotify. En este caso vamos a hacer uso de NumPy para generar dicho histograma, para nosotros el eje de las *x* será la *longitud* y el de las *y* es la *latitud*:

```python
left, right, top, bottom = -105.68, -92.04, 20.14, 13.79
coordinates_range = [[left, right], [bottom, top]]
values, xedges, yedges = np.histogram2d(x=earthquakes["Longitud"].values, 
                                        y=earthquakes["Latitud"].values,
                                        range=coordinates_range,
                                        bins=[600, 100])
```

Los parámetros que le pasamos a la función `histogram2d` son:  
 - `x`: las coordenadas en el eje X a tomar en cuenta
 - `y`: las coordenadas en el eje Y a tomar en cuenta
 - `range`: los límites que debe cubrir nuestro histograma, en este caso, como estamos tratando con las coordenadas del sur de México, -105.68 a la izquierda, -92.04 a la derecha, 20.14 arriba, 13.79 abajo.
 - `bins`: el número de *"cubetas"* a crear, en nuestro caso, se crearán 600 cubetas horizontalmente y 100 verticales

El resultado del método es una tripleta de valores: 
 - `values`: un arreglo bidimensional con los valores de la cubeta, con nuestros valores anteriores las dimensiones de la cubeta son (600, 100)
 - `xedges`: los límites de las cubetas en el eje de las X, un arreglo de 601 entradas
 - `yedges`: los límites de las cubetas en el eje de las y, un arreglo de 101 entradas 

Si te estás preguntando por qué tanto `xedges` como `yedges` tienen tan peculiar número de entradas es por que los valores de la cubeta incluyen ambos bordes (derecha-izquierda, arriba-abajo), en el caso de `xedges` tenemos algo como esto:

![visualizando xedges](https://i.imgur.com/Gk1iWLf.png)

Mientras que tener acceso a los bordes es útil para la mayoría de los casos, para nuestro caso no lo es tanto, lo que vamos a hacer es promediar los bordes de las cubetas para obtener un par único para cada una de ellas, dejándonos un arreglo de 600 entradas para el eje `x` y 100 para `y`:

![visualizando xavg](https://i.imgur.com/NVF9ixW.png)

```python
xavg = (xedges[1:] + xedges[:-1]) / 2
yavg = (yedges[1:] + yedges[:-1]) / 2
```

Ya que casi tenemos todo, toca hacer un pequeño truco para escalar los valores, y es que si usamos los valores `values` como están, nuestra gráfica terminaría saturada:

```python
def scale_values(values):
    return values / values.max()

scaled = scale_values(values)
```

Ahora sí, a graficar:  

Creamos una nueva figura, de dimensiones considerables:

```python
fig = plt.figure(figsize=(20,10))
ax = fig.gca()
```

Graficamos el las siluetas de los estados mexicanos usando el dataframe obtenido de leer los datos con *geopandas*:

```python
map_df.plot(color='#eeeeee', edgecolor='#B3B3B3', ax=ax)
```

El siguiente paso es graficar cada una de las líneas horizontales que representan los valores percibidos (contenidos dentro de `scaled`), si te das cuenta, estamos sumando `yavg[i]` a cada iteración, esto es para mover nuestras gráficas a lo largo del eje vertical.

```python                                                                         
for i in range(len(yavg)):
    plt.plot(xavg, scaled[:,i] + yavg[i], c="black", linewidth=1)
```

Como solamente estamos interesados en una porción de los datos, debemos establecer los límites de la información que queremos mostrar:

```python
ax.set_xlim(coordinates_range[0])
ax.set_ylim(coordinates_range[1])
```

Y con eso bastaría para poder ver resultados de nuestra gráfica, sin embargo, podemos mejorarla un poco más removiendo las marcas de los ejes, poniéndole un título y por último añadiéndole la información del autor y la fuente de los datos:

```python
plt.title("Earthquakes in Mexico (2017/01/01 - 2018/01/01)", size=30)

text = AnchoredText("@io_exception - Data: SSN México", loc=3,
                    prop={'size': 10}, frameon=True)
ax.add_artist(text)
```

![visualizando xavg](https://i.imgur.com/MHLosn4.png)

Ya quedará en ti modificar algunos aspectos de la gráfica, tal vez ponerle un poco más de color, mejorar la escala de los datos, incluyendo más factores, o hacer una gráfica similar pero con otros datos. Por lo mientras, te invito a jugar con los datos en [este Google Colab Notebook](https://colab.research.google.com/drive/1fwVd2QMEMXgrJD-XNDv5EGayjZYAX7IB) y a que me contactes [vía twitter en @io_exception](https://twitter.com/io_exception).
