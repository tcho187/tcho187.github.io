---
layout: post
title: OLS regression assumptions
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pineapple-supply-co-139259.jpg'
---

### Assumption 1

> The number of observations must be greater than the number of features

We'll see how p >> n becomes a problem in the closed form solution.

Recall that we can solve for the beta parameters using the OLS estimator:

![estimator](/images/posts/ols/estimator.png)

Suppose, I want to fit a regression y on x1 and x2, i.e.

If I fit on two training points X1=[0,2] y1=0 and X2=[1,1] y2=1, i.e. n = 2 and p = 3,

then solving the estimator gets us

![estimator](/images/posts/ols/matrix.png)

The determinate is 0 and thus the matrix is not invertible. Another way to go about this is setting up a system of equations.


We can see that the optimal solutions are a line and not one point. Therefore any values on this line are "optimal", and is sensitive to whatever noise we add to the equation.

![estimator](/images/posts/ols/system.png)


### Assumption 2

> The mean of the residuals is 0 (or very close to 0)

When the mean of the residuals is 0, we can prove that the OLS estimator is unbiased. That is, the expected value of the estimated beta parameters using the OLS estimator is the beta parameters of the model.

![mean_residual](/images/posts/ols/mean_residual.png)


### Assumption 3

> Homoscedasticity of residuals or equal variance

When the variance of the residual is not consistent i.e. a horizontal line on a fitted values vs. residuals plot, we will lose confidence in our inference. The covariance matrix is used to calculate predictions so our prediction intervals will be biased.

When the variance is constant, then we can see that the expected value of the variance of the residuals will equal to a constant standard error. Thus we have unbiased and consistent estimates of the variance to use for inference.

![variance_residuals](/images/posts/ols/var_residual.png)


### Assumption 4

> No autocorrelation

Autocorrelation and heteroscedasticity often go together because they affect the consistency of the variance of beta parameter estimates. In the case of autocorrelation, we go back to the expected value of residual x residual transpose. We multiply a correlation coefficient to the standard error, and since the coefficient is a fraction between -1 and 1 (positive really since we rarely see negative correlation), the off-diagonal of the correlation coefficient times the standard error skews the covariance matrix. Thus, we will underestimate the standard error.

![auto_residual](/images/posts/ols/auto_residual.png)