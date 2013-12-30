---
layout: post
title: "Solving the Monty Hall Problem using Monte Carlo Simulation"
date: 2013-12-30 11:25:09 +0800
comments: true
categories: 
---
This exercise is from [Harvard's CS109 course.](http://cs109.org/)

In the Monty Hall game show, contestants try to guess which of 3 closed doors contain a cash prize (goats are behind the other two doors). Of course, the odds of choosing the correct door are 1 in 3. As a twist, the host of the show occasionally opens a door after a contestant makes his or her choice. This door is always one of the two the contestant did not pick, and is also always one of the goat doors (note that it is always possible to do this, since there are two goat doors). At this point, the contestant has the option of keeping his or her original choice, or swtiching to the other unopened door. The question is: is there any benefit to switching doors? The answer surprises many people who haven't heard the question before.

We can answer the problem by running simulations in Python. 

<!-- more -->

First, write a function called `simulate_prizedoor`. Remember to include `Numpy` library by including at the top `import numpy as np`


```python
def simulate_prizedoor(nsim):
  return np.random.random_integers(0, high=2, size=nsim)
```

Next, write a function that simulates the contestant's guesses for `nsim` simulations. Call this function `simulate_guess`. This function should be similar to above but we're only testing so we'll just return a bunch of zeros for now:

```python
def simulate_guess(nsim):
  return np.zeros(nsim, dtype=np.int)
```

Next, write a function, `goat_door`, to simulate randomly revealing one of the goat doors that a contestant didn't pick.

```python
def goat_door(prizedoors, guesses):
    
    #strategy: generate random answers, and
    #keep updating until they satisfy the rule
    #that they aren't a prizedoor or a guess
    result = np.random.randint(0, 3, prizedoors.size)
    while True:
        bad = (result == prizedoors) | (result == guesses)
        if not bad.any():
            return result
        result[bad] = np.random.randint(0, 3, bad.sum())
        
# print goat_door(simulate_prizedoor(10), simulate_guess(10))
```

Write a function, `switch_guess`, that represents the strategy of always switching a guess after the goat door is opened. This function is identical in concept to `goat_door`

```python
def switch_guess(guesses, goatdoors):
    
    #strategy: generate random answers, and
    #keep updating until they satisfy the rule
    #that they aren't the original guess or a goat door
    result = np.random.randint(0, 3, guesses.size)
    while True:
        bad = (result == guesses) | (result == goatdoors)
        if not bad.any():
            return result
        result[bad] = np.random.randint(0, 3, bad.sum())

        
#prize = simulate_prizedoor(10)
#guess = simulate_guess(10)
#print guess
#print prize
#goat = goat_door(prize, guess)
#print goat
#switch = switch_guess(guess, goat)
#print switch
```

Last function: write a `win_percentage` function that takes an array of `guesses` and `prizedoors`, and returns the percent of correct guesses

```python
def win_percentage(guesses, prizedoors):
    return (guesses == prizedoors).mean()*100

# prize = simulate_prizedoor(10)
# guess = simulate_guess(10)
# print guess
# print prize
# win_percentage(guess, prize)
```

Putting it all together. Simulate 10000 games where contestant keeps his original guess, and 10000 games where the contestant switches his door after a  goat door is revealed. Compute the percentage of time the contestant wins under either strategy. Is one strategy better than the other?

```python
nsim = 10000

#keep guesses
print "Win percentage when keeping original door"
print win_percentage(simulate_prizedoor(nsim), simulate_guess(nsim))

#switch
pd = simulate_prizedoor(nsim)
guess = simulate_guess(nsim)
goats = goat_door(pd, guess)
guess = switch_guess(guess, goats)
print "Win percentage when switching doors"
print win_percentage(pd, guess).mean()
```

The result looks something like this:
```
Win percentage when keeping original door
32.95
Win percentage when switching doors
66.61
```

Voila! Always switch!

Additional work: One of the best ways to build intuition about why opening a Goat door affects the odds is to re-run the experiment with 100 doors and one prize. If the game show host opens 98 goat doors after you make your initial selection, would you want to keep your first pick or switch? Can you generalize your simulation code to handle the case of "n" doors?