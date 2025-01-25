---
layout: post
title: Logg Loss aka Cross Entropy with sigmoid aka logistic or softmax
tags: [log probability, binary logistic regression, parameter learning, Negative Log Loss, binary cross entropy, multinomial logistic classification, softmax]
image: '/images/posts/pexels-brett-sayles.jpg'
---


# Log Probability


Two Problems
1. Multiplying probabilities makes numbers very small very fast and eventually floating point precision becomes a problem.
2. Sometimes probabilities are zero and multiplying zero against other probabilities make everything zero.

### Problem 1

Solution - shift to log scale.

In the log scale, from the values of 0 to 1 where are our probability values will lie, gets converted to values from 0 to negative infinity.
What's nice is that really really small numbers becomes negatives of big numbers.
So log(1) = 0 and log(0) = -infinity

We're trying to make values closer to 0.

Never have to worry about numbers zeroing out (though small probabilities can become very large)

Term - log probability

Remember in log scale the multiplication of two probabilities in log space becomes addition.

## Binary Logistic Regression

If we set y = {+1, -1}

Probabilistically: P(Y=+1 | X=x , theta = f(score(x; theta)))
f converts score into a probability distribution [0,1]
f = sigmoid function aka the logistic function

sigmoid function = 1 / 1+e^-x

![Logistic-curve.svg.png](/images/posts/Logistic-curve.svg.png)

it puts a lot of value in +1 or 0. not a lot of wiggle room in the middle.

sigmoid approximates a distribution that is either true (label = +1) or false (label not +1)

P(Y=-1 | X=x; theta) = 1-sigmoid(score(x;theta))
which is equal to sigmoid(-score(x;theta))

Generally
P(Y=y | X=x;theta) = sigmoid(y*score(x;theta))
where y is {+1,-1}


## Parameter Learning

The parameter learning will do the following
1. Change the probability space to log probability space. The product becomes a summation. Remember in this space we want probabilities that are high and so the values are closer to 0.
So predicting the label correctly means max score of zero
2. Flip log to negative log. The arg max becomes arg min. Now numbers go from infinity down to 0. So predicting the label correctly means
going from a very high number down to zero loss. We call this Negative Log Loss. Similar to Cross Entropy Loss.
![negative-log-loss.png](/images/posts/negative-log-loss.png)
3. Put it in terms of our score function which sigmoid(y*score(x;theta))
4. Think of log scale of sigmoid looks like. Log of sigmoid is negative log of the denominator. Remember division is subtraction so log(1) = 0 (the numerator) so it's 0 - log(denominator).
5. Plugging in negative log(denominator), then the negative before the log cancels out with the negative from log of sigmoid. And then the inside becomes log(1 + e^(-y*score(x;theta))). The image is wrong. It's -y.
6. Optional (vector)
7. This is the loss function.

![parameter-learning.png](/images/posts/parameter-learning.png)

### Binary Cross Entropy

In the context of neural network
Before we were sending the scores y-hat and target y; {+1,-1}  before sending it to sigmoid and then log.
Now we need to change the target to y={+1, 0} and that means we need to use binary cross entropy.

loss(y-hat, y) = y * log(p(y-hat)) + (1-y) * log(1-p(y-hat))

Basically, we're selecting which way we need to look at the outputs of our neural network. Whether to look at the probability
or flip flop and use 1-probability.

![binary-cross-entropy.png](/images/post/Fbinary-cross-entropy.png)

### Softmax

Previously we used sigmoid for one comparison.

We need softmax for more than 2 categories.

Softmax takes a vector of numbers and makes one close to 1 and the rest close to 0.
Pretend that softmax is a probability distribution.

1. Take a vector of scores
2. We exponential every number
3. We normalize. Take the sum of each e^number
4. Take the log. Remember we want numbers that are close to 1 because then log of number becomes close to 0

Exponentiation makes big numbers really big and small numbers really small

![softmax.png](/images/posts/softmax.png)


### Parameter Learning with softmax

TODO