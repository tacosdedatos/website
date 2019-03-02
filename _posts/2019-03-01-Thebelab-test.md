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
---
  <!-- Load Thebelab for interactive widgets -->
{% include thebelab.html %}
{% include thebelab_button.html %}

¿Qué tal si pudieramos crear código en páginas estaticas de HTML?


<pre data-executable="true" data-language="python">
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
x = np.linspace(0,10)
plt.plot(x, np.sin(x))
plt.plot(x, np.cos(x))
</pre>


