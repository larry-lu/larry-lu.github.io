---
title: "Classification of news articles using Naive Bayes classifier"
---

The objective of this assignment is to use the Naive Bayes algorithm to build a classifier to automatically categorize news articles into different topics. I used the Reuter’s dataset ([Reuters-21578](http://www.daviddlewis.com/resources/testcollections/reuters21578/)), which include thousands of news article items, each with its own topic label. The data are saved in 22 separate *.sgm files. For this assignment, I am only focusing on the below topics:

money, fx, crude, grain, trade, interest, wheat, ship, corn, oil, dlr, gas, oilseed, supply, sugar,gnp, coffee, veg, gold, soybean, bop, livestock, cp

The pyspark will be used for the entire project. We also use sklearn to compare the speed with pyspark.

{% capture fig_img %}
![News classification]({{ "https://www.google.com/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&ved=2ahUKEwiv75uDn4feAhUP7awKHXP1APQQjRx6BAgBEAU&url=https%3A%2F%2Fwww.providermatching.com%2Flatest-news-headlines-june-2018%2F&psig=AOvVaw3lJoZCeumu2434yVjtUZqy&ust=1539651519282751" | absolute_url }})
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption style="center">Photo from providermatching.com</figcaption>
</figure>

## Importing libraries and getting directories ready

```python
from os import chdir, getcwd
from glob import glob
import pyspark
import numpy as np
from bs4 import BeautifulSoup
import pandas as pd
import time
from pyspark.ml.feature import HashingTF, IDF, Tokenizer
import re
from nltk.corpus import stopwords
from nltk.stem.snowball import PorterStemmer, SnowballStemmer
```

```python
path = getcwd()
chdir(path)
```

## Defining our target and some useful functions

```python
topic_list = ["money", "fx", "crude", "grain", "trade", "interest", "wheat", "ship", "corn", "oil", "dlr", "gas", "oilseed", "supply", "sugar", "gnp", "coffee", "veg", "gold", "soybean", "bop", "livestock", "cpi"]
```
