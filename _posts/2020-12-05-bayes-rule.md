---
layout: post
title: Baye's Rule
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pineapple-supply-co-139259.jpg'
---

Many machine learning models rely on Baye's rule. It's a formula for calculating the conditional probability ie. the probability of an event if a second event has occurred. The crux of the theorem provides a way to update the probability of an event given new evidence.

![bayes](/images/posts/bayes.png)

If we look at the right hand side of the equation. P(A) is called the prior distribution of event A. In other words, what is the probability of event A in general. This is the base probability of event A that we want to start off with. Then, we add new data, and that will change the probability. The update or otherwise called likelihood is P(B\|A). Then finally, the updated probability is divided by P(B) or otherwise called the evidence. It's a normalizing effect of the new data. It's normally taken out when using Baye's rules to task such as parameter estimation. So, we can also rewrite it as a proportion:

![bayes_proportion](/images/posts/bayes_proportion.png)

It's easy to understand the derivation.

![joint_derivation](/images/posts/joint_derivation.png)

Let's say we have two independent and mutually exclusive events A and B.

P(A) is the probability of event A.

P(B) is the probability of event B.

P(x=A, y=B) is the joint probability of picking both A and B.

In other words, The joint probability is the count of picking A in x and picking B in y divided by the total count.

And I'm going to add a trick here. I'm adding the count of every event A in both the numerator and denominator. It won't change the joint probability since this new expression is basically a 1 constant. Shuffle the top around, and I have P(A) and the conditional probability of P(B\|A). Likewise, I can compute the joint probability of P(B, A).

![bayes_derivation](/images/posts/bayes_derivation.png)

Then, the joint probabilities are equal because A and B are mutually independent, so if I combine the two equations. Voila, I get the Baye's rule.



---


## Naive Bayes from scratch


Let's try to code na naive bayes classifier from scratch in Python.

Let's first start with a Multinomial Naive Bayes.

Let's first create the Multinomial class.

```
class MultinomialNB(object):
  def __init__(self, alpha=1.0):
    self.alpha=alpha

```


We need to create the fit method, which will create our
prior and likelihood.

The general framework is:
1. Get the number of samples
2. Create an array of arrays with records in each class.
3. Create an array of priors. Length of the array in 2 is divided by 1
4. Create an array of likelihood numerator. Take the sum of each word.
5. Create an array of likelihood denominator. Take the sum of the count in each row.
6. Create an array of likelihood. Take 4 divided by 5.


```
def fit(X,y):
  count_samples = X.shape[0]
  records_by_class = [[x for x,label in zip(X,y) if label == target] for target in np.unique(y)]
  self.priors_ = [np.log(len(records)/count_samples) for records in records_by_class]
  word_sum = np.array([np.array(records).sum(axis=0) for records in records_by_class]) + self.alpha
  count_sum = count.sum(axis=1)
  self.likelihood_ = np.log(word_sum/count_sum[np.newaxis].T)
```


Now, let's predict. The prediction is straightforward.
The general framework
1. Multiply the record with the likelihood and add the prior

```
def predict_log_proba(self,X):
  return [(self.likelihood_ * x).sum(axis=1) + self.prior_ for x in X]
```

We can also just output the highest probability.

```
def predict(self,X):
  return np.argmax(self.predict_log_proba(X), axis=1)
```

Here's the complete code.

```
class MultinomialNB(object):
  def __init__(self, alpha=1.0):
    self.alpha=alpha

def fit(X,y):
  count_samples = X.shape[0]
  records_by_class = [[x for x,label in zip(X,y) if label == target] for target in np.unique(y)]
  self.priors_ = [np.log(len(records)/count_samples) for records in records_by_class]
  word_sum = np.array([np.array(records).sum(axis=0) for records in records_by_class]) + self.alpha
  count_sum = count.sum(axis=1)
  self.likelihood_ = np.log(word_sum/count_sum[np.newaxis].T)


def predict_log_proba(self,X):
  return [(self.likelihood_ * x).sum(axis=1) + self.prior_ for x in X]

def predict(self,X):
  return np.argmax(self.predict_log_proba(X), axis=1)

```



### Bernoulli NB

It's identical to multinomial except rather than the count of words
we look at the existence of the word.

```
class BernoulliNB(object):
	def __init__(self, alpha):
		self.alpha = alpha

	def fit(self,X):
		count_samples = X.shape[0]
		records_by_class = [[x for x,label in zip(X,y) if label == target] for target in np.unique(y)]
		self.class_log_prior_ = [np.log(len(records)/count_samples) for records in records_by_class]
		count = np.array([np.array(i).sum(axis=0) for i in records_by_class]) + self.alpha
		smoothing = 2 * self.alpha
		n_doc = np.array([len(i) + smoothing for i in records_by_class])
		self.feature_prob_ = count / n_doc[np.newaxis].T
		return self

	def predict_log_proba(self, X):
		return [(np.log(self.feature_prob_) * x + np.log(1 - self.feature_prob_) * np.abs(x - 1)).sum(axis=1) + self.class_log_prior_ for x in X]

```


Posterior is likelihood * x + (1-likelihood) * (x-1)

so the posterior is likelihood times the x-record + 1-likelihood times absolute value of x-1. so
if the word exists then x-1 is 0 so 1-likelihood is 0.


We should also include the binarize function
```
class BernoulliNB(object):
	def __init__(self, alpha=1.0, binarize=0.0):
		self.alpha = alpha
		self.binarize = binarize

	def fit(self,X):
		X = self._binarize_X(X)
		count_samples = X.shape[0]
		records_by_class = [[x for x,label in zip(X,y) if label == target] for target in np.unique(y)]
		self.class_log_prior_ = [np.log(len(records)/count_samples) for records in records_by_class]
		count = np.array([np.array(i).sum(axis=0) for i in records_by_class]) + self.alpha
		smoothing = 2 * self.alpha
		n_doc = np.array([len(i) + smoothing for i in records_by_class])
		self.feature_prob_ = count / n_doc[np.newaxis].T
		return self

	def predict_log_proba(self, X):
		X = self._binarize_X(X)
		return [(np.log(self.feature_prob_) * x + np.log(1 - self.feature_prob_) * np.abs(x - 1)).sum(axis=1) + self.class_log_prior_ for x in X]
```


If the features are continuous and follow a normal distribution, the
GaussianNB is appropriate.


```
class GaussianNB(object):
	def __init__(self):
		pass

	def fit(self, X,y):
		records_by_class = [[x for x, label in zip(x,y) if label == target] for target in np.unique(y)]
		self.model = np.array([np.c_(np.mean(records),axis=0),np.std(records, axis=0)] for records in records_by_class)
		return self
	def _prob(self, x, mean, std):
		exponent = np.exp(- ((x-mean)**2 / (2* std**2)))
		return np.log(exponent / (np.sqrt(2*np.pi) * std))

	def predict_log_proba_(self, X):
		return [[sum(self._prob(i, *s) for s,i in zip(statistics, x)) for statistics in self.model] for x in X]

	def predict(self, X):
		return np.argmax(self.predict_log_proba_(X), axis=1)

	def score(self, X, y):
		return sum(self.predict(X)==y) / len(y)

```

https://kenzotakahashi.github.io/naive-bayes-from-scratch-in-python.html
https://nlp.stanford.edu/IR-book/html/htmledition/naive-bayes-text-classification-1.html