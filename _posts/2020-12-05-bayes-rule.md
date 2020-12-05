---
layout: post
title: Baye's Rule
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pineapple-supply-co-139259.jpg'
---

Many machine learning models rely on Baye's rule. It's a formula for calculating the condtional probability ie. the probability of an event if a second event has occured. The crux of the theorem provides a way to update the probability of an event given new evidence.

![bayes](/images/posts/bayes.png)

If we look at the right hand side of the equation. P(A) is called the prior distribution of event A. In other words, what is the probability of event A in general. This is the base probability of event A that we want to start off with. Then, we add new data, and that will change the probability. The update or otherwise called likelihood is P(B|A). Then finally, the updated probability is divided by P(B) or otherwise called the evidence. It's a normalizing effect of the new data. It's normally taken out when using Baye's rules to task such as parameter estimation. So, we can also rewrite it as a proportion:

![bayes_proportion](/images/posts/bayes_proportion.png)

It's easy to understand the derivation.

![joint_derivation](/images/posts/joint_derivation.png)

Let's say we have two indepedent and mutually exclusive events A and B.

P(A) is the probabity of event A.

P(B) is the probability of event B.

P(x=A, y=B) is the joint probability of picking both A and B.

In other words, The joint probability is the count of picking A in x and picking Y in y divided by the total count.

And I'm going to add a trick here. I'm adding the count of every event A in both the numerator and denominator. It won't change the joint probability since this new expression is basically a 1 constant. Shuffle the top around, and I have P(A) and the conditional probability of P(B|A). Likewise, I can compute the joint probability of P(B, A).

![bayes_derivation](/images/posts/bayes_derivation.png)

Then, the joint probabilities are equal because A and B are mutually independent, so if I combine the two equations. Voila, I get the Baye's rule. 



---