---
layout: post
current: post
cover:  assets/blogposts/001.png
navigation: True
title: Probando Thebelab
date: 2019-03-01 12:00:00
tags: [blog, recursos]
class: post-template
subclass: 'post tag-blog'
author: sergio
kernel_name: R
thebelab: true
repo: tacos-de-datos/probando-bbplot
---
<!-- Load Thebelab for interactive widgets -->
{% include thebelab.html %}

{% include thebelab_button.html %}

¿Qué tal si pudieramos crear código en páginas estaticas de HTML?


<pre data-executable="true" data-language="R">
if(!require(pacman))install.packages("pacman")

pacman::p_load('dplyr', 'tidyr', 'gapminder',
               'ggplot2',  'ggalt',
               'forcats', 'R.utils', 'png', 
               'grid', 'ggpubr', 'scales',
               'bbplot')

#Datos de gapminder
line_df <- gapminder %>%
  filter(country == "Malawi") 

#crea el grafico
line <- ggplot(line_df, aes(x = year, y = lifeExp)) +
  geom_line(colour = "#1380A1", size = 1) +
  geom_hline(yintercept = 0, size = 1, colour="#333333") +
  bbc_style() +
  labs(title="Living longer",
       subtitle = "Life expectancy in Malawi 1952-2007")     

line          
</pre>


