# Safe Policy Iteration

Approximate policy iteration algorithms. When approximate, sequence of policies not monotically increasing, and oscillations may occur.
Safe policy improvement: search for a policy that maximizes a lower bound to the policy improvement wrt the current policy.
Propose two safe-policy iteration algorithms, that differ in the choice of the next policy wrt the estimated greedy policy.

## Introduction

Focus on policy iteration = policy evaluation & policy improvement. When either Q_pi_k or pi_k+1 cannot be computed exactly, we consider approximate policy iteration (API) algorithms. Most studies focus on reducing the approximation error in the policy evaluation step. However, taking the greedy policy wrt an approximate Q function may result in a worse policy, leading to oscillations.

An existing approach: incremental policy updates, make only small updates in the space of policies. Also in the related class of policy gradient algorithms. -> Conservative Policy Iteration (CPI) (Kakade 2002, *cf article 2*)

Contributions: (1) lower bounds on the policy improvement, (2) two algorithms (monotically improving policies) by maximizing the policy improvement bounds, and (3) empirical experiments and comparisons.

## Layout

Finite MDP, rewards in [0,1], discount gamma, infinite horizon.
Policies are ranked by their expected dicounted reward starting from the state distribution D:
J_pi,D = \sum_s D(s)V_pi(s) = \sum_s d_pi,D(s) \sum_a pi(a|s) R(s,a)
where d_pi,D = \sum_t^\infty gamma^t P(st=s | pi,D) (unnormalized galla discounted future distributions starting from D). Can be normalized with factor 1-gamma. ! in Kakade, it is normalized.

Solving an MDP is finding pi* maximizing J_pi,D. Also define Q, and A_pi(s, a) = Q_pi(s, a) - V_pi(s) advantage function (advantage of taking action a in state s instead of following pi).
Advantage of policy pi' over pi with initial distr mu: A^pi'_pi,mu = \sum_s d_pi,mu(s) A^pi'_pi (s) (same as in Kakade).

Matrix notations for v, q, J, A, Pi for simplicity.

## Bound on Policy Improvement

Aim: lower bound on the performance improvement of pi' over pi, given the policy advantage A^pi'_pi. A^pi'_pi can provide a good estimate of J^pi'_mu only when pi and pi' visit all the states with similar probabilities, ie when d^pi'_mu ~d^pi_mu.
--> In the following results, pi and pi' are stationary policies.

(1) Lemma 3.1: bound on d^pi'_mu - d^pi_mu when the MDP in known.
(2) Corollary 3.2: model-free version (looser). bound on d^pi'_mu - d^pi_mu as a function of ||Pi^pi' - Pi^pi || _\infty (Pi describes the policy).
(3) Lemma 3.3 (Kakade 2002): J^pi'_mu - J^pi_mu = d^pi'_mu T A^pi'_pi. Computing A^pi'_pi is expensive, since it requires to estimate d^pi'_mu for each candidate pi'.
(4) Lemma 3.4: general result on vectors.

(5) Theorem 3.5: given any baseline policy pib,
J^pi'_mu - J^pi_mu >= d^pib_mu T A^pi'_pi - gamma/(1-gamma)^2  ||Pi^pi' - Pi^pib || _\infty DeltaA^pi'_pi / 2
(in the following we will consider pib = pi).
It is the sum of two terms: the advantage of pi' over pi averaged according to the distrib induced by pib, and a penalization term that is a function of the discrepency between pi' and pib and the advantage function.

(6) Corollary 3.6 (looser but simpler, with pib = pi): 
J^pi'_mu - J^pi_mu >= d^pi_mu T A^pi'_pi - gamma/(1-gamma)^2  ||Pi^pi' - Pi^pi || _\infty ^2  ||q_pi|| _\infty / 2

## Exact Safe Policy Iteration
