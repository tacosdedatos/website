---
layout: post
current: post
cover:  assets/blogposts/015.png
navigation: True
title: Creando animaciones con Python y matplotlib
date: 2020-04-05 10:00:00
tags: [blog, python, matplotlib]
class: post-template
subclass: 'post tag-blog'
author: ioexception
---

## Creando animaciones  
A veces, la forma en la que presentamos los datos podría ayudarnos aún más a contar la historia que representan. A veces esta forma puede tomar forma de una animación, haciendo que la presentación de los datos sea más dinámica y más entretenida.

En este post te mostraré cómo es que puedes animar tus gráficas hechas con *matplotlib* en Python; además usaré *seaborn* porque... pues por que es bonito usarlo para graficar datos.

<small>Importamos algunos paquetes:</small>  

```python
import numpy as np # pip install numpy
import pandas as pd # pip install pandas
import matplotlib.pyplot as plt # pip install matplotlib
import seaborn as sns # pip install seaborn
```

### Comenzando por... ¿el final?  
Una de las claves para realizar la animación es comenzar por el final, sí, el final es la gráfica final que queremos mostrar; en mi caso, estaré graficando una simple función de distribución (puedes verla acá debajo), pero tu información puede ser cualquier cosa, con suerte, algo más útil.  

Los datos que vamos a graficar:  

```python
# Código para generar una función de distribución
normal = np.random.normal(loc=5, scale=100, size=10_000)
y, x = np.histogram(normal, bins=100)
x = (x[1:] + x[:-1]) / 2
y = np.cumsum(y)
```  

El resultado final de la gráfica:  

```python
fig = plt.figure(figsize=(10,7))
ax = fig.gca()
sns.lineplot(x, y, ax = ax)
ax.set_title("Cumulative Distribution")
```

![](https://i.imgur.com/CFTusWs.png)

El siguiente paso es definir cuál va a ser el inicio de nuestra animación. En mi caso, yo quiero que la gráfica comience con un solo dato, del lado izquierdo de la gráfica. Y cada cuadro de la animación iré agregando un nuevo punto. Por ejemplo, este será el punto inicial:

```python
sns.lineplot(x[:1], y[:1])
``` 

![](https://i.imgur.com/gFKAzse.png)

¡Nada! ¿uh? pues la cosa es que en la gráfica tiene un solo punto y como es una *lineplot* que debe tener más de un punto para que haya una línea entre ellos. Vamos a adelantarnos en el tiempo y graficar varios *frames* en el tiempo, esta vez en la misma figura usando *subplots*:

```python
timesteps = 16
columns = 4
fig, axes = plt.subplots(timesteps // columns, columns, figsize=(10,10))
for i in range(2, timesteps + 2):
    ax = axes[(i - 2) // columns][(i - 2) % columns]
    sns.lineplot(x[:i], y[:i], ax = ax)
    ax.set_title(f"Frame {i}")
```

![](https://i.imgur.com/UpQAUg7.png)

La idea clave es que en la animación, cada *frame* debe ser una gráfica "nueva", cuando presentamos una gráfica después de otra dará la ilusión de que la gráfica se está moviendo (sí, de la misma manera en la que imágenes estáticas presentadas rápidamente crean un video).

Como se ve en la gráfica de arriba, en este caso es más que suficiente mover el *slice* en incrementos para nuestras variables `x` e `y `, excepto que para la animación no queremos graficar una a un lado de la otra, sino **en lugar de la otra**. Para realizar esto, vamos a hacer uso de la clase `FuncAnimation` dentro del módulo `matplotlib.animation`:

```python
from matplotlib.animation import FuncAnimation
```

Para usarla, hay que crear una función cuya responsabilidad es la de crear cada gráfica a cada *frame*, generando la animación. Esta función recibe el número del grame que se debe graficar; comenzando desde 0, (aunque adicionalmente nosotros le mandamos otro parámetro llamado `ax` que es el *axes* en donde vamos a graficar):

```python
def create_frame(step, ax):
    ax.cla()
    sns.lineplot(x[:step], y[:step], ax = ax)
```

De la función anterior, solamente la primera línea debería ser "nueva" para nosotros, la usamos para limpiar los *axes*, si no realizamos este paso, cuando graficamos datos consecutivamente estaríamos graficando los datos del paso **`n`** sobre los datos para el paso **`n - 1`**. La línea `sns.lineplot(x[:step], y[:step], ax = ax)` es simplemente para graficar la función.  

```python
fig = plt.figure()
ax = fig.gca()
create_frame(10, ax)
```

Y finalmente, por fin podemos usar `FuncAnimation`, pasando como parámetros la `Figure` en la que vamos a dibujar, la función que estamos usando para dibujar, el número de *frames* (100 en este caso) y el *axes* en el que vamos a dibujar, con el argumento `fargs`:

```python
fig = plt.figure()
ax = fig.gca()
animation = FuncAnimation(fig, create_frame, frames=100, fargs=(ax,))
```

Ya casi terminamos; solamente tenemos que generar nuestra animación, podemos convertirla en una animación en video, gif (usando el método `save`) o algo que podemos abrir con html (usando `to_jshtml`):

```python
from IPython.display import HTML
# .gif necesita la librería imagemagick que puede ser instalada con !apt install imagemagick
# animation.save('poly.gif', writer='imagemagick', fps=20); 
animation.save('animation.mp4', writer='ffmpeg', fps=20);
HTML(animation.to_jshtml())
```

<iframe style="border:none;" src="https://mystifying-meitner-c75bce.netlify.com/animation.html" width="100%" height="380"></iframe>
<small>Sí, puedes interactuar con la gráfica de arriba.</small>

Y... ¡eso es todo! Solo para recordar el concepto principal: **Piensa en tus animaciones como una serie de *frames*, y luego piensa en cada *frame* como una gráfica independiente**.


### Bonus  
Con un poco de trabajo extra se pueden hacer cosas un tanto más complejas, como la animación que muestro a continuación (son distribuciones de probabilidad):

![](https://i.imgur.com/8ON2Bq0.gif)

No me voy a detener a hablar tanto del código, pero lo dejo ahí para que juegues con él. Me puedes contactar en los comentarios, o en [@io_exception en Twitter](https://twitter.com/io_exception) si tienes dudas sobre él.

Nuevamente, el principio más importante es pensar en la forma final de nuestra animación, solo que en este caso tenemos cuatro posibles finales, que a su vez son 4 posibles principios...

Comencemos por crear las distribuciones, también hay que graficarlas para ver si logramos crearlas correctamente:

```python
from scipy.stats import skewnorm
from itertools import cycle

# Create some distributions, with 1000 samples each one
size = 1000
distributions = [
                 np.random.uniform(0, 10, size),
                 np.random.normal(5, 1, size),
                 skewnorm.rvs(-5, 9, size=size),
                 skewnorm.rvs(5, 1, size=size),
]

bins = 20
limits = (0,10)
x_axis = np.linspace(*limits, bins+1)
x_axis = (x_axis[1:] + x_axis[:-1]) / 2
functions = [
    np.histogram(distribution, bins=bins, range=limits, density=True)[0] 
    for distribution in distributions
]

# Plot the distributions just to confirm we did the right thing
for distribution in distributions:
    sns.distplot(distribution)
```

![](https://i.imgur.com/c8T1Bap.gif)

En este caso, cree las animaciones yo mismo, guardando los valores para cada punto de la gráfica en un arreglo llamado `frame_contents` usando `cycle` para iterar sobre las distribuciones.

Un truco bastante útil para animar la transición entre dos gráficas de manera "suave" es utilizando la siguiente fórmula:

𝑥𝛼=𝛼∗𝑥1+(1−𝛼)∗𝑥0

Para ir de 𝑥0 a 𝑥1, con un factor de 𝛼, donde 𝛼 ∈ [0,1]. Esta acción se puede observar en el segundo `for` dentro del ciclo principal:

```python
frame_contents = []
interpolations = 60
wait_frames = 10
cycle_distributions = cycle(functions)
f0 = next(cycle_distributions)
for _ in range(len(functions)):
    f1 = next(cycle_distributions)
    for _ in range(wait_frames):
        frame_contents.append(f0)
    for alpha in np.linspace(0,1, interpolations):
        frame_contents.append(alpha * f1 + (1 - alpha) * f0)
    f0 = f1
```

Y, finalmente, usando `FuncAnimation` de nuevo podemos crear la animación, cada vez graficando un valor diferente dentro de nuestro arreglo de valores `frame_contents`:

```python
fig = plt.figure(figsize=(8,5))
ax = fig.gca()

def animate(step, ax):
    ax.cla()
    sns.barplot(x=x_axis, y=frame_contents[step], ax=ax)
    ax.set_ylim(0, 0.8)
    ax.set_xticklabels(" ") 
    ax.set_yticklabels(" ") 
    
frames = len(frame_contents)
anim = FuncAnimation(fig, animate, frames=frames, fargs=(ax,))
anim.to_jshtml()
```

<iframe style="border:none;" src="https://mystifying-meitner-c75bce.netlify.com/multiplots.html" width="100%" height="450"></iframe>
<small>Sí, puedes interactuar con la gráfica de arriba.</small>

Y, ¡ya está! ahora espero que puedas tu también hacer animaciones para que tus datos te ayuden a contar historias más interesantes y atractivas. Te recuerdo que me puedes contactar en [@io_exception en Twitter](https://twitter.com/io_exception) si tienes dudas sobre lo que aquí te conté o te interesa saber más sobre Python o *matplotlib*.
