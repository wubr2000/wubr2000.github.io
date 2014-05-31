---
layout: post
title: "Bayesian Invasion"
date: 2014-05-27 22:53:07 -0700
comments: true
categories: bayes bayesian maximum posteriori
---

> Methods can be abused and that is true with any approach, including Bayesian but it offers a more coherent, philosophical way in which to look at the world 
> - Nate Silver

Problems with MLE:

1.  MLE does poorly when the sample size is small. In particular, the ML estimator can be biased.

2.  If the coin is tossed once, and came up heads, ML estimation would estimate p = 1.

3.  Also, ML assumes uniform priors, which is often incorrect. We frequently have very specific information regarding the plausibility of various hypotheses (e.g., refer the simplest hypothesis etc).

We want to prove the following statement:

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$


To adress these problems, we turn to the framework of Bayesian estimation.

<!-- more -->

