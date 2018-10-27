---
layout: archive
permalink: /media/
author_profile: false
sidebar:
  - image: "/assets/images/me_rec.jpg"
  - caption: "Photo credit: [**Clint Shannon**](https://www.instagram.com/clint_shannon/)"
  - image_alt: "profile_picture"
  - title: "Gallery"
  - text: "I use my Fuji XT20 camera for most of my works."
---

<div class="grid__wrapper">
  {% assign posts = site.gallery %}
  {% for post in posts %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
