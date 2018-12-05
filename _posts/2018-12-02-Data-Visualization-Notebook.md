---
title: "Notes about data visualization in Python"
categories:
  - Data
  - Visualization
  - Research
tags:
  - python
  - data
  - visualization
  - research
toc: true
use_math: true
header:
  teaser: "assets/images/note_images/rainCloudPlot.jpg"
excerpt: An updating notebook about data visualization using Python
---

>This is a notebook intended primarily to collect notes about data visualization. The post will be constantly updated as any other notebook.
{: .notice--primary}

## Common types of graph by purpose

### Distribution

#### Raincloud plot

[Raincloud-plot](https://xeno.graphics/raincloud-plot/)

>A [raincloud-plot](https://xeno.graphics/raincloud-plot/) combines boxplots, raw jittered data, and a split-half violin.

The following raincloud plot was created by Dr. [Micah Allen's blog](https://micahallen.org/2018/03/15/introducing-raincloud-plots/). The original plot was created in `R` using `ggplot2`. It turns out pog87 created the [`PtitPrince`](https://github.com/pog87/PtitPrince) library to create the raincloud-plot easily in `python`.

{% capture fig_img %}
![Raincloud-plot]({{ "../assets/images/note_images/rainCloudPlot.jpg" | relative_url }})
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption style="center">Rain cloud plot by Dr. Micah Allen</figcaption>
</figure>

## Interesting posts

[A Dramatic Tour through Python’s Data Visualization Landscape (including ggplot and Altair)](https://dsaber.com/2016/10/02/a-dramatic-tour-through-pythons-data-visualization-landscape-including-ggplot-and-altair/)

## Resources

- [python graph gallery](https://python-graph-gallery.com/): types of charts with reproducible code. 
- [From data to viz](https://www.data-to-viz.com/): a classification of common chart types based on input data format. 
  Note: (Most graphs using `R`, but it can be a good place to start.)
- [Xenographcis](https://xeno.graphics/): A collection of unusual charts and maps. 