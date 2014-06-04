---
layout: post
title: "A Gentle Gradient Descent"
date: 2014-05-27 22:53:07 -0700
comments: true
categories: gradient descent linear regression machine learning statistics optimization
---

During our third week at Zipfian, we implemented the linear regression model by using various Python libraries (e.g. `statsmodel` and `scikit-learn`). We also coded the normal equation ($$\beta = (X^TX)^{-1}X^TY$$) directly as a Python function. In addition to using standard library functions to perform linear regressions, we also implemented an optimization technique called **[gradient descent](http://en.wikipedia.org/wiki/Gradient_descent)** to approximate the analytical solution for deriving the coefficients of a linear regression. When a matrix is non-invertible, one would have to use gradient descent to arrive at the coefficients of a linear regression since a [unique solution does not exist](http://stats.stackexchange.com/questions/69442/linear-regression-and-non-invertibility) in this case. In fact, gradient descent is a general optimization technique for finding the local minimum of a function and as such, can be applied to many other machine learning situations where an analytical solution is either too cumbersome or is infeasible. So I thought it'd be quite useful to get a better handle on this important tool.

Here's the general gradient descent algorithm:

$$
\begin{align*}
  \theta_{j+1} = \theta_j - \alpha \frac{\partial}{\partial\theta_j} J(\theta)
\end{align*}
$$

where $$J(\theta)$$ is a function that we want to minimize and $$\alpha$$ is the *learning rate*.

<!-- more -->

There is a [video](https://class.coursera.org/ml-003/lecture/10) by Andrew Ng of Stanford on the basic intuition behind gradient descent. 

> The general idea of gradient descent is that we take the gradient (i.e. slope or first-derivation) of the function we want to minimize at some starting point. Then we take one step in the **negative direction** of the gradient (hence, a descent) and repeat this process many times. Eventually, the algorithm will converge to a point where the gradient is zero and where the function is, therefore, at a local minimum. The $$\alpha$$ (or the learning rate) of the gradient descent algorithm determines how big a step we take at each iteration.

For the case of linear regression, the function we want to minimize is the RSS (or the cost function):

$$
\begin{align*}
  RSS = \displaystyle \sum_{i=1}^{n} (y_i - f(x_i))^2
\end{align*}
$$

By plugging the RSS (as $$J(\theta)$$) into the general gradient descent algorithm and doing some math (as explained on pages 4 and 5 of Andrew Ng's lecture notes [here](http://cs229.stanford.edu/notes/cs229-notes1.pdf)), we arrive at the following:

$$
\begin{align*}
  \theta_{j+1} = \theta_j - \alpha \frac{1}{m} \displaystyle \sum_{i=1}^{m}(h_{\theta}(x^{(i)}) - y^{(i)}))x_j^{(i)}
\end{align*}
$$

where $$h_{\theta}(x^{(i)})$$ is the predicted value ($$\hat{y}$$) of $$x^{(i)}$$ and $$y^{(i)}$$ is the true $$y$$ value.

Once we have tailored the gradient descent algorithm for linear regression, the Python implementation of the coefficient `update` function is not too difficult. We just need to make sure that the matrix multiplications are done properly:

```python
def update(self, X, y, alpha):
  gradient = X.T.dot(X.dot(self.coeffs)-y)
  m = len(y)
  return self.coeffs - alpha * gradient / m
```

Next, we create a `run` function to iterate through the above gradient descent `update` function in order to arrive at a set of coefficient vector that would minimize the RSS for the linear regression. To do this, we need to pass in $$\alpha$$ to control how *large* a step we'd want to take from one iteration to the next:

```python
def run(self, X, y, alpha=0.01, num_iterations=10000):
  for i in xrange(num_iterations):
    self.coeffs = self.update(X,y,alpha)
```

Finally, we can use this set of coefficients vector for our linear regression ($$\hat{y} = \beta X$$):

```python
def predict(self, X):
  return X.dot(self.coeffs)
```


