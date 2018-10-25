---
layout: archive
permalink: /posts/
title: Posts
author_profile: false
sidebar:
  - image: "/assets/images/me_rec.jpg"
description: "I am a Data scientist with training in Geographical information sciences. I am always interested in machine learning algorithms and data visualization. "
og_image: "/assets/images/me_rec.jpg"
---
I use this place to record things about computer science, data, photography, and many other aspects of life here. 

## Latest Stories

<div class="grid__wrapper">
  {% assign posts = site.posts %}
  {% for post in posts %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
