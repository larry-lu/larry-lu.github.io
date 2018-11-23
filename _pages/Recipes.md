---
layout: archive
permalink: /recipes/
author_profile: false
sidebar:
  - image: "/assets/images/me_rec.jpg"
  - caption: "Photo credit: [**Clint Shannon**](https://www.instagram.com/clint_shannon/)"
  - image_alt: "profile_picture"
  - title: Why cooking is important
  - text: "The process of cooking involves the coordination between eyes, nose, ears, mouth, hand, and mind. It is rewarding to orchestrate all the processes to optimize the flavors, colors, nutrients, and textures from all the ingredients. It further inspires me to appreciate the cultures many of which I am not familiar with." 
---

Being on the road is always amazing, and I travel as much as I can. Other times, I try to be exploratory mentally by experiencing food from different regions. Compared with going to exotic restaurants, I prefer to learn how to cook nonnative dishes myself. Cooking has become a ritual for me to relax and reflect, and gives me a feeling of peace and fulfillment. 

## Recipes

<div class="grid__wrapper">
  {% assign posts = site.recipes %}
  {% for post in posts %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
