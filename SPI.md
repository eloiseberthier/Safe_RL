# Safe Policy Iteration

Approximate policy iteration algorithms. When approximate, sequence of policies not monotically increasing, and oscillations may occur.

Safe policy improvement: search for a policy that maximizes a lower bound to the policy improvement wrt the current policy.

Propose two safe-policy iteration algorithms, that differ in the choice of the next policy wrt the estimated greedy policy.

## Introduction

Focus on policy iteration = policy evaluation & policy improvement. When either Q_pi_k or pi_k+1 cannot be computed exactly, we consider approximate policy iteration (API) algorithms. Most studies focus on reducing the approximation error in the policy evaluation step. However, taking the greedy policy wrt an approximate Q function may result in a worse policy, leading to oscillations.

An existing approach: incremental policy updates, make only small updates in the space of policies. Also in the related class of policy gradient algorithms. -> Conservative Policy Iteration (CPI) (Kakade 2002, *cf article 2*)

--> Contributions: (1) lower bounds on the policy improvement, (2) two algorithms (monotically improving policies) by maximizing the policy improvement bounds, and (3) empirical experiments and comparisons.

## Layout

Finite MDP, rewards in [0,1], discount gamma, infinite horizon.

Policies are ranked by their expected dicounted reward starting from the state distribution D:

J_pi,D = \sum_s D(s)V_pi(s) = \sum_s d_pi,D(s) \sum_a pi(a|s) R(s,a),

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

When we make approximations or restrictions to the policy space, the greedy policy in policy iteration can be worse than the current policy. Following the approach of CPI (Kakade), we consider the class of SPI algos. They produce a seq of improving policies and stop when no improvement can be garanteed.

Idea: *implement the policy improvement step as the maximization of a lower bound to the policy improvement* (cf theorem 3.5/6).

Two SPI algos for the exact case (value function known exactly): USPI and MSPI.

### Unique Parameter Safe Policy Improvement: USPI

Update rule: pi' = alpha pibar + (1-alpha) pi. 
In CPI (Kakade), pibar is the greedy policy.

If A^pibar_pi(s)>= 0 forall s, then pi' is not worse than pi for any alpha (always true when pibar is a greedy policy). We take the value of alpha maximizing the lower bound in theorem 3.5 (with pib = pi).

Corollary 4.1: If A^pibar_pi,mu >= 0, then, using alpha*= (1-gamma)^2 A^pibar_pi,mu / (gamma ||Pi^pibar - Pi^pi || _\infty DeltaA^pibar_pi), we set alpha=min(1, alpha*),

so that when alpha*<=1, the policy improvement is garanteed: J^pi'_mu - J^pi_mu >= (1-gamma)^2 A^pibar_pi,mu ^2 / (2 gamma ||Pi^pibar - Pi^pi || _\infty DeltaA^pibar_pi) ;

and when alpha*>1, we perform a full update toward the target policy pibar with the policy improvement in theorem 3.5.

Comparison with CPI: provided a slight improvement of Cor 4.2 in Kakade. The bound is J^pi'_mu - J^pi_mu >= (1-gamma)^2 A^pibar_pi,mu ^2 / (8 gamma / (1-gamma) ).

The difference between USPI and CPI is in the denominator. The improvement in USPI in no worse than CPI. Since CPI is tight, USPI also is. When pibar is the greedy policy in exact policy iteration, USPI guarantees much larger improvements than CPI.

### Multiple Parameter Safe Policy Improvement: MSPI

The new policy is here generated using different convex combination coefficients for each state: alpha(s). Too hard to compute the optimal alpha(s) with theorem 3.5, so use simplified version of Cor 3.6.

Corollary 4.2: Take alpha(s) = 0 where the advantage of pibar over pi is not positive (s \notin S). Elsewhere take alpha(s) = min(1, Gbar* / || pibar(.|s) - pi(.|s) || _1 ), and Gbar* is the value that maximizes the function:

B(Gbar) = \sum_s\inS min(1, Gbar / || pibar(.|s) - pi(.|s) || _1 ) d^pi_mu A^pi'_pi - Gbar^2 gamma/(1-gamma)^2  ||q_pi|| _\infty / 2

Computing Gbar* requires to maximize a function with discontinuous derivative. We can do it with an iterative algo (Algo 1). Idea: start from Gbar = 0, and search for zero crossing values of the derivative of B by running over the values that lead to coefficient saturation. The complexity of the algo is dominated by the cost of sorting the states according to the discrepancy between pi and pibar, O(n_states * (n_actions + log(n_states)).

MSPI optimizes over a much larger set of policies than USPI. But it could lead to smaller policy improvements bounds, because it uses the looser bound 3.6 instead of 3.5. Also, MSPI never does worse than CPI.

## Approximate Safe Policy Iteration

The exact algos can't be exploited when the state-transition model is unknown :( Consider instead sample based versions in order to estimate the terms in the bounds.

It requires many samples to estimate L^\infty bounds like for DeltaA (USPI) or q^pi (MSPI), we consider a simplification of the bound in Cor 3.6:

J^pi'_mu - J^pi_mu >= A^pi'_pi - gamma/(2*(1-gamma)^3)  ||Pi^pi' - Pi^pi || _\infty ^2 (obtained my maximizing ||q _pi|| with 1/(1-gamma)).

The only value to be estimated is A^pi'_pi,mu. It is possibles (Kakade, 2003) to estimate Ahat an eps approximation of A.

General algos aUSPI and aMSPI similar than in CPI:

(1) Initial policy at random.

(2) Select the target policy pibar through the maximization of a sample based version of the Q function.

(3) Produce an eps/(3(1-gamma)) estimate of A.

(4) If Ahat is larger than 2eps/(3(1-gamma)), than compute the next policy according to USPI/MSPI.

(5) When Ahat <= 2eps/(3(1-gamma)) stop and return the current policy.

Since the two are faster than CPI, the number of iterations is no more than O(1/(eps^2 (1-gamma)^2)). No result on the advantage in speed of SPI over CPI.

Theorem 5.1: If the same target policy pibar is used at each iteration, aUSPI and aMSPI terminate after O(1/(eps (1-gamma)^2)) (better).

Conjecture: if the set of target policies is small wrt the number of iterations (achieved by choosing eps small), then the number of iterations would grow linearly with 1/eps instead of quadratically in CPI.

## Experiments

Chain-walk problems and Blackjack.

Exact chain walk: PI >> CPI, but USPI/MSPI converge in a reasonable number of iterations, like NPG (natural policy gradient with hand tuned learning rate). alpha for CPI very low and decreases slowly. For SPI, increase when current pi near the greedy one. Values of alpha suddenly drops when there is a change in the greedy policy. USPI empirically better than MSPI.

Approximate version: aPI oscillates, aSPI slowly converges (at infty), aUSPI/aMSPI converge in a finite number of steps. aMPSI faster than aUSPI, advantage increases with number of states.

Blackjack: search in a subset of two deterministic policies. aPI oscillates, aCPI, aUSPI and aMSPI converge. aMSPI converges faster to a better policy than both aUSPI and aCPI, exploiting the flexibility of multiple convex coeffs.

## Discussion

Limitations of SPI: if at each iteration we choose as target the greedy policy, the algo needs to enumerate all the states. Could restrict to a subset of the policy space. 

Or consider a parametrized subset of policies and use the bounds to provide a safe value for the step size in the natural policy gradient method.

If large number of states, aMSPI requires convex comb for each state. Modified version with subregions sharing coefficients.

Further direction: exploit the bounds to perform approximate policy iteration in the off-policy case. Use theorem 3.5 with pib the exploration strategy.

Theoretically prove that SPI stops in significantly less iterations than CPI.
