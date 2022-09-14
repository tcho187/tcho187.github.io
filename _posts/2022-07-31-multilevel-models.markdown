---
layout: post
title: Multilevel structures
tags: [frontpage, jekyll, blog]
image: '/images/posts/7.jpg'
---


### 11.4 Indicator variables and fixed or random effects

In a multilevel model, it's unnecessary
to choose one of the categories as a baseline and
include indicators for the other J − 1 categories


Multilevel linear models: the basics

the constant term in the regression
is replaced by J separate intercept terms

these J coefficients are then themselves given a model
1. a common distribution for the J parameters αj
2. a regression model for the αj’s given group-level predictors


### Group-level predictors

y<sub>i</sub> ~ N(α<sub>j [i]</sub> + βx<sub>i</sub>, σ<sup>2</sup><sub>y</sub> ) for i ... N



From classical to multilevel regression

* Complete-pooling model: a single classical regression completely ignoring the group information—that is, a single model fit to all the data, perhaps including group-level predictors but with no coefficients for group indicators.
* No-pooling model: a single classical regression that includes group indicators (but no group-level predictors) but with no model for the group coefficients.
* Separate models: a separate classical regression in each group. This approach is not always possible if there are groups with small sample sizes. (For example, in Figure 12.4 on page 257, Aitkin County has three measurements in homes with basements and one in a home with no basement. If the sample from Aitkin County had happened to contain only houses with basements, then it would be impossible to estimate the slope β from this county alone.)
* Two-step analysis: starting with either the no-pooling or separate models, then fitting a classical group-level regression using, as “data,” the estimated coeffi- cients for each group.
