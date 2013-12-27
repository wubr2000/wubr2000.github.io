---
layout: post
title: "Code Snippets Test"
date: 2013-12-27 16:28:32 -0500
comments: true
categories: 
---
Here's an example of a Ruby code:

``` ruby Discover if a number is prime http://www.noulakaz.net/weblog/2007/03/18/a-regular-expression-to-check-for-prime-numbers/ Source Article
class Fixnum
  def prime?
    ('1' * self) !~ /^1?$|^(11+?)\1+$/
  end
end
```

An example of Coffeescript:

``` coffeescript Coffeescript Tricks 
# Given an alphabet:
alphabet = 'abcdefghijklmnopqrstuvwxyz'

# Iterate over part of the alphabet:
console.log letter for letter in alphabet[4..8]
```
<!-- more -->

Example of embedding Gist:

{% gist 4321346 gistfile1.diff %}

Inline code blocks example:

{% codeblock Javascript Array Syntax lang:js http://j.mp/pPUUmW MDN Documentation %}
var arr1 = new Array(arrayLength);
var arr2 = new Array(element0, element1, ..., elementN);
{% endcodeblock %}
