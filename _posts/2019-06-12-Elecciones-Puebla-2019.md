---
layout: post
current: post
cover:  assets/blogposts/007.png
navigation: True
title: Elecciones para gobernador - Puebla 2019
date: 2019-06-12 12:00:00
tags: [blog, nota, mapas, altair]
class: post-template
subclass: 'post tag-blog'
author: israel
---
<style>
    .vega-actions a {
        margin-right: 12px;
        color: #757575;
        font-weight: normal;
        font-size: 13px;
    }
    .error {
        color: red;
    }
</style>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm//vega@5"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm//vega-lite@3.3.0"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm//vega-embed@4"></script>


Un análisis y visualización de los datos de la elección de gobernador de Puebla este junio 2019. Análisis en R por Israel Piña ([@YaNiPaper](https://twitter.com/yanipaper)) y visualización por Sergio Sánchez (recreada en `altair` a partir de las visualizaciones estáticas de Israel) ([@tacosdedatos](https://twitter.com/tacosdedatos)). 

Los repositorios con el código y los datos se encuentran en GitHub:
1. Análisis por Israel: [tacos-de-datos/elecciones-puebla-2019](https://github.com/tacos-de-datos/elecciones-puebla-2019/) - [Pruébalo en Rstudio en mybinder.org](https://mybinder.org/v2/gh/tacos-de-datos/elecciones-puebla-2019/master?urlpath=rstudio)
2. Visualización por Sergio: [tacos-de-datos/vis-elecciones-puebla-2019](https://github.com/tacos-de-datos/vis-elecciones-puebla-2019/) - [Pruébalo en JupyterLab en mybinder.org](https://mybinder.org/v2/gh/tacos-de-datos/vis-elecciones-puebla-2019/master?urlpath=lab/tree/notebooks/01_Visualizacion.ipynb)

_Nota: Estas visualizaciones son de 10-12 megabytes así que esta página puede tardarse un poco en cargar (oops 😁)_

_Nota 2: No caben en una pantalla de móvil - estamos trabajando en eso pero es demasiada información como para que la disfrutes bien en tu móvil así que visitanos en tu compu porfa_.


### Visualización a nivel municipal
<div id="municipios"></div>
<hr>

### Visualización a nivel sección
<div id="participacion"></div>
<script type="text/javascript" src="../assets/blogposts/007_municipios.js"></script>
<script type="text/javascript" src="../assets/blogposts/007_participacion.js"></script>


*** 

¿Qué te pareció la nota? [Mandanos un tuit a @tacosdedatos](https://twitter.com/share?text=Obvio+que+estuvo+super+el+blog+%40tacosdedatos+%F0%9F%8C%AE) o [ a @YaNiPaper](https://twitter.com/share?text=Obvio+que+estuvo+super+el+blog+%40yanipaper+%F0%9F%8C%AE)envianos un correo a [✉️ sugerencias@tacosdedatos.com](mailto:sugerencias@tacosdedatos.com?subject=Sugerencia&body=Hola-holaaa). Y recuerda que puedes subscribirte a nuestro boletín aquí debajo. Cada mes te enviamos enviamos nuestras publicaciones y las últimas noticias directamente a tu caja de entrada.
