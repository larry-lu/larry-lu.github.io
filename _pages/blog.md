---
layout: archive
permalink: /posts/
title: Blog 
author_profile: true
description: "I am a Data scientist with training in Geographical information sciences. I am always interested in machine learning algorithms and data visualization. I try to use Python and R to address most of my questions whenever I can. "
---
I record things about computer science, data, photography, and many other aspects of life here. 

## Latest Stories

<div class="grid__wrapper">
  {% assign posts = site.posts %}
  {% for post in posts %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
