---
layout: post
title: "Exploring NLP in Python"
date: 2013-12-30 09:46:50 +0800
comments: true
categories: NLP, Python, Vector Space Model, Bag of Words Model
---
Came across a [great tutorial](http://blog.scripted.com/scripted-updates/nlp-hacking-in-python/) on the basics of natural language processing (NLP) and classification. 

The example in this tutorial uses a Python library called `gensim` which (according to its [website](http://radimrehurek.com/gensim/about.html)) is the "the most robust, efficient and hassle-free piece of software to realize unsupervised semantic modelling from plain text." As far as I understand it, it's a very handy and commonly-used library for NLP.

One important tool/algorithm used for classification is something called the [Vector Space Model](http://en.wikipedia.org/wiki/Vector_space_model). Basically, all documents or queries are represented as "vectors of identifiers", such as an index of words.

<!-- more -->

A common way to represent a written document as a vector is to think about it in terms of [Bag of Words](http://en.wikipedia.org/wiki/Bag-of-words_model) (BoW) vectors.

For example, if an entire document consists only of the words "dog" and "cat", then the BoW vector for this document would be [# of 'dog', # of 'cat'].

A very high-level overview of the workflow for NLP using the Vector Space Model is as follows:

> Preprocessing -> Create Bag of Words vector -> Dimensionality Reduction -> use SVM algorithm for classification

A more detailed workflow:

> Remove stopwords and split on spaces -> Take out rare terms using `gensim` -> Create Bag of Words vectors -> Dimensionality reduction using LSI ([Latent Semantic Indexing](http://en.wikipedia.org/wiki/Latent_semantic_indexing)) model in `gensim` -> 