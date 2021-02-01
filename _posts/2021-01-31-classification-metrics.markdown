---
layout: post
title: Metrics
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pineapple-supply-co-139259.jpg'
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

# Recall

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


