---
layout: post
title: KL Divergence
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pixabay-268533.jpg'
---
Often in statistics and machine learning, complex distributions of the truth are simplified with easier to work with distributions. The benefits of simplification introduce the loss of information that we would've had had we kept the complex distribution. Quantifying that loss is what KL divergence aims to do. 

KL divergence has its roots from the field of information theory. I have to explain entropy before I can explain KL divergence. Entropy is what aims to quantify the information. The defintion of entropy for a probability distribution is 

![entropy](/images/posts/entropy.svg)

We sum all x's which are states that one could belong to and the product of their probability of the state and the log of the probability of the state. I'll walk through an example to explain why we multiply the probability and the log of its probability. It's the example from linear digressions and I really like it. Imagine we have the income information between Brazil and Sweden. If we plot the distribution of both incomes, let's assume that Sweden's distribution is very equal, aka everybody in Sweden roughly makes the same amount of money. Then let's assume that Brazil's distribution has fat tails. So, 80% of the population makes very little money and the 1% makes alot of money. If we have this random person, and we want to know where he's most likely to end up. In the case of Brazil, we could just place him in the poorest bucket, and we'd be right 80% of the time. In the case of Sweden, we really have no idea where he belongs. With more information, we could use a binary search and search down the space until we place him in a bucket. In the case of computers, this information that we need to place him in a bucket is represented by bits and log of 2 of the equation captures the bits needed to encode this information. Therefore the product captures the balance between the probability of the space and the bits needed to encode the information. 


OK

So, that's entropy. I didn't even get into what KL divergence is. At this point, we have a way of quantifying the probability distribution. KL divergence is the difference between two probability distributions. Rather, it's how much information is lost if we choose distribution b as a proxy for distribution a. 

![kl divergence](/images/posts/kl_dvergence.svg)


Log of (P/Q) comes from the log property log(P) - log(Q).

How do we use this in machine learning?

The idea of a model is to find a function estimation that best describes our data. If we think of distribution of our data, it's complex and overfit if we decide to use our model to predict. We can compare the distribution of the data to the distribution of the model and minimize it to find the best fit model.

Let P be the data generating process ie. the distribution of the data and Q be the model density ie. the distribution of the model. We can rewrite KL divergence. 

![kl divergence](/images/posts/kl_2.svg)

The first term is the cross entropy and the second term is entropy of the data. Therefore, H(P) entropy of the data is how much information I get from the data. We aim to minimize the loss of the cross entropy of the data and the model. Note that the second term doesn't depend on the model, and therefore you can't modify it. So, minimizing either cross entropy or KL divergence as a loss function is equivalent. 


https://stats.stackexchange.com/questions/476170/cross-entropy-vs-kl-divergence-whats-minimized-directly-in-practice?noredirect=1&lq=1
https://www.countbayesie.com/blog/2017/5/9/kullback-leibler-divergence-explained



