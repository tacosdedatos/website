---
layout: post
current: post
cover:  assets/detrasdelavis/004.png
navigation: True
title: Word2vec ilustrado
date: 2020-09-05 10:00:00
tags: [python, skelarn, texto]
class: post-template
subclass: 'post tag-tutoriales'
author: io_exception
math: True
---

 > “Hay en todas las cosas un ritmo que es parte de nuestro universo. Hay simetría, elegancia y gracia… esas cualidades a las que se acoge el verdadero artista. Uno puede encontrar este ritmo en la sucesión de las estaciones, en la forma en que la arena modela una cresta, en las ramas de un arbusto creosota o en el diseño de sus hojas. 
 > Intentamos copiar este ritmo en nuestras vidas y en nuestra sociedad, buscando la medida y la cadencia que reconfortan. Y sin embargo, es posible ver un peligro en el descubrimiento de la perfección última. Está claro que el último esquema contiene en sí mismo su propia fijeza. En esta perfección, todo conduce hacia la muerte” ~ Frank Herbert. “Dune” (1965)  

Encuentro la idea de *embeddings*\* una de las más fascinantes dentro del aprendizaje automático. Si alguna vez has usado *Siri*, *Google Assistant*, *Alexa* o *Google Translate*, o inclusive un teléfono con teclado que predice tu siguiente palabra, entonces seguramente te has beneficiado de esta idea que se a convertido en la clave de los modelos de Procesamiento de Lenguaje Natural (*NLP*). En las últimas décadas ha existido mucho desarrollo respecto a usar *embeddings* para modelos neuronales (investigaciones recientes incluyen *embeddings* contextualizados que llevan a modelos vanguardistas como [BERT](https://jalammar.github.io/illustrated-bert/) o GPT2).

**Word2vec** es un método para crear *embeddings* de forma eficioente que ha existido desde 2013. pero además de su utilidad para la creación de estos *embeddings*, algunos de sus conceptos han sido exitosamente empleados para crear modelos de recomendación y para hacer sentido de datos secuenciales, inclusive en aplicaciones comerciales no relacionadas con lenguajes. Compañías como [Airbnb](https://www.kdd.org/kdd2018/accepted-papers/view/real-time-personalization-using-embeddings-for-search-ranking-at-airbnb), [Alibaba](https://www.kdd.org/kdd2018/accepted-papers/view/billion-scale-commodity-embedding-for-e-commerce-recommendation-in-alibaba), [Spotify](https://www.slideshare.net/AndySloane/machine-learning-spotify-madison-big-data-meetup), y [Anghami](https://towardsdatascience.com/using-word2vec-for-music-recommendations-bb9649ac2484) le han sacado provecho a esta brillante pieza del mundo del *NLP* y la están usando para potenciar una nueva clase de modelos de recomendación.  

En este post, vamos a revisar el concepto de *embeddings*, y cómo es que se generan estos con la técnica de *word2vec*. Pero comencemos con un ejemplo para familiarizarnos con el uso de vectores para representar cosas. ¿Sabías que una lista de cinco números (es decir, un vector) puede representar mucho sobre tu personalidad?

## *Embeddings* de personalidad: ¿cómo eres?  

 > “Te doy el camaleón del desierto, cuya habilidad para mezclarse con el fondo te dice todo lo que necesitas saber sobre las raíces de la ecología y las bases de la identidad personal” ~ Hijos de Dune

En una escala de 0 a 100, ¿qué tan introvertido/extovertido eres tu (donde 0 es introvertido, y 100 es extrovertido)? ¿alguna vez has tomado un test de personalidad como MBTI – o, mejor aún, una prueba del [modelo de los cinco grandes](https://es.wikipedia.org/wiki/Modelo_de_los_cinco_grandes)? si no lo has hecho, este tipo de pruebas te hace una serie de preguntas y te califica en diferentes ejes, siendo la introversión o extraversión uno de ellos.

![](http://jalammar.github.io/images/word2vec/big-five-personality-traits-score.png)

<small>Ejemplo de un resultado en una prueba del modelo de los cinco grandes. Este te puede decir mucho sobre ti mismo y ha mostrado tener habilidades predictivas en cuanto a éxito [académico](http://psychology.okstate.edu/faculty/jgrice/psyc4333/FiveFactor_GPAPaper.pdf), [personal](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1744-6570.1999.tb00174.x) y [profesional](https://www.massgeneral.org/psychiatry/assets/published_papers/soldz-1999.pdf). [Aquí](https://projects.fivethirtyeight.com/personality-quiz/) puedes calcular tus valores.</small>

Imagina que obtuve 38/100 en el eje de introversión/extroversión. Lo podemos graficar así:  

![](http://jalammar.github.io/images/word2vec/introversion-extraversion-100.png)

Si colocamos los valores de -1 a 1:

![](http://jalammar.github.io/images/word2vec/introversion-extraversion-1.png)

¿Qué tan bien crees conocer a una persona si sabes solo esta información sobre ella? No mucho, las personas son complejas. Añadamos otra dimensión, la calificación de otra de las características del test:  

![](http://jalammar.github.io/images/word2vec/two-traits-vector.png)

<small>Podemos representar dos dimensiones como un punto en la gráfica, o mejor aún, como un vector desde el origen a ese punto. Tenemos herramientas increíbles que para trabajar con vectores que nos resultarán útiles más adelante.</small>

He ocultado qué características estamos graficando solamente para que nos acostumbremos a no saber qué representa cada dimensión – aún así estamos obteniendo mucha información de la representación vectorial de cada una de las personalidades.  

Ahora podemos decir que este vector representa parcialmente mi personalidad. La usabilidad de esta representación es útil cuando quieres comparar otras dos personas conmigo. Digamos que me atropella un autobus y deboi ser reemplazado por alguien con una personalidad similar. Dada la siguiente figura, ¿cuál de las dos personas es más similar a mi?  

![](http://jalammar.github.io/images/word2vec/personality-two-persons.png)

Cuando estamos trabajando con vectores, una forma común de calcular una medida de similitud es la [similitud coseno](https://es.wikipedia.org/wiki/Similitud_coseno) (o *cosine similarity* que es su nombre en inglés).  

![](http://jalammar.github.io/images/word2vec/cosine-similarity.png)

<small><span style="color: #70BF41;">Person #1</span> es más similar a mi en cuanto a personalidad. Vectores que apuntan a la misma dirección (aunque la longitud también tiene que ver) tienen una similitud coseno más grande.</small>

Aún así, dos dimensiones no son suficientes para capturar información suficiente sobre qué tan diferentes dos personas son. Décadas de investigación psicógica han llevado a que existen 5 características (y muchas sub-características). Así que vamos a usar todas en nuestras comparaciones:  

![](http://jalammar.github.io/images/word2vec/big-five-vectors.png)

El problema con estas cinco dimensiones es que hemos perdido la habilidad de graficar esas flechitas tan bonitas en dos dimensiones. Este es un obstáculo común en el aprendizaje automático en el que constantemente tenemos que pensar en espacios de grandes dimensiones. La ventaja que teneos es que la similitud coseno aún funciona sin importar las dimensiones:  

![](http://jalammar.github.io/images/word2vec/embeddings-cosine-personality.png)

<small>La similitud coseno funciona sin importar el número de dimensiones. Estas son mejores califacaciones porque han sido calculadas en una representación con mayor resolución de las cosas que están siendo comparadas.</small>

Para concluír esta sección, quiero que nos quedemos con dos ideas principales:  

 1. Podemos representar personas (y cosas) como vectores de números (lo que es perfecto para las computadoras).
 2. Podemos comparar fácilmente qué tan similares son los vectores entre sí.  

![](http://jalammar.github.io/images/word2vec/section-1-takeaway-vectors-cosine.png)

## *Embeddings* de palabras  

 > “El don de las palabras es el don del engaño y la ilusión” ~ Hijos de Dune  

Entendiendo esto, podemos proceder a ver ejemplos de vectores-palabra entrenados (también conocidos como *embeddings*) y ver algunas de sus propiedades interesantes.  

Este es un *embedding* para la palabra *"king"* –rey, en inglés– (GloVe vector entrenado en Wikipedia):

```
[ 0.50451 , 0.68607 , -0.59517 , -0.022801, 0.60046 , -0.13498 , -0.08813 , 0.47377 , -0.61798 , -0.31012 , -0.076666, 1.493 , -0.034189, -0.98173 , 0.68229 , 0.81722 , -0.51874 , -0.31503 , -0.55809 , 0.66421 , 0.1961 , -0.13495 , -0.11476 , -0.30344 , 0.41177 , -2.223 , -1.0756 , -1.0783 , -0.34354 , 0.33505 , 1.9927 , -0.04234 , -0.64319 , 0.71125 , 0.49159 , 0.16754 , 0.34344 , -0.25663 , -0.8523 , 0.1661 , 0.40102 , 1.1685 , -1.0137 , -0.21585 , -0.15155 , 0.78321 , -0.91241 , -1.6106 , -0.64426 , -0.51042 ]
```

Es una lista de 50 números. No podemos decir mucho si solamente vemos estos números. 
