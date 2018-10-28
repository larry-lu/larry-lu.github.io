---
title: "Classification of news articles using Naive Bayes classifier"
categories:
  - Data
tags:
  - python
  - spark
  - data
toc: true
header:
  teaser: "assets/images/post_images/news.jpg"
excerpt: Using Pyspark to classify news articles.
---

The objective of this assignment is to use the [Naive Bayes classifier](https://en.wikipedia.org/wiki/Naive_Bayes_classifier) to build a classifier to automatically categorize news articles into different topics. I used the Reuter’s dataset ([Reuters-21578](http://www.daviddlewis.com/resources/testcollections/reuters21578/)), which include thousands of news article items, each with its own topic label. The data are saved in 22 separate *.sgm files. 

{% capture fig_img %}
![News classification](https://www.providermatching.com/wp-content/uploads/2018/06/news.jpg)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption style="center">Photo from providermatching.com</figcaption>
</figure>

For this project, I am only focusing on the below topics:money, fx, crude, grain, trade, interest, wheat, ship, corn, oil, dlr, gas, oilseed, supply, sugar,gnp, coffee, veg, gold, soybean, bop, livestock, cp

The [`pyspark`](http://spark.apache.org/docs/2.2.0/api/python/pyspark.html) will be used for the entire project. We also use [`sklearn`](http://scikit-learn.org/stable/) to compare the speed with `pyspark`.

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

```python
def if_topic_in(topic, topic_list = topic_list):
    """function to determine if each entry belongs to our topic list
    ---------------------------------------------
    
    :param topic: list of many topics of one article
    :param topic_list: list of pre-defined topics
    
    :returns: index of first element in the topic list that belongs to topic_list
    """
    try:
        ans = list(set(topic).intersection(topic_list))
    except:
        ans = ""
    
    return ans
```

```python
def cleanbody(text):
    """function to clean text by removing punctuations, and numbers
    ---------------------------------------------
    
    :param text: a string
    
    :returns: string with punctuations and numbers removed
    """
    stopwords_set= set(stopwords.words('english'))
    stemmer = SnowballStemmer('english')
    text = text.replace('\n',' ').lower().strip()
    text = re.sub("[^a-zA-Z]+", " ", text).split()
    text = ' '.join(stemmer.stem(i) for i in text)
    stemmed = ' '.join([word for word in text.split() if word not in stopwords_set])
    return(stemmed)
```

## Pre-processing the Reuters' dataset (Reuters-21578)

For this project we are only interested in articles whose topic falls in the topic list. 

Some articles are of multiple topics separated by dashes (e.g. "money-fx"). In this case, we will create duplicate articles with each article corresponding to one topic. This will inevitably reduce the accuracy of our output, but we can live with it for this project.

We started with parsing through all the (*.sgm) files and create a list of all relevant articles as tuples (topic, body). The [`BeautifulSoup`](https://www.crummy.com/software/BeautifulSoup/) library is used for parsing.

```python
f_list = glob('reuters21578/*.sgm')

doi_list = list()
for filename in f_list:
    print('Start parsing {0}...'.format(filename))
    file = open(filename, 'rb')
    soup = BeautifulSoup(file, 'html.parser')
    file.close()
    for topic_raw in soup.find_all('topics'):
        topic = topic_raw.get_text().split('-')
        topic = if_topic_in(topic)
        if len(topic) != 0:
            body = topic_raw.find_next('body').get_text()
            for t in topic:
                tb_tup = (t, body)
                doi_list.append(tb_tup)
    print('Done.')
```

```python
data = pd.DataFrame(doi_list)
data.columns = (['topic', 'body'])
data['body'] = data['body'].apply(cleanbody)
print('A total number of {0} items were retrieved. Articles with multiple classes are recorded multiple times.'.format(len(data)))
data.head()
```
A total number of 3625 items were retrieved. Articles with multiple classes are recorded multiple times. It can be seen that the stop words are removed

 index|topic|body|
 ---|---|---
0|trade|hous trade lawmak took first vote measur desig...
1|trade|soviet first deputi prime minist vsevolod mura...
2|crude|venezuela suppli ecuador yet undetermin amount...
3|trade|britain today call japan increas foreign impor...
4|oil|british minist said propos european communiti ...

The final output is saved as a pandas DataFrame. A total number of 3625 items were retrieved. Articles with multiple topics are recorded multiple times.

For convenience, we will save the data as a .txt file. 

```python
data.to_csv('training_test_data.txt', index=False)
data.loc[0:10].to_csv('top10.txt', index = False)
```

## TF-IDF transformation in `sklearn`

```python
from sklearn.feature_extraction.text import TfidfVectorizer, TfidfTransformer, CountVectorizer
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import train_test_split
from sklearn.naive_bayes import MultinomialNB
from nltk.tokenize import RegexpTokenizer

data = pd.read_csv('training_test_data.txt',index_col=None)

body_list = list(data['body'])
start = time.clock()
vectorizer = TfidfVectorizer()
vectorizer.fit(data['body'])
print ("sklearn TFIDF processing time: {0:.5f} s".format(time.clock() - start))
```

sklearn TFIDF processing time: 0.37128 s

## TF-IDF transformation in `pyspark`
```python
from pyspark.sql import SparkSession
from pyspark.sql import Column
from pyspark.sql.types import *
from pyspark.sql.functions import udf
from pyspark.ml.feature import HashingTF, IDF, OneHotEncoder, StringIndexer
from pyspark.ml.classification import NaiveBayes

path = getcwd()
chdir(path)

spark = SparkSession\
        .builder\
        .appName("NewsClassification")\
        .getOrCreate()

df = spark.read.csv("training_test_data.txt",header=True,inferSchema=True)
```

Below is a udf (user defined function) that split the body text. The lines directly beneath applies the funciton to the body column meanwhile change the type to ArrayType.

```python
def text_split(text):
    """
    user-defined funtion to split the text
    """
    text = text.split()
    return text

clean_udf = udf(text_split, ArrayType(StringType()))
df = df.withColumn("body", clean_udf("body"))
```

```python
#following section transforms the text using TFIDF
start = time.clock()
hashingTF = HashingTF(inputCol="body", outputCol="term_freq")
df = hashingTF.transform(df)
idf = IDF(inputCol="term_freq", outputCol="tfidf")
idfModel = idf.fit(df)
df = idfModel.transform(df)
print ("pyspark TFIDF processing time: {0:.5f} s".format(time.clock() - start))
```

pyspark TFIDF processing time: 0.01982 s

## Building a Naive Bayes Classifier

The first step is to convert the topics (nominal) to a list of discrete integers.

```python
#Using the OneHotEncoder to convert the topics into discrete integers
stringIndexer = StringIndexer(inputCol="topic", outputCol="topicIndex")
model = stringIndexer.fit(df)
indexed = model.transform(df)
```

The entire dataset will be split 3 ways into the training/test/cross-evaluation set, and 3 different split proportions (50/40/10, 60/30/10, and 70/20/10) were used. The Naive Bayes classifier was trained, and for each split condition our model will train 10 times to evaluate the sensitivity of the model.

A total number of 30 models will be trained, and their parameters and accuracy are stored as key-value pairs in a dictionary.

```python
val_dict = dict()
train_test_cv_split_params = {'50/40/10': [0.5, 0.4, 0.1],
                               '60/30/10': [0.6, 0.3, 0.1], 
                               '70/20/10': [0.7, 0.2, 0.1]}

for split_param in train_test_cv_split_params.keys(): #run the model for each train/test/cv split
    for seed in np.arange(10): #run each model 10 times using different random seed
        train,test,cv = indexed.select("tfidf","topicIndex").randomSplit(train_test_cv_split_params[split_param],seed=seed)

        #Naive bayes
        nb = NaiveBayes(featuresCol="tfidf", labelCol="topicIndex", predictionCol="NB_pred",
                        probabilityCol="NB_prob", rawPredictionCol="NB_rawPred")
        nbModel = nb.fit(train)
        cv = nbModel.transform(cv)
        total = cv.count()
        correct = cv.where(test['topicIndex'] == cv['NB_pred']).count()
        accuracy = correct/total
        val_dict[(split_param, seed)] = accuracy
```

```python
params = max(val_dict, key = val_dict.get)
print("The combination of parameters that produced the highest accuracy ({0:.2f}): train/test/cv split ratio: {1}, randomseed: {2}".format(max(val_dict.values()),params[0], params[1]))
```

The combination of parameters that produced the highest accuracy (0.52): train/test/cv split ratio: 70/20/10, randomseed: 5

```python
def meancal(val_dict, split_param):
    l = list()
    for i in val_dict.keys():
        if i[0] == split_param:
            l.append(val_dict[i])
    lmean = np.mean(l)
    lstd = np.std(l)
    return (lmean, lstd)
```
```python
print('The mean accuracy of the 30 models: {0:.3f}'.format(np.mean(list(val_dict.values()))))

for split_param in train_test_cv_split_params:
    mean_accuracy, std_accuracy = meancal(val_dict, split_param)
    print('The split condition {0} has a mean accuracy of {1:.3f}'.format(split_param, mean_accuracy))
    print('The st.d. of split condition {0} for 10 runs: {1:.3f}'.format(split_param, std_accuracy))
```

The mean accuracy of the 30 models: 0.491
The split condition 50/40/10 has a mean accuracy of 0.481
The st.d. of split condition 50/40/10 for 10 runs: 0.013
The split condition 60/30/10 has a mean accuracy of 0.495
The st.d. of split condition 60/30/10 for 10 runs: 0.013
The split condition 70/20/10 has a mean accuracy of 0.498
The st.d. of split condition 70/20/10 for 10 runs: 0.012

## Conclusion

Generally, the accuracy of the model increases as we have a higher proportion of training data. In our case the highest performing model was produced with a split of 70/20/10 with a mean accuracy of 0.498. The 70/20/10 split also produces the lowest st.d., therefore it is less sensitive to the split of the data compared to the other two. It is possible that if we increase the size of the training data, we will increase the model accuracy.

For this project, I built a simple Naive Bayes classifier to classify articles based on the topics. As the result suggests, when using the highest percent of data for training, the model produced the highest accuracy. A possible future development might be to gather more data to see if the accuracy improves.

Only a limited number of topics were used for this assignment. It would be interesting to see what the model performance will be when more topics are used. Also, we only used single topic for each article entry, without considering multi-labeled articles.

Last, I trained the model based on the TFIDF result created using the cleaned text. I did not consider the combination of multiple words and their influence to the model. `Ngram` provides such capability, and it would be interesting to see if the model improves.
