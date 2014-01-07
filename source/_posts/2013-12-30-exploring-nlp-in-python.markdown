---
layout: post
title: "Exploring NLP in Python"
date: 2013-12-30 09:46:50 +0800
comments: true
categories: NLP Python "Vector Space Model" "Bag of Words Model"
---
Came across a [great tutorial](http://blog.scripted.com/scripted-updates/nlp-hacking-in-python/) on the basics of natural language processing (NLP) and classification. 

The example in this tutorial uses a Python library called `gensim` which (according to its [website](http://radimrehurek.com/gensim/about.html)) is the "the most robust, efficient and hassle-free piece of software to realize unsupervised semantic modelling from plain text." As far as I understand it, it's a very handy and commonly-used library for NLP.

One important tool/algorithm used for classification is something called the [Vector Space Model](http://en.wikipedia.org/wiki/Vector_space_model). Basically, all documents or queries are represented as "vectors of identifiers", such as an index of words and use the angle (theta) between vectors as a similarity measure (explained further later). Incidentally, if you ranked the similarity of each document to a query, you'd have a search engine.

<!-- more -->

A common way to represent a written document as a vector is to think about it in terms of [Bag of Words](http://en.wikipedia.org/wiki/Bag-of-words_model) (BoW) vectors. For example, if an entire document consists only of the words "dog" and "cat", then the BoW vector for this document would be [# of 'dog', # of 'cat'].

A very high-level overview of the workflow for NLP using the Vector Space Model is as follows:

> Preprocessing -> Create "Bag of Words" vector -> Dimensionality Reduction -> use SVM algorithm for classification

A more detailed workflow is as follows:

> Remove stopwords and split on spaces -> Take out rare terms using `gensim` -> Create Bag of Words vectors -> Dimensionality reduction using LSI ([Latent Semantic Indexing](http://en.wikipedia.org/wiki/Latent_semantic_indexing)) model in `gensim` (topic vectorization) -> unit vectorization -> finding cosine distance

Several important concepts to be aware of from this tutorial:

1. **Vector Space Model**
1. **[The Curse of Dimensionality](http://en.wikipedia.org/wiki/Curse_of_dimensionality)**
  - "There are all kinds of terrible things that happen as the dimensionality of your descriptor vectors rises. One obvious one is that as the dimensionality rises, both the time and space complexity of dealing with these vectors rises, often exponentially. Another issue is that as dimensionality rises, the amount of samples needed to draw useful conclusions from that data also rises steeply. Another way of phrasing that is with a fixed number of samples, the usefulness of each dimension diminishes. Finally, as the dimensionality rises, your points all tend to start becoming equidistant to each other, making it difficult to draw solid conclusions from them."
1. **Similarity in Vector Space**
  + Euclidean distance (this ignores direction)
  - Cosine distance - measuring similarity based on angle between vectors is know as cosine distance, or cosine similarity. 
  *Unit vectorization - modify the vectors themselves by dividing each number in each vector by that vector's magnitude. In doing so, all our vectors have a magnitude of 1. This process is called unit vectorization because the output vectors are units vectors.

4. **Supervised Learning**
  - Train the algorithm on samples which have the 'correct' answer provided with them. The specific supervised learning problem we're addressing here is called classification. You train an algorithm on labelled descriptor vectors, then ask it to label a previously unseen descriptor vector based on conclusions drawn from the training set.

5. **[Support Vector Machine](http://en.wikipedia.org/wiki/Support_vector_machine)**
  + SVM is a family of algorithms which define decision boundaries between classes based on labelled training data.
  
  > For our 'dog' vs. 'sandwich' classification problem, we provide the algorithm with some training samples. These samples are documents which have gone through our whole process (BoW vector -> topic vector -> unit vector) and carry with them either a 'dog' label or a 'sandwich' label. As you provide the SVM model with these samples, it looks at these points in space and essentially draws a line between the 'sandwich' documents and the 'dog' documents. This border between "dog"-land and "sandwich"-land is known as a decision boundary. Whichever side of the line the query point falls on determines what the algorithm labels it.
  
  > All samples in both training and test sets are labeled. However, in practice, you would build the model on the labeled training set, ignore the labels on the test set, feed them into the model, have the model guess what those labels are, and finally check whether or not the algorithm guessed correctly. This process of testing out your supervised learning algorithm with a training and test set is called cross-validation.


```python
import warnings
warnings.filterwarnings('ignore', category=DeprecationWarning)
from math import sqrt
import gensim
from sklearn.svm import SVC
import os


def vec2dense(vec, num_terms):

    '''Convert from sparse gensim format to dense list of numbers'''
    return list(gensim.matutils.corpus2dense([vec], num_terms=num_terms).T[0])

if __name__ == '__main__':

    #Load in corpus, remove newlines, make strings lower-case
    docs = {}
    corpus_dir = 'corpus'
    for filename in os.listdir(corpus_dir):

        path = os.path.join(corpus_dir, filename)
        doc = open(path).read().strip().lower()
        docs[filename] = doc

    names = docs.keys()

    #Remove stopwords and split on spaces
    print "\n---Corpus with Stopwords Removed---"

    stop = ['the', 'of', 'a', 'at', 'is']
    preprocessed_docs = {}
    for name in names:

        text = docs[name].split()
        preprocessed = [word for word in text if word not in stop]
        preprocessed_docs[name] = preprocessed
        print name, ":", preprocessed

    #Build the dictionary and filter out rare terms
    dct = gensim.corpora.Dictionary(preprocessed_docs.values())
    unfiltered = dct.token2id.keys()
    dct.filter_extremes(no_below=2)
    filtered = dct.token2id.keys()
    filtered_out = set(unfiltered) - set(filtered)

    print "\nThe following super common/rare words were filtered out..."
    print list(filtered_out), '\n'

    print "Vocabulary after filtering..."
    print dct.token2id.keys(), '\n'

    #Build Bag of Words Vectors out of preprocessed corpus
    print "---Bag of Words Corpus---"

    bow_docs = {}
    for name in names:

        sparse = dct.doc2bow(preprocessed_docs[name])
        bow_docs[name] = sparse
        dense = vec2dense(sparse, num_terms=len(dct))
        print name, ":", dense

    #Dimensionality reduction using LSI. Go from 6D to 2D.
    print "\n---LSI Model---"

    lsi_docs = {}
    num_topics = 2
    lsi_model = gensim.models.LsiModel(bow_docs.values(),
                                       num_topics=num_topics)
    for name in names:

        vec = bow_docs[name]
        sparse = lsi_model[vec]
        dense = vec2dense(sparse, num_topics)
        lsi_docs[name] = sparse
        print name, ':', dense

    #Normalize LSI vectors by setting each vector to unit length
    print "\n---Unit Vectorization---"

    unit_vecs = {}
    for name in names:

        vec = vec2dense(lsi_docs[name], num_topics)
        norm = sqrt(sum(num ** 2 for num in vec))
        unit_vec = [num / norm for num in vec]
        unit_vecs[name] = unit_vec
        print name, ':', unit_vec

    #Take cosine distances between docs and show best matches
    print "\n---Document Similarities---"

    index = gensim.similarities.MatrixSimilarity(lsi_docs.values())
    for i, name in enumerate(names):

        vec = lsi_docs[name]
        sims = index[vec]
        sims = sorted(enumerate(sims), key=lambda item: -item[1])

        #Similarities are a list of tuples of the form (doc #, score)
        #In order to extract the doc # we take first value in the tuple
        #Doc # is stored in tuple as numpy format, must cast to int

        if int(sims[0][0]) != i:
            match = int(sims[0][0])
        else:
            match = int(sims[1][0])

        match = names[match]
        print name, "is most similar to...", match

    #We add classes to the mix by labelling dog1.txt and sandwich1.txt
    #We use these as our training set, and test on all documents.
    print "\n---Classification---"

    dog1 = unit_vecs['dog1.txt']
    sandwich1 = unit_vecs['sandwich1.txt']

    train = [dog1, sandwich1]

    # The label '1' represents the 'dog' category
    # The label '2' represents the 'sandwich' category

    label_to_name = dict([(1, 'dogs'), (2, 'sandwiches')])
    labels = [1, 2]
    classifier = SVC()
    classifier.fit(train, labels)

    for name in names:

        vec = unit_vecs[name]
        label = classifier.predict([vec])[0]
        cls = label_to_name[label]
        print name, 'is a document about', cls

    print '\n'
```