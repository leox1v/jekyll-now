---
layout: post
title: Mean Field Approximation
header-img: "images/math.png"
comments: true
---

When facing a clustering problem we often have statistical dependencies between the assignments of different objects. This means to evaluate the cost function we need to check the whole space of possible assignments of cardinality $$ k^N $$.

To avoid this computationally infeasible method we approximate the assignment distribution by restricting its space to the space of factorial distributions. 

$$ \mathcal{Q} = \{ Q \in P: Q(c, \Theta^0) = \prod_{i\leq n} q_i(c(i)) , q_i(\nu) \in [0,1] \} $$

Remark: The notion $$q_i(\nu)$$ denotes the probability of assigning object $$ i$$ to cluster $$\nu$$. The function $$ c(i)$$ is the mapping from the objects to the clusters.

With the defined distribution we try to approximate the true underlying clustering distribution i.e. the Gibbs-Distributions for our cost function $$ R(c)$$.  

$$p^{Gibbs}(c,\Theta) = \frac{e^{-\beta R(c)}}{Z} = e^{-\beta (R(c) - \mathcal{F})} $$

To quantify the closeness of our approximated, factorial distribution to the Gibbs distribution we use the Kullback-Leibler-Divergence between them. And we say to have the best approximation if we minimize this divergence.  

$$ \arg \min_{\Theta^0} D^{KL}[Q(c, \Theta^0) || p^{Gibbs}(c,\Theta))] $$

## Meanfield Upper Bound

Because of the non-negativity property of the Kullback-Leibler-Divergence we can derive an upper bound for the free energy $$ \mathcal{F}$$.  

$$\begin{aligned} 0 &\leq \sum_{c \in \mathcal{C}}Q(c, \Theta^0) \log \frac{Q(c, \Theta^0)}{e^{-\beta (R(c) - \mathcal{F})}} \\ &=\sum_{c \in \mathcal{C}}Q(c, \Theta^0) [ \sum_{i \leq n} \log q_i(c(i)) + \beta(R(c) - \mathcal{F})] \\ &= \sum_{i \leq n}\sum_{c \in \mathcal{C}}Q(c, \Theta^0) \log q_i(c(i)) + \beta (\mathbb{E}_Q[R] - \mathcal{F}) \\ &=\sum_{i \leq n} \sum_{\nu \leq K} q_i(\nu) \log q_i(\nu) +\beta \mathbb{E}_Q[R] - \beta  \mathcal{F}\end{aligned} $$

which leads us to the upper bound of the free energy, denoted with $$ \mathcal{B}$$. 

$$ \begin{aligned} \mathcal{F} \leq \underbrace{\frac{1}{\beta}}_\text{temperature scale}\underbrace{\sum_{i \leq n} \sum_{\nu \leq K} q_i(\nu) \log q_i(\nu)}_\text{negative entropy of assignments} + \underbrace{\mathbb{E}_Q[R]}_\text{Expected costs}  &= \mathcal{B} \end{aligned} $$

In the next step we are going to minimize the upper bound $latex \mathcal{B}$ with respect to our probabilistic assignment variable $$ q_u(\alpha)$$ (that's the likelihood of assigning object $$ u$$ to cluster $$ \alpha$$). In order to do this we construct the following lagrangian and set it to zero:  

$$ \begin{aligned}  \frac{\partial}{\partial q_u(\alpha)} [ \mathcal{B} + \sum_{i \leq n} \lambda _i (\sum_{\nu \leq k} q_i(\nu) - 1)] =  0 \end{aligned} $$

The second part of the lagrangian is due to the constraint that we want the assignment for each object to be a probability distribution over the space of possible clusters. This means that the probabilities for the different clusters for one object needs to sum up to one ($$ \sum_{\nu \leq k} q_i(\nu) = 1 \quad \forall i  $$). Evaluating the given expression leads us directly to the desired Meanfield Equations.  

$$ \begin{aligned} 0 = \sum_{c \in \mathcal{C}} \prod_{\substack{i \leq n \\ i \neq u}} q_i(c(i)) \mathbf{1}_{\{ c(u) = \alpha \}} R(c) + \frac{1}{\beta} (\log q_u(\alpha) + 1) + \lambda_u \end{aligned}$$

Solving the equation for $$ q_u(\alpha)$$ and using the above introduced constraint $$ \sum_{\nu \leq k} q_i(\nu) = 1 \quad \forall i  $$ we get the Meanfield Equations.  

$$ \begin{aligned} q_u{\alpha} = \frac{e^{-\beta h_{u,\alpha}}}{\sum_{\nu \leq k} e^{-\beta h_{u,\nu}}} \qquad  h_{u,\alpha} = \mathbb{E}_{Q_{u \rightarrow \alpha}} [R(c)] \end{aligned} $$



## The Algorithm

Now that we know how to derive the Meanfield Equations we want to know how we can actually apply this to real world data. Since the equations are based on the the expected cost of assigning object $$u$$ to cluster $$ \alpha$$  ($$ (\mathbb{E}_{Q_{u \rightarrow \alpha}} [R(c)]$$), we need to be able to decompose our cost function into contributions of object $$ u$$ and costs that are not affected by $$ u$$. The algorithm then follows an iterative EM style approach.

1. Initialize $$ q_i(1), \dots , q_i(k) = \frac{1}{k} \quad \forall i $$  
2. Repeat  
	1. Choose a site $$ u$$ randomly from your objects
	2. E-Step:  calculate $$h_{u,\alpha} = \mathbb{E}_{Q_{u \rightarrow \alpha}} [R(c)]$$ for given $$ q_u(1), \dots , q_u(k)$$
	3. M-Step: calculate $$q_u(\alpha) = \frac{e^{-\beta h_{u,\alpha}}}{\sum_{\nu \leq k} e^{-\beta h_{u,\nu}}} \quad \forall \alpha$$ for given $$h_{u,1}, \dots , h_{u,k}$$


