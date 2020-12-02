---
layout: post
title: Normal Equation derivation
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/math.jpg'
---

It's been a couple years out of college, and I completely forgot about the normal equation to solve a multivariate linear regression. For small dataset problems, it's an elegant and clean solution.

Let's say we have m data points and n feature variables. So, the linear combination is defined as:

![ols](/images/posts/ols.png)

In matrix form, the linear combination is defined as:

![ols_matrix](/images/posts/ols_matrix.png)

where Beta is a vector of parameters and X is a matrix of feature variable inputs. 

![matrix_params](/images/posts/matrix_params.png)

We setup the cost function which is the residuals or the difference between the observed output and the model output and we square the residual to get a positive number.

Let's expand out the cost function.

![ols_expand](/images/posts/ols_expand.png)

In step 3, the square turns into a transpose since the residual is a vector, and a vector multiplied by a vector needs to be transposed.

In step 4, I carry the transpose over since the transpose of a sum (subtraction in this case) is equivalent to the transpose to the sum of their transposes.

In step 6, I carry the transpose over the middle term since the tranpose of a product is equivalent to the product of their transposes in reversed order.

In step 7, I combine the two middle terms since both turn out to have the same dimensions which is 1 x 1 matrix.


Then, we can find the parameters with the best linear combination by setting the derivation of the step 8 to 0 because the line we're solving for is a convex function and the minimum of it's derivation minimizes the error.

![ols_derivation](/images/posts/ols_derivation.png)

There you have it. We have the normal equation which we can use to find the parameters that minimize the error of the OLS regression problem.


---


