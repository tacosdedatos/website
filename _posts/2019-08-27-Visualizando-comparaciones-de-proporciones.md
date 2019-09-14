---
layout: post
current: post
cover:  assets/blogposts/008.png
navigation: True
title: Visualizando Comparaciones de Proporciones
date: 2019-08-27 10:00:00
tags: [blog,]
class: post-template
subclass: 'post tag-blog'
author: sergio
---

Recientemente en mi trabajo tuvimos una discusión muy importante sobre el futuro de nuestros _productos_ - reportes, blogs, informes, etc. además de las visualizaciones que los acompañarán. La primera saldrá a la luz muy pronto y va a incluir algo como lo siguiente. 

<iframe title="Voters do not reflect California's racial diversity" aria-label="Stacked Column Chart" id="datawrapper-chart-6FC72" src="//datawrapper.dwcdn.net/6FC72/1/" scrolling="no" frameborder="0" style="width: 0; min-width: 100% !important; border: none;" height="400"></iframe><script type="text/javascript">!function(){"use strict";window.addEventListener("message",function(a){if(void 0!==a.data["datawrapper-height"])for(var e in a.data["datawrapper-height"]){var t=document.getElementById("datawrapper-chart-"+e)||document.querySelector("iframe[src*='"+e+"']");t&&(t.style.height=a.data["datawrapper-height"][e]+"px")}})}();</script>

Esto es un gráfico de barras _apiladas_ (? es _stacked bar chart_ en inglés) que esta comparando 3 grupos: No votantes, Adultos y Votantes probables. Los datos provienen de nuestra encuesta a nivel estatal de California, EEUU. El punto que _se supone_ que esta resaltando es que los latinxs en California estan subrepresentados en el electorado y los blancos estan sobrerepresentados. Es decir, aunque los latinxs sean el X% de los adultos en California representan un porcentage menor de los Votantes probables. Lo opuesto es verdad sobre la población blanca de California.

Aquí va la primera modificación que yo hubiera hecho: **Sí lo que estas tratando de resaltar atrae el mismo nivél de atención que el resto de los elementos de tu gráfico - no lo estás resaltando.** 

Es decir, si lo que estamos tratando de enfatizar es que existe este fenómeno similar pero opuesto en las poblaciones latinx y blanca en California - resaltemos las poblaciones latinx y blanca.

<iframe title="Voters do not reflect California's racial diversity" aria-label="Stacked Column Chart" id="datawrapper-chart-Yuveq" src="//datawrapper.dwcdn.net/Yuveq/1/" scrolling="no" frameborder="0" style="width: 0; min-width: 100% !important; border: none;" height="400"></iframe><script type="text/javascript">!function(){"use strict";window.addEventListener("message",function(a){if(void 0!==a.data["datawrapper-height"])for(var e in a.data["datawrapper-height"]){var t=document.getElementById("datawrapper-chart-"+e)||document.querySelector("iframe[src*='"+e+"']");t&&(t.style.height=a.data["datawrapper-height"][e]+"px")}})}();</script>

Pero en realidad, lo que yo quisiera (y propuse) fue el cambiar completamente el tipo de gráfico que estamos utilizando. 

Los gráficos de barras son útiles para visualizar _magnitudes_. El tamaño de una barra me indíca, fácilmente, _que tanto_ de algo tengo. Es decir, cuando vez la gráfica de en medio (Adultos) y por el contexto sabes que es un porcentaje, ver una barra que sube el 42% del espacio entre el 0 y el 100 te ayuda a contextualizar ese 42% más que si fuera un círculo, por ejemplo, en un gráfico de dispersión. 

En cierto modo, esta gráfica de barras apiladas es esencialmente un gráfico de pastel, pero eso es para otro blog.

El punto que quiero tocar es que en el título y las notas del gráfico no estamos hablando de magnitudes. No importa si la población latinx es 19 o 23 o 26 por ciento. Lo que importa es que en cada una de estas barras es diferente. 

__Lo que queremos visualizar es la diferencia, no la magnitud.__

Por eso yo preferiría utilizar algo que se conoce como _rangle plot_ o gráfico de rango (a veces también aparece en el internet como _dumbbell chart_ o gráfico de _pesas_ por su similitud a las pesas que uno levanta en el gimnasio).

<iframe title="Voters do not reflect California's racial diversity" aria-label="Dot Plot" id="datawrapper-chart-UsK7s" src="//datawrapper.dwcdn.net/UsK7s/1/" scrolling="no" frameborder="0" style="width: 0; min-width: 100% !important; border: none;" height="258"></iframe><script type="text/javascript">!function(){"use strict";window.addEventListener("message",function(a){if(void 0!==a.data["datawrapper-height"])for(var e in a.data["datawrapper-height"]){var t=document.getElementById("datawrapper-chart-"+e)||document.querySelector("iframe[src*='"+e+"']");t&&(t.style.height=a.data["datawrapper-height"][e]+"px")}})}();</script>

Con este graáfico es más fácil observar las diferencias entre las 3 categorías: No votantes, Adultos, Votantes probables. 

No solo de nuestros 2 grupos principales (latinxs y blancos) pero también aquí podemos ver que, por ejemplo, los números de Votantes probables y Adultos de la población afro-americana son los mismos. Es decir, no son ni sub- ni sobrerepresentados en el electorado. 

Pero la verdad es que los grupos asiático-americanos, afro-americanos, y otros están presentes en el gráfico de barras por el simple hecho de que los porcentajes tienen que sumar a 100. Los números son muy bajos para extraer conclusiones y el punto que estamos tratando de ilustrar es lo que acontece en las poblaciones latinx y blanca.

Por esa razón, propuse el mostrar sólo estos dos grupos. Algo que se vería así:

<iframe title="Voters do not reflect California's racial diversity" aria-label="Dot Plot" id="datawrapper-chart-B15v1" src="//datawrapper.dwcdn.net/B15v1/1/" scrolling="no" frameborder="0" style="width: 0; min-width: 100% !important; border: none;" height="203"></iframe><script type="text/javascript">!function(){"use strict";window.addEventListener("message",function(a){if(void 0!==a.data["datawrapper-height"])for(var e in a.data["datawrapper-height"]){var t=document.getElementById("datawrapper-chart-"+e)||document.querySelector("iframe[src*='"+e+"']");t&&(t.style.height=a.data["datawrapper-height"][e]+"px")}})}();</script>

Este gráfico muestra, en mi opinión, de una manera más fácil para la lectora que ambos grupos (latinxs y blancos) tienen proporciones similares de la población de adultos (35 vs 42%) pero esto cambia de manera drástica y en direcciones opuestas cuando exploramos la población de No votantes y de Votantes probables.

Con este gráfico ves dos _rangos_ similares pero opuestos. Eso es el punto que estamos tratando de ilustrar en nuestro reporte. 

Incluso sería posible ir un paso más adelante y solo comparar las proporciones de adultos y de votantes problables, ya que son las dos categorías a las que les estamos poniendo atención en el reporte (y en el título y las notas del gráfico).

Podríamos hacer algo así:

<iframe title="Voters do not reflect California's racial diversity" aria-label="Range Plot" id="datawrapper-chart-rvdfO" src="//datawrapper.dwcdn.net/rvdfO/1/" scrolling="no" frameborder="0" style="width: 0; min-width: 100% !important; border: none;" height="193"></iframe><script type="text/javascript">!function(){"use strict";window.addEventListener("message",function(a){if(void 0!==a.data["datawrapper-height"])for(var e in a.data["datawrapper-height"]){var t=document.getElementById("datawrapper-chart-"+e)||document.querySelector("iframe[src*='"+e+"']");t&&(t.style.height=a.data["datawrapper-height"][e]+"px")}})}();</script>

Esto ilustra todavía más efectivamente no solo que existe una diferencia similar en la manera en que estas poblaciones forman estas dos categorías (adultos y votantes problables), sino que enfatiza que estas diferencias van en direcciones opuestas: una población es **sobrerepresentada** y la otra es **subrepresentada**.

Cuando queremos visualizar diferencias es fácil pensar "_Bueno, agrupo mis barras por categoría, las cambio de color y mis lectoras y lectores verán que las barras de un color son más grandes que las otras_". O como en este caso "_Quienes lean este reporte podrán ver que en una barra el cachito de color naranja es más grande en esta barra que en aquella_". 

Puede ser. Pero lo que en realidad estás pidiendo es que tus lectoras tomen estos numeros (o barras) y calculen la diferencia entre estos grupos **y luego comparen estas diferencias con las diferencias calculadas de otras categorías**. 

¿Por qué? Si lo que quieres es ilustrar que ciertos grupos son _diferentes_, visualiza esa diferencia. 

*** 

¿Qué te pareció la nota? [Mandanos un tuit a @tacosdedatos](https://twitter.com/share?text=Obvio+que+estuvo+super+el+blog+%40tacosdedatos+%F0%9F%8C%AE) o envianos un correo a [✉️ sugerencias@tacosdedatos.com](mailto:sugerencias@tacosdedatos.com?subject=Sugerencia&body=Hola-holaaa). Y recuerda que puedes subscribirte a nuestro boletín aquí debajo. Cada mes te enviamos enviamos nuestras publicaciones y las últimas noticias directamente a tu caja de entrada.
