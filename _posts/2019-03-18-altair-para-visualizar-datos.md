---
layout: post
current: post
cover:  assets/blogposts/002.png
navigation: True
title: ggplot2 + BBC = bbplot
date: 2019-03-12 12:00:00
tags: [blog, recursos]
class: post-template
subclass: 'post tag-blog'
author: sergio
kernel_name: python
thebelab: true
repo: tacos-de-datos/tacos-en-jupyter
CodeMirrorMode: python
---

  <script src="https://cdn.jsdelivr.net/npm/vega@5.3.0"></script>
  <script src="https://cdn.jsdelivr.net/npm/vega-lite@3.0.0-rc15"></script>
  <script src="https://cdn.jsdelivr.net/npm/vega-embed@4.0.0-rc1"></script>
<!-- Load Thebelab for interactive widgets -->
{% include thebelab.html %}

{% include thebelab_button.html %}

<pre data-executable="true" data-language="python">
<code class = 'language-python'>import altair as alt
from vega_datasets import data

# Since the data is more than 5,000 rows we'll import it from a URL
source = data.zipcodes.url

alt.Chart(source).mark_circle(size=3).encode(
    longitude='longitude:Q',
    latitude='latitude:Q',
    color='digit:N'
).project(
    type='albersUsa'
).properties(
    width=650,
    height=400
).transform_calculate(
    "digit", alt.expr.substring(alt.datum.zip_code, 0, 1)
)
</code></pre>
