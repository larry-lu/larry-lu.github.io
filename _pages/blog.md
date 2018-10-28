---
layout: archive
permalink: /posts/
author_profile: false
sidebar:
  - image: "/assets/images/me_rec.jpg"
  - caption: "Photo credit: [**Clint Shannon**](https://www.instagram.com/clint_shannon/)"
  - image_alt: "profile_picture"
  - title: "Posts"
  - text: "I use this place to record things about computer science, data, photography, and many other aspects of life. "
---

<div class="grid__wrapper">
  {% assign posts = site.posts %}
  {% for post in posts %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
