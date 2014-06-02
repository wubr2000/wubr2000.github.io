---
layout: post
title: "Gradient Descent Tutorial"
date: 2014-05-27 22:53:07 -0700
comments: true
categories: gradient descent linear regression
---

During the third week of Zipfian, we implemented the linear regression model using different Python libraries (statsmodel and scikit-learn). While linear regression is one of the most well-known statistical analysis tool, we learned to use an optimization technique called gradient descent to approximate the analytical solution for linear regression. In fact, when a matrix is *non-invertible*, one would have to use gradient descent to approximate linear regression (since an analytical solution would not exist in this case).

In general, gradient descent updates the coefficients of the linear regression this way:

$$
\begin{align*}
  \theta_j := \theta_j - \alpha \frac{\partial}{\partial\theta_j} J(\theta)
\end{align*}
$$


<!-- more -->

