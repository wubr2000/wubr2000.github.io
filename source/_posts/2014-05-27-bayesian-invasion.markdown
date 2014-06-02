---
layout: post
title: "A Gentle Gradient Descent"
date: 2014-05-27 22:53:07 -0700
comments: true
categories: gradient descent linear regression
---

During our third week at Zipfian, we implemented the linear regression model by using various Python libraries (e.g. `statsmodel` and `scikit-learn`). We also coded the normal equation ($$\beta = (X^TX)^{-1}X^TY$$) directly as a Python function. In addition to performing linear regressions with functions provided by the built-in Python libraries, we also learned to use an optimization technique called **[gradient descent](http://en.wikipedia.org/wiki/Gradient_descent)** to approximate the analytical solution for deriving the coefficients of a linear regression. In fact, when a matrix is non-invertible, one would have to use gradient descent to arrive at the coefficients of a linear regression since a [unique solution does not exist](http://stats.stackexchange.com/questions/69442/linear-regression-and-non-invertibility) in this case. In fact, gradient descent is a general optimization technique for finding the local minimum of a function and as such, can be applied to many other situations where an analytical solution is either too cumbersome or infeasible. 

In general, gradient descent updates the coefficients of the linear regression by:

$$
\begin{align*}
  \theta_j := \theta_j - \alpha \frac{\partial}{\partial\theta_j} J(\theta)
\end{align*}
$$

where $$J$$ is the cost function (the RSS in the case of linear regressions) and $$\theta$$ is the coefficient vector.

<!-- more -->

