---
layout: post
current: post
cover:  assets/blogposts/001.png
navigation: True
title: ggplot2 + bbc = bbplot
date: 2019-03-10 12:00:00
tags: [blog, recursos]
class: post-template
subclass: 'post tag-blog'
author: sergio
kernel_name: ir
thebelab: true
repo: tacos-de-datos/probando-bbplot
---
<!-- Load Thebelab for interactive widgets -->
{% include thebelab.html %}

{% include thebelab_button.html %}

`ggplot2` es un paquete del lenguage `R` para la visualizacion de datos. El par de **G**s en `ggplot2` es de *Grammar of Graphics* o la ***Gramatica de Graficos*** - una lenguaje simple e intuitivo para *construir* graficos. 

# DOS ORACIONES SOBRE LA HISTORIA DE GRAMMAR OF GRAPHICS - EL LIBRO Grammar of Graphics y el Articulo de Hadley Wickham creador del tidyverse

## QUE ES BBPLOT
A finales de enero de este 2019, la BBC publico el paquete [`bbplot`](https://github.com/bbc/bbplot) y un [*libro de recetas*](https://bbc.github.io/r-cookbook) para crear varios graficos listos para publicacion con `ggplot2`. <br>
La meta es crear un proceso de creacion de graficos con su estilo personalizado mas reproducible y de paso ayudarle a principiantes de `R` a hacer graficos. 

### FIGURA DE EJEMPLO DE GRAFICOS QUE PUEDES CREAR (DEL COOKBOOK)



# Mas que un tutorial este articulo tiene la meta de explorar el paquete `bbplot`
`bbplot` es esencialmente dos funciones:
* `bbc_style()`: agrega atributos a tu grafico de `ggplot2`. Lo unico que necesitas hacer es agregar la linea `+ bbc_style()` a tu grafico de `ggplot2` para transformarlo en algo que pareciera haber salido de un articulo de la BBC.
* `finalise_plot()`: agrega los ultimos detalles a tu grafico y lo guarda como image `.png`. Esto esencialmente alinea a la izquierda el titulo y el subtitulo de tu grafico ademas de agregar una nota al pie con la fuente de tus datos y hasta un logo si asi lo deseas.

### Ejemplos
Sin mas preambulo veamos `bbplot` en accion. En **tacosdedatos** acabamos de agregar una magia antigua para poder hacer nuestros articulos mas interactivos :eyes: (se llama [thebelab](https://thebelab.readthedocs.io/)). Si vas al inicio de esta pagina veras el boton *activar codigo*. Al hacer clic transformaras las celdas de codigo aqui debajo en celdas ejecutables. Estas celdas *activadas* son editables asi que te invito a que cambies el codigo para personalizar los graficos un poco como se te ocurra. Detras de todo esto esta el poder de [MyBinder](https://mybinder.org) un proyecto del mismo equipo detras de `Project Jupyter` del cual aprenderemos mas adelante. 

### Primero necesitas cargar los paquetes necesarios
En el *libro de recetas* publicado en conjunto con `bbplot` la BBC sugiere utilizar el paquete `pacman` para cargar los paquetes necesarios a tu entorno. Esto es el equivalente de escribir `library("dplyr")`, `library("tidyr")`, `library("gapminder")`, etc. <br>
La primera linea del codigo instala `pacman` si no lo tienes. 
<pre data-executable="true" data-language="R">
if(!require(pacman))install.packages("pacman")

pacman::p_load('dplyr', 'tidyr', 'gapminder',
               'ggplot2',  'ggalt',
               'forcats', 'R.utils', 'png', 
               'grid', 'ggpubr', 'scales',
               'bbplot')          
</pre>

***
**OJO**, nosotros ya tenemos instalado el paquete `bbplot`. Si no lo haz instalado el codigo aqui arriba resultara en un error.<br>
`bbplot` no esta en CRAN, el sistema central de paquetes de `R` del que normalmente descargarias un paquete nuevo. <br>
A `bbplot` lo instalas desde *GitHub* con `devtools`. Esto puede ser un poco confuso para los principiantes ya que en esencia son dos pasos aunque con todos los que hable en preparacion para este articulo me lo contaron como si fuera algo simple y sencillo... :eyes:
<br>
Paso 1: instala `devtools`, el paquete que te ayuda a instalar paquetes de *GitHub*. Este si existe en CRAN asi que solo necesitas ejecutar:
```r
install.packages("devtools")
```
Paso 2: instala `bbplot` utilizando `devtools`:
```r
devtools::install_github("bbc/bbplot")
```

**OJO PARTE 2**, existe un sinfin de razones por las cuales esto no funcione en ciertos sistemas. El servidor conectado a esta pagina donde estas ejecutando codigo esta basado en `Linux` (Ubuntu, creo :p) y por alguna razon no podiamos instalarlo con `devtools`. Lo que tuvimos que hacer es clonar el repositorio `bbc/bbplot`, instalarlo como **source** y luego borramos los archivos de donde estabamos trabajando ya que no los necesitamos mas. 

```shell
git clone https://github.com/bbc/bbplot.git
R --quiet -e "install.packages('bbplot', repos = NULL, type = 'source')"
rm -rf bbplot # en sistemas linux/macos 'rmdir /s /s bbplot' en Windows
```

***


### Ya que tenemos todos los paquetes instalados y cargados en tu entorno podemos hacer nuestros graficos
<pre data-executable="true" data-language="R">
# Datos de gapminder
datos_para_linea <- gapminder %>%
  filter(country == "Colombia") 

# crea el grafico
linea <- ggplot(datos_para_linea, aes(x = year, y = lifeExp)) +
  geom_line(colour = "#1380A1", size = 1) +
  geom_hline(yintercept = 0, size = 1, colour="#333333") +
  labs(title="Pura Vida",
       subtitle = "Esperanza de Vida en Colombia 1952-2007") +
  bbc_style()    

# muestra el grafico
linea
</pre>

Pero vayamos paso a paso
<pre data-executable="true" data-language="R">
# Ya tenemos cargados los datos
# crea el grafico - paso 1
linea <- ggplot(datos_para_linea, aes(x = year, y = lifeExp))

# muestra el grafico
linea
</pre>

<pre data-executable="true" data-language="R">
# Ya tenemos cargados los datos
# crea el grafico - paso 2
linea <- ggplot(datos_para_linea, aes(x = year, y = lifeExp)) +
  geom_line(colour = "#1380A1", size = 1) 

# muestra el grafico
linea
</pre>

<pre data-executable="true" data-language="R">
# Ya tenemos cargados los datos
# crea el grafico - paso 3
linea <- ggplot(datos_para_linea, aes(x = year, y = lifeExp)) +
  geom_line(colour = "#1380A1", size = 1) +
  geom_hline(yintercept = 0, size = 1, colour="#333333")

# muestra el grafico
linea
</pre>

<pre data-executable="true" data-language="R">
# Ya tenemos cargados los datos
# crea el grafico - paso 4
linea <- ggplot(datos_para_linea, aes(x = year, y = lifeExp)) +
  geom_line(colour = "#1380A1", size = 1) +
  geom_hline(yintercept = 0, size = 1, colour="#333333") +
  labs(title="Pura Vida",
       subtitle = "Esperanza de Vida en Colombia 1952-2007")

# muestra el grafico
linea
</pre>

<pre data-executable="true" data-language="R">
# Ya tenemos cargados los datos
# crea el grafico - paso 5
linea <- ggplot(datos_para_linea, aes(x = year, y = lifeExp)) +
  geom_line(colour = "#1380A1", size = 1) +
  geom_hline(yintercept = 0, size = 1, colour="#333333") +
  labs(title="Pura Vida",
       subtitle = "Esperanza de Vida en Colombia 1952-2007") +   
  bbc_style()

# muestra el grafico
linea
</pre>



## MAS EJEMPLOS

<pre data-executable="true" data-language="R">
<code class="r">
library("ggalt")
library("tidyr")

#Prepare data
dumbbell_df <- gapminder %>%
  filter(year == 1967 | year == 2007) %>%
  select(country, year, lifeExp) %>%
  spread(year, lifeExp) %>%
  mutate(gap = `2007` - `1967`) %>%
  arrange(desc(gap)) %>%
  head(10)

#Make plot
ggplot(dumbbell_df, aes(x = `1967`, xend = `2007`, y = reorder(country, gap), group = country)) + 
  geom_dumbbell(colour = "#dddddd",
                size = 3,
                colour_x = "#FAAB18",
                colour_xend = "#1380A1") +
  bbc_style() + 
  labs(title="We're living longer",
       subtitle="Biggest life expectancy rise, 1967-2007")
</code>
</pre>



<pre data-executable="true" data-language="R">
#Prepare data
facet <- gapminder %>%
  filter(continent != "Americas") %>%
  group_by(continent, year) %>%
  summarise(pop = sum(as.numeric(pop)))

#Make plot
facet_plot <- ggplot() +
  geom_area(data = facet, aes(x = year, y = pop, fill = continent)) +
  scale_fill_manual(values = c("#FAAB18", "#1380A1","#990000", "#588300")) + 
  facet_wrap( ~ continent, ncol = 5) + 
  scale_y_continuous(breaks = c(0, 2000000000, 4000000000),
                     labels = c(0, "2bn", "4bn")) +
  bbc_style() +
  geom_hline(yintercept = 0, size = 1, colour = "#333333") +
  theme(legend.position = "none",
        axis.text.x = element_blank()) +
  labs(title = "Asia's rapid growth",
       subtitle = "Population growth by continent, 1952-2007")

facet_plot
</pre>

<pre data-executable="true" data-language="R">
#Make plot
facet_plot_free <- ggplot() +
  geom_area(data = facet, aes(x = year, y = pop, fill = continent)) +
  facet_wrap(~ continent, scales = "free") + 
  bbc_style() +
  scale_fill_manual(values = c("#FAAB18", "#1380A1","#990000", "#588300")) +
  geom_hline(yintercept = 0, size = 1, colour = "#333333") +
  theme(legend.position = "none",
        axis.text.x = element_blank(),
        axis.text.y = element_blank()) +
  labs(title = "It's all relative",
       subtitle = "Relative population growth by continent,1952-2007")

facet_plot_free
</pre>
