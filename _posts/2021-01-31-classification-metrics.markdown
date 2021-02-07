---
layout: post
title: Metrics
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-brett-sayles.jpg'
---

# Accuracy

Accuracy = (TP + TN)/(TP+TN+FP+FN)

When to use?
Accuracy is a valid choice of evaluation for classification problems which are well balanced and not skewed or No class imbalance.

# Precision

what proportion of predicted Positives is truly Positive?

Precision = TP/(TP+FP)

When to use?
Precision is a valid choice of evaluation metric when we want to be very sure of our prediction. 

# Recall (True Positive Rate/ Sensitivity/ Hit Rate)

what proportion of actual Positives is correctly classified?

Recall = TP/(TP+FN)

Recall is a valid choice of evaluation metric when we want to capture as many positives as possible.

# F1 Score

The F1 score is a number between 0 and 1 and is the harmonic mean of precision and recall.

F1 Score = 2 * (precision * recall)/(precision+recall)

When to use?
We want to have a model with both good precision and recall. If your precision is low, the F1 is low and if the recall is low again your F1 score is low.

# Weighted F1 Score
F1 Score = (1+B^2) * (Precision * recall)/((B^2*precision) + recall)



# Cost-sensitive classification

## ROC curves
Plot metric performance against thresholds.

TPR (Recall) = TP/(TP+FN)

FPR = FP/(FP+TN)

TPR vs FPR as an implicit function of the threshold. Aka Receiver Operating Characteristic or ROC curve.


## Cross entropy 

h(p,q) = - sum(for x in X) [p(x)*log(q)]


When to Use?
When the output of a classifier is prediction probabilities. Log Loss takes into account the uncertainty of your prediction based on how much it varies from the actual label. This gives us a more nuanced view of the performance of our model. In general, minimizing Log Loss gives greater accuracy for the classifier.

I always use (test) cross-entropy under cross-validation to assess the performance of a classification model. It's far more robust than accuracy on small datasets (because accuracy isn't "smooth"), and far more meaningful than accuracy (although perhaps not than precision and recall) when classes are imbalanced.

However, the problem with cross-entropy, is that it doesn't live on any objective scale, it's a very relative metric. You can compare the performance of XGBoost Vs a Neural Network on a given data set and the one with a lower cross-entropy (or higher test log-likelihood) is the better model. Saying that "XGBoost gets a cross-entropy of X on problem A and a cross-entropy of Y on problem B" is harder to interpret.
log loss = -(ylog(p) - -(1-y)log(1-p))


## AUC

area under curve of ROC curve


![fpr vs tpr](/images/posts/fpr_tpr.jpeg)

When to Use?
AUC is scale-invariant. It measures how well predictions are ranked, rather than their absolute values. So, for example, if you as a marketer want to find a list of users who will respond to a marketing campaign. AUC is a good metric to use since the predictions ranked by probability is the order in which you will create a list of users to send the marketing campaign.
Another benefit of using AUC is that it is classification-threshold-invariant like log loss. It measures the quality of the model’s predictions irrespective of what classification threshold is chosen, unlike F1 score or accuracy which depend on the choice of threshold.