---
layout:     post
title:      "An interesting relationship between training and testing error"
subtitle:   ""
date:       2026-1-19 12:00:00
author:     "lLukii"
header-img: "img/dark-mode-otherworld-wallpaper-v0-utqp151a9e7b1.png"
catalog: true
mathjax: true
tags: 
    - Computer Science
---
**Note: this is copied from my old website**

In this blog, we show that a model's training error will always be less than its testing error. By mathematically proving this idea, we confidently reveal that assessing an ML model's performance on training error will always be naievely optimistic, which leads to inaccurate conclusions about the model's performance. I got this question from a problem set in a statistical learning class I took over the summer. The solution below closely matches the 4 parts on my homework, but there might be other ways to prove this theorem (and if so, let me know).

**Problem statement:** We recieve a $D_1 = \{(x_i, y_i)\}_{i=1}^N$ such that $y_i = f(x_i) + \varepsilon_i$. We then estimate $f$ by $\hat f$ using some learning algorithm and yield predictions $\hat y_i = \hat f(x_i)$. Define the training error as $$\hat R_{train}=\sum_{i=1}^{N}(y_i - \hat y_i)^2$$

Simiarily, we recieve a testing dataset $D_2 = \{(x_i, y'_i)\}_{i=1}^N$ where the set of $x$ values in $D_2$ is identical to that of $D_1$ and $y'_i = f(x_i) + \varepsilon'_i $ must hold true. Assume that $\varepsilon_i$ and $\varepsilon'_i$ are independent random variables and come from the same probability distribution with identical expected value and variance. Now, define the test error as $$\hat R_{test}=\sum_{i=1}^{N}(y'_i - \hat y_i)^2$$

We want to prove that (1.1) $$E[\hat R_{train}] < E[\hat R_{test}]$$ and that (1.2)
$$E[\hat R_{test}] = E[\hat R_{train}] + 2\sum_{i=1}^{N} Cov(y_i, \hat y_i)$$
Where $E$ denotes expected value, $V$ denotes variance, and $Cov$ denotes covariance. 

First, notice that $Cov(y_i, \hat y_i)>0$ must hold true. Since $\hat f$ is trained on $D_1$, then $\hat f(x_i)$ must generally increase as $y_i$ increases and vice versa. Showing that this holds true for all $i \in [1, N]$ implies that $2\sum_{i=1}^{N} Cov(y_i, \hat y_i)$ must always be positive. Thus, showing (1.2) will automatically prove (1.1). 

**Lemma 1:** $E[y_i]=E[y'_i]$, $V[y_i]=V[y'_i]$, and $Cov(y_i, y'_i)=Cov(\hat y_i, y'_i)=0$ holds true.

By linearity of expectation, we have 
$$E[y_i]=E[f(x_i)+\varepsilon_i]=E[f(x_i)] + E[\varepsilon_i]$$
$$E[y'_i]=E[f(x_i)+\varepsilon'_i]=E[f(x_i)] + E[\varepsilon'_i]$$

Since both error terms are part of the same distribution, their expected value must be the same. $y_i$ and $y'_i$ therefore have the same expected value. Simiarily, expand the two variables to prove the second claim: $$V[y_i]=V[f(x_i)+\varepsilon_i]$$
      $$V[y'_i]=V[f(x_i)+\varepsilon'_i]$$ Since $f(x_i)$ is a constant, adding it to our two error terms
      does not change the variance. Because the errors come from the same distribution anyway, they must also have
    the same variance, thus proving that $y_i$ and $y'_i$ have the same variance.

**Lemma 2:** $E[(y_i-\hat y_i)^2]=V[y_i]+V[\hat y_i]-2Cov(y_i, \hat y_i)+(E[y_i]-E[\hat y_i])^2$

Recall that for some random variables $X,Y$, we have $V[X]=E[X^2]-E[X]^2$ and $Cov(X,Y)=E[XY]-E[X]E[Y]$. Using these definitions and working backwards, we get
$$V[y_i]+V[\hat y_i]-2Cov(y_i, \hat y_i)+(E[y_i]-E[\hat y_i])^2$$
      $$=E[y_i^2]-E[y_i]^2+E[\hat y_i^2]-E[\hat y_i]^2-2E[y_i \hat y_i]+2E[y_i]E[\hat y_i]$$
      $$+E[y_i]^2-2E[y_i]E[\hat y_i]+E[\hat y_i]^2$$
      $$=E[y_i^2]+E[\hat y_i^2]-2E[y_i \hat y_i]$$
      $$=E[y_i^2-2y_i \hat y_i + \hat y_i^2]=E[(y_i-\hat y_i)^2]$$

Using the same definitions and method of expansion, we can also show that 
  $E[(y'_i-\hat y_i)^2]=V[y'_i]+V[\hat y_i]-2Cov(y'_i, \hat y_i)+(E[y'_i]-E[\hat y_i])^2$ must also hold true.

Now, let's combine all the previous parts together and prove (1.2). We start by substituting $\hat R_{train}, \hat R_{test}$ for their definitions

$$E[\hat R_{train}]+2\sum_{i=1}^{N} Cov(y_i, \hat y_i)=E[\sum_{i=1}^N(y_i - \hat y_i)^2]+2\sum_{i=1}^{N} Cov(y_i, \hat y_i)$$
    $$=\sum_{i=1}^NE[(y_i - \hat y_i)^2] + 2\sum_{i=1}^{N} Cov(y_i, \hat y_i)$$

By lemma 2, $$=\sum_{i=1}^NV[y_i]+V[\hat y_i]-2Cov(y_i, \hat y_i)+(E[y_i]-E[\hat y_i])^2+2Cov(y_i, \hat y_i)$$
    $$=\sum_{i=1}^NV[y_i]+V[\hat y_i]+(E[y_i]-E[\hat y_i])^2$$

Likewise,
    $$E[\hat R_{test}]=E[\sum_{i=1}^N(y'_i - \hat y_i)^2]$$
    $$=\sum_{i=1}^NE[(y'_i - \hat y_i)^2]$$
    $$=\sum_{i=1}^NV[y'_i]+V[\hat y_i]-2Cov(y'_i, \hat y_i)+(E[y'_i]-E[\hat y_i])^2$$

By lemma 1, $2Cov(y'_i, \hat y_i)=0$, so this term cancels out. Since $E[y_i]=E[y'_i]$ and $V[y_i]=V[y'_i]$, we can substitute these into $R_{test}$ as well. Doing so gives us the same expansion as $
      E[\hat R_{train}]+2\sum_{i=1}^NCov(y_i, \hat y_i)$, proving (1.2) and therefore (1.1). 