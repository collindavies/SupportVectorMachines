Support Vector Machines
================
Collin Davies
10/5/2021

## Overview

In this problem, you will use simulation to evaluate (by Monte Carlo)
the expected misclassification error rate given a particular generating
model. Let yi be equally divided between classes 0 and 1, and let x
within R^10 be normally distributed.

Given y1 = 0, xi \~ N(0, I10). Given yi = 1, xi \~ N(u, I10) with u =
(1, 1, 1, 1, 1, 0, 0, 0, 0, 0).

The N10 notation just means its a ten-dimensional Gaussian distribution;
you can use the mvrnorm function in the MASS package to help generate
the data. Now, we would like to know the expected test error rate if we
fit an SVM to a sample of 50 random training points from class 1 and 50
more from class 0. We can calculate this to high precision by 1)
generating a random training sample to train on, 2) evaluating the
number of mistakes we make on a large test set, and then 3) repeating
(1-2) many times and averaging the error rate for each trial.

Aside: in real life don’t know the generating distribution, so we have
to use resampling methods instead of the procedure described above.

For all of the following, please enter your error rate as a number
between zero and 1 (e.g., 0.21 instead of 21 if the error rate is 21%).

## P1

Summary: Use svm in the e1071 package with the default settings (the
default kernel is a radial kernel). What is the expected test error rate
of this method (to within 10%)?

Task 1: Load libraries and set seed.

``` r
set.seed(2077)
library(MASS)
library(e1071)
```

    ## Warning: package 'e1071' was built under R version 4.1.1

``` r
library(ISLR)
```

Task 2: Create a function to create data set and calculate test error
rate for SVM.

``` r
mse1 = rep(NA, 100)
for (i in 1:100){
  # x = matrix(rnorm(100 * 10), ncol = 10)
  x = mvrnorm(100, mu=c(1,1,1,1,1,0,0,0,0,0), Sigma=diag(10))
  y = c(rep(1,50), rep(0,50))
  x[y==1, 1:5] = x[y==1, 1:5] + 1
  df = data.frame(x, y=as.factor(y))
  
  svm.fit = svm(y~., data=df, kernel='radial')
  
  x.test = mvrnorm(100, mu=c(1,1,1,1,1,0,0,0,0,0), Sigma=diag(10))
  # x.test = matrix(rnorm(100 * 10), ncol = 10)
  y.test = sample(c(0,1), 100, replace=T)
  x.test[y.test==1,] = x.test[y.test==1,] + 1
  df.test = data.frame(x.test, y.test=as.factor(y.test))
  svm.pred = predict(svm.fit, df.test)
  tab = table(svm.pred, df.test$y)
  mse1[i] = (tab[2] + tab[3])/100
}
```

Task 3: Return test error rate.

``` r
mean(mse1)
```

    ## [1] 0.1638

## P2

Summary: Now fit an svm with a linear kernel (kernel = “linear”). What
is the expected test error rate to within 10%?

Task 1: Revise svm function for linear kernel.

``` r
mse2 = rep(NA, 100)
for (i in 1:100){
  # x = matrix(rnorm(100 * 10), ncol = 10)
  x = mvrnorm(100, mu=c(1,1,1,1,1,0,0,0,0,0), Sigma=diag(10))
  y = c(rep(1,50), rep(0,50))
  x[y==1, 1:5] = x[y==1, 1:5] + 1
  df = data.frame(x, y=as.factor(y))
  
  svm.fit = svm(y~., data=df, kernel='linear')
  
  x.test = mvrnorm(100, mu=c(1,1,1,1,1,0,0,0,0,0), Sigma=diag(10))
  # x.test = matrix(rnorm(100 * 10), ncol = 10)
  y.test = sample(c(0,1), 100, replace=T)
  x.test[y.test==1,] = x.test[y.test==1,] + 1
  df.test = data.frame(x.test, y.test=as.factor(y.test))
  svm.pred = predict(svm.fit, df.test)
  tab = table(svm.pred, df.test$y)
  mse2[i] = (tab[2] + tab[3])/100
}
```

Task 2: Return expected test error rate.

``` r
mean(mse2)
```

    ## [1] 0.1638

## P3

Summary: What is the expected test error for logistic regression? (to
within 10%)

(Don’t worry if you get errors saying the logistic regression did not
converge.)

Task 1: Revise svm function for a linear regression.

``` r
mse3 = rep(NA, 100)
for (i in 1:100){
  # x = matrix(rnorm(100 * 10), ncol = 10)
  x = mvrnorm(100, mu=c(1,1,1,1,1,0,0,0,0,0), Sigma=diag(10))
  y = c(rep(1,50), rep(0,50))
  x[y==1, 1:5] = x[y==1, 1:5] + 1
  df = data.frame(x, y=as.factor(y))
  
  glm.fit = glm(y~., data=df, family="binomial")
  summary(glm.fit)
  
  x.test = mvrnorm(100, mu=c(1,1,1,1,1,0,0,0,0,0), Sigma=diag(10))
  # x.test = matrix(rnorm(100 * 10), ncol = 10)
  y.test = sample(c(0,1), 100, replace=T)
  x.test[y.test==1,] = x.test[y.test==1,] + 1
  df.test = data.frame(x.test, y.test=as.factor(y.test))
  glm.probs = predict(glm.fit, df.test, type="response")
  glm.pred=ifelse(glm.probs >0.5, 1,0)
  tab = table(glm.pred, df.test$y)
  mse3[i] = (tab[2] + tab[3])/100
}
```

    ## Warning: glm.fit: algorithm did not converge

    ## Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

Task 2: Return expected test error rate.

``` r
mean(mse3)
```

    ## [1] 0.1613
