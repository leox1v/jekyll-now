---
layout: post
title: Maximum Entropy Inference
---
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
Maximum Entropy Inference is used to find the underlying distribution of the data. In particular, it finds the density that fulfills given constraints but is maximally undefined otherwise. In other words:

We look for the distribution which fits the measurements but otherwise is most similar to the uniform distribution.
Why the uniform distribution? Because the uniform distribution makes the least additional assumptions on the data.

## What are we even looking for?

To find the optimal distribution described by the above definition, we first need to come up with an objective which captures this notion and can later be optimized. To do so we introduce the concept of Surprise. Surprise S is defined as

$ S(p) = log_2 (\frac{1}{p}) S(\frac{1}{2}) $

where $latex S(1/2)$ is defined as the unit of surprise, bit. Intuitively, this definition of surprise makes sense. It has the same properties that we associate with the word in everyday life:

* There is no surprise for the sure event ($latex S(p=1)=0$)
* If some event A has a higher probability than another event B, then $latex S(p_A) \leq S(p_B)$.  (You would be more surprised if you get a Ferrari for Christmas than another pair of socks, because the probability for the latter is a lot higher.)
Now that we have a mathematical expression of surprise we can come up with a nice objective that captures our goal of finding a distribution that is maximally undefined. We say that a distribution is maximally undefined if we made the least assumptions about it in terms of fitting it to the data. Combining this goal with the above definition of surprise we could say:

>The maximally undefined distribution is obtained by minimizing the expectation of surprise over our distribution, i.e.
> $latex min_{p(x)} \mathbb{E}_{p(x)}S(p(x)) = min_{p(x)} - \int_\Omega p(x) log(p(x)) &s=2$
>Optimizing our Objective

## Optimizing our Objective 

Now that we have our objective we need to introduce all the constraints so that we can finally start optimizing our problem and find the distribution we're looking for.

### Constraints

Since we want to obtain a proper density we need to fulfill the two obvious constraints, namely

$latex \int_\Omega p(x)dx = 1 &s=1$
$latex p(x) \geq 1 &s=2$ for all $latex x \in \Omega  &s=1$
Additionally we need constraints that depend on the data itself. Otherwise the whole procedure wouldn't make sense and we would always come up with a uniform distribution over our solution space $latex \Omega  $. We call these data dependent contstraint Expectation Constraints. We can have m of such constraints

* $latex \int_\Omega p(x)r_j(x) dx = \mu_j &s=2$ for   
* $latex 1 \leq j \leq m &s=2$

where $latex r_j(x) $ is a function of x that represents the argument for the expectation moment, e.g.

* For the mean we have $latex \mathbb{E}x =\int_\Omega p(x)x dx = \mu_1  \implies r_1(x) = x&s=2$
* For the variance we have $latex \mathbb{E}x^2 =\int_\Omega p(x)x^2 dx =  \mu_2  \implies r_2(x) = x^2&s=2$

### Constrained Optimization Problem

We have our objective and the constraints in place and can now build our lagrangian to finally optimize the problem.

$latex \begin{aligned} \mathcal{L} &= -\int_\Omega p(x) log(p(x)) dx + \lambda_0 (\int_\Omega p(x) dx -1) + \sum_{j=1}^m (\lambda_j \int_\Omega p(x) r_j(x) - \mu_j) \\ &= \int_\Omega p(x) (-log(p(x)) + \lambda_0  + \sum_{j=1}^m \lambda_j  r_j(x))dx -\int_\Omega (\lambda_0 +\sum_{j=1}^m \lambda_j \mu_j) dx \\ \frac{\partial \mathcal{L}}{\partial p(x')} &= -log(p(x')) -1 + \lambda_0 +\sum_{j=1}^m \lambda_j \stackrel{!}{=} 0 \\ \Leftrightarrow & p(x') = e^{\lambda_0 -1} e^{-\sum_{j=1}^m \lambda_j x'} = \frac{1}{Z} e^{-\sum_{j=1}^m \lambda_j x'}  \end{aligned} &s=1$

Since we constrained $latex p(x) $ to be a probability density function we find out Z easily by normalizing the distribution. This leads us to the final form, the so called Gibbs Distribution.

> $latex \begin{aligned} p(x)  = \frac{e^{-\sum_{j=1}^m \lambda_j x}}{\int_{\Omega} e^{-\sum_{j=1}^m \lambda_j x'} dx'}  \end{aligned} &s=1$
> Examples and Further Readings

At the Wikipedia page of  [Maximum Entropy Probability Distribution](https://en.wikipedia.org/wiki/Maximum_entropy_probability_distribution) you find a table with different Maximum Entropy Constraints and their resulting probability density functions. The most common case you will probably encounter is when you  want to fit the density only to the mean and variance of the data, which implies the two Maximum Entropy Constraints:

* $latex \mathbb{E}x =  \mu  \implies r_1(x) = x \quad \text{and} \quad \mu_1 = \mu &s=2$
* $latex \mathbb{V}x =  \sigma^2  \implies r_2(x) = x^2 \quad \text{and} \quad \mu_2 = \sigma^2 &s=2$
The resulting distribution with these two additional constraints is the Normal Distribution. 

$latex \begin{aligned} p(x) = \frac{1}{\sqrt{2\pi} \sigma} e^{-\frac{(x-\mu)^2}{2\sigma^2}} \end{aligned} &s=2$