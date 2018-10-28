---
layout: archive
permalink: /gallery/
author_profile: false
sidebar:
  - image: "/assets/images/me_rec.jpg"
  - caption: "Photo credit: [**Clint Shannon**](https://www.instagram.com/clint_shannon/)"
  - image_alt: "profile_picture"
  - title: Gallery
  - text: "It's interesting how the world is so distorted yet so real when you look at it through a lens. "
---

I am an amateur at photography, but I am not afraid. I love shooting street photography and I yearn to be good at it. To shoot in the street you have to be sharp and adaptable, and you have to be ready at all times. For someone who is a little paranoid about the crowdedness and noise associated with the street, street photography confronts me with my biggest fear. The lens is the best friend and the best outlet for me to express myself. Just like the shutter, everything about street photography is on/off at all times. But you have to keep looing.

Most of the time I use my Fujifilm X-T20 camera. I also use iPhone whever the camera is not around.

## On the film

<div class="grid__wrapper">
  {% assign posts = site.gallery %}
  {% for post in posts %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
