---
layout: post
current: post
cover:  assets/blogpost/00X.png
navigation: True
title: Titulo de tu Blogpost #00X
date: 2019-02-12 12:00:00
tags: [blog, R, python, etc,]
class: post-template
subclass: 'post tag-cachitos'
author: sergio
---


la primera linea aparecerá en postal junto a la portada. Debe de ser menos de 220 caracteres.
Ejemplo: "Datawrapper es una herramienta de visualización de datos _"por periodistas, para periodistas"_. No necesitas saber programar o de diseño para crear visualizaciones atractivas e interactivas en cuestión de minutos."


Asegurate que la _metadata_ de tu archivo Markdown (`.md`) incluya lo siguiente:
```
---
layout: post *
current: post *
cover:  assets/blogpost/00X.png
navigation: True *
title: Título de tu Blogpost #00X
date: 2019-02-12 12:00:00
tags: [blog, R, python, etc,]
class: post-template *
subclass: 'post tag-blog' *
author: sergio
---
```
las líneas con * deben mantenerse iguales en todos los blogs (layout, current, navigation, class, y subclass).

`cover:`Si tu blogpost tiene una imagen principal agregala a `assets/blogposts/` con el número correspondiente y asegurate que tenga por lo menos 3 dígitos. Por ejemplo, si ya existen 4 blogs, la imagen principal de tu blog sería `005.png`. Todas las imagenes que vas a utilizar en tu blog post deben comenzar con este numero (por ejemplo, la imagen 1 sería algo como `005_ejemplo1.png`). Tu imagen principal va en `cover:`

`title:` Aquí va el título de tu blog. Trata de mantenerlo descriptivo pero corto.

`date:` La fecha de publicación. Te recomiendo aqui comenzar con la fecha y hora de ahora mismo y luego la cambiamos a la fecha real. Cuando mandas una Pull Request, Netlify toma tus archivos y crea un demo en uno de sus servidores para que veas como se va a ver tu blog en el sitio. Si tienes una fecha en el futuro al momento de mandar la Pull Request, no vas a poder ver tu post.

`tags:` las etiquetas de tu blog post. Trata de mantenerlo a 3 o 4 etiquetas. La primera debe ser "blog", la segunda te recomiendo que sea el lenguaje de programación del que estas escribiendo, y las demás pueden ser lo que tu quieras. Mantenlas descriptivas, estas etiquetas es como van a encontrar tu blog en tacosdedatos.com/

`author:` autor. Aquí no va tu nombre completo. En el archivo `authors.yml` en el directorio `_data/` debes primero agregar tu información siguiendo el formato yaml. Por ejemplo,
```yaml
TUNOMBRE:
  username: TUNOMBRE
  name: Sergio Sánchez Zavala
  url_full: https://soyserg.io/
  url: soyserg.io
  bio: Soy investigador asociado de política pública basado en San Francisco, California. Diseñador de visualizaciones de datos
  picture: assets/images/yo.jpg
  facebook: chekos.visuals
  twitter: chekoswh
  cover: assets/images/chekos-cover.PNG
```
No tienes que agregar toda esta información si no quieres. Simplemente elimina los campos que no quieras agregar a tu bio. Asegurate de agregar una foto de perfil (`picture:`) y una principal (`cover:`) si deseas tenerlas en tu página personal de tacosdedatos.com/

Eso es lo más importante. Fuera de eso recuerda que esto es un archivo Markdown. Si quieres aprender más de como escribir Markdown aquí hay una _cheatsheet_: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

También puedes escribir en `HTML` si así lo deseas. Pero no te lo recomiendo amenos que sepas lo que estás haciendo.