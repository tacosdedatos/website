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
kernel_name: python3
thebelab: true
---
<!-- Load Thebelab for interactive widgets -->
{% include thebelab.html %}

{% include thebelab_button.html %}

¿Qué tal si pudieramos crear código en páginas estaticas de HTML?


<pre data-executable="true" data-language="python">
%matplotlib inline
import osmnx as ox
G = ox.graph_from_place('Manhattan Island, New York City, New York, USA', network_type='drive')
ox.plot_graph(G)
</pre>


