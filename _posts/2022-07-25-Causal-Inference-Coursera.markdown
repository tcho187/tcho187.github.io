---
layout: post
title: Crash Course in Causality
tags: [frontpage, jekyll, blog]
image: '/images/posts/7.jpg'
---

Fundamental Problems of Causal Inference

We can only observe one potential outcome for each person

However with certain assumptions, we can estimate population level (average) causal effects

Hopeless: What would have happened to me had I not taken ibuprofen? (Unit level causal effect)

Possible: What would the rate of headache remission be if everyone took ibuprofen when they had a headache
versus if no one did?


# Confounders
Confounders are often defined as variables that affect treatment
and affect the outcome.

Confounder Control
We are interested in
1. Identifying a set of variables X that will make ignorability assumption hold.
2. Use statistical methods to control

Causal graphs

aka DAGs
helpful for identifying which variables to control for
make assumptions explicit