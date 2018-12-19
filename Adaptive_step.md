# Adaptive Step-Size for Policy Gradient Methods

importance of the step size (alpha) in policy gradient methods. Automatic choice by maximizing a lower bound to the expected performance gain. Focus on Gaussian policies.

Lots of litterature on how to estimate the gradient direction. Not so much on the choice of the learning rate. We cannot do line search like in classical optim, it would take too much policy evaluations. Propose a method that guarantees an improvement at each step. Find a lower bound on the difference of performances between two policies, and then between a policy and another with a change of params in the direction of the gradient. Polynomial in alpha, so can be optimized in alpha.

Setting: continuous MDP. L\infty norm on densities to measure the distance between two policies. + same notations as in the other articles.

## Policy gradient formulation

Start from the general bound in SPI (Pirotta, [3]), here in continuous version, then specialize to gradient direction.

Lemma 3.1 (corr 3.6 in [3]): J_mu(theta') - J_mu(theta) >= 1/(1-gamma) \int d_mu^pitheta(s) A_pitheta^theta' ds - gamma/(2(1-gamma)^2) ||pitheta' - pitheta||_infty^2 ||Q^pitheta||_infty

To maximize the perf improvement, we must find a policy with large advantage, but not too different (trade-off). It is controled by the step size in the case of a change in the gradient direction.

Lemma 3.2: if theta' = theta + alpha gradJ(theta), then with deltatheta = \alpha gradJ(theta)

pi(a|s, theta') - pi(a|s, theta) >= alpha gradpi(a|s, theta)^T gradJ(theta) + alpha^2 inf_c\in(0,1) ( \sum_i,j d2pi/dthetai dthetaj|theta+cDeltatheta \deltathetai \deltathetaj /(1+1(i=j) )

Main result combines the two lemmas.

Theorem 3.3: BIG FORMULA (see paper). A fourth order polynomial in alpha. Stationary points can be expressed in closed form. For alpha >0, single stationary point that is a local maximum.

## The Gaussian Policy Model

In the case of gaussian policies, second order derivatives can be easily bounded. So the bound can be reduced to a second order polynomial in alpha, and then closed form expression for alpha.

Lemma 4.1: bound on the second order derivatives for gaussian policies. Restate lemma 3.2 in the case of Gaussians.

Lemma 4.2: bound on Linfty norm between two Gaussians.

Theorem 4.3 (for gaussians): Simpler bound quadratic in alpha.

Cor 4.4: Choice of an optimal step size (see the funny formula) that guarantes performance greater than 1/2 alpha* ||gradJ(theta)||^2

## Approximate Framework

Many quantities cannot be computed in reality, must be estimated through sampling.

Cor 5.1: simpler version of thm 4.3 & cor 4.4. with only deltaJ(theta) unknown.

Estimate hatgradJ(theta) estimated through sampling trajectories. With error bounded with high proba. Introduce dgradJ a low bound to the absolute gradient with approx error. and ugradJ upper bound on L1 norm.

Theorem 5.2: Bound with probability at least (1-delta)^m, and optimal step size.

How to find a bound on the approx error for two policy gradient approaches.

### Approximation with REINFORCE (cf course)

Lemma 5.3: bound on the variance of hatgradJ(theta) for REINFORCE algo, as a function of the number of trajectories + others. Linear with the time horizon

Thm 5.4: Given N=(formula) trajectories, the approx error of the gradient is less than eps with high proba.

### Approximation with GPOMDP/PGT FJGHIFSHG gradient estimator

REINFORCE has too large variance in general. PGT is an approach using likelihood ratio.

Lemma 5.5: Again bound the variance (smaller than in REINFORCE)

Thm 5.6: N=(formula) and same results.

## Numerical Simulations

Linear Quadratic Gaussian LQG regulation problem (cf HW3). Compare the number of iterations for convergence.

Depends on the variance value of the policy. Not clear how to choose it optimal. No real conclusion.



