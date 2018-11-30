# Approximately Optimal Approximate RL

## Setting

Finite MDP, discounted gamma

Assume access to $\mu$ restart distribution, which draws the next state from the distribution $\mu$.

advantage function $A_\pi(s, a) = Q_\pi(s, a) - V_\pi(s)$, normalized so that A dans -R, R, Q dans 0, R.

Define the gamma discounted future state distribution under $\pi$, $\mu$ initial distribution D:

d pi, mu (s) = (1-gamma) sum 0, \infty gamma^t Pr(st = s | pi, mu)

## The problems with Current Methods

(1) Is there some performance measure garanteed to improve at each step?
(2) How to verify it?
(3) What performance obtained after some steps (a reasonable amount of time, not asymptotic)?

Approximate value function methods are like policy iterations, but with an approximate V function. No good theoretical results. V is just garanteed to not decrease too much at each iteration.

Policy gradient methods
eta_D(pi) =  E_s~D (V_pi (s))

Parametrized class of policies pi_theta, compute the gradient of eta_D(pi), which we try to maximize wrt pi. If the gradient is properly computed, then performance garanteed to improve at each iteration.

Lack of exploration can lead to an exponential number of samples needed to estimate the right gradient direction  (simple MDP example given). In fact we could estimate the gradient magnitude, but the direction is hard.
Asymptotic results are possible, but could be exponentially slow.

## Approximately Optimal RL

Fundamental problem with policy gradient: eta_D is insensitive to improvements at unlikely states, though policy improvement at these states might be needed to find optimum. -> Find an alternative performance measure that does not do that.

eta_mu(pi) = E_s~mu (V_pi (s)), where mu is some exploratory restart distribution. More uniform weighting of the improvement from all states. If we have access to a mu restart distribution, we can estimate eta_mu(pi).

Any optimal policy both maximizes eta_mu and eta_D. But the policy maximizing eta_mu *in the parametric model* could perform poorly wrt eta_D.

A more conservative update rule: pi_new = (1-alpha) pi + alpha pi', with alpha < 1.

### Policy improvement

Improve the policy with alpha > 0 and pi' that chooses better actions at most but not all states. Define the policy advantage A_pi,mu (pi') of pi' wrt pi and mu. It measures how much pi' chooses actions with large advantages, wrt the states visited under pi, starting from s~mu.

Delta eta_mu = alpha / (1-gamma) A + O(alpha^2)
For alpha small, there is policy improvement if A >0. If alpha = 1, degradation can occur.

Bounds of this improvment depend on A and R (bound on rewards).
Taking alpha = (1-gamma) A / (4R) garantees policy improvement eta_mu(pi_new) - eta_mu(pi) >= A^2 / (8R)

### Quality of policy obtained after a reasonable number of policy updates

Suppose access to an eps greedy policy chooser: G_eps(pi, mu) a function of pi, mu returning pi' st  A_pi,mu (pi') >= max(A_pi,mu) - eps.

For instance a regression algorithm which fits the advantages with eps/2 average error is enough to construct a G_eps.

When OPT(A_pi,mu) < eps, the greedy policy chooser is no longer garanteed to return a policy with positive policy advantage, and then policy improvement is not garanteed. So in the main algo, we will stop when the policy advantage  A_pi, mu is less than eps (breaking point).

## Conservative policy iteration algorithm

(i) Call G_eps(pi, mu) to obtain some pi'

(ii) Estimate the policy advantage A_pi,mu(eps') with some Ahat. Obtain eps/3 precision using O(...polynomial...) mu restarts for this estimation (with importance sampling, details given...)

(iii) If A is less than 2eps/3 : stop and return pi

(iv) Else update the policy with parameter alpha=(1-gamma)(Ahat - eps/3)/(4R)  and go to (i)

With high proba, the algo improves eta_mu at every update, stops in at most 72*R^2/eps^2 calls to G_eps and returns a policy pi st OPT(A) < 2eps. Time bound independent from mu the restart distribution.

Bound on the real performance eta_D. pi st OPT(A)<eps, eps* optimal policy:
eta_D(pi*) - eta_D(pi) <= eps/(1-gamma) || d_pi*,D / d_pi,mu ||_infty

The last term measures the mismatch between the distributions of states under pi and pi*. A more uniform d_pi,mu prevents this mismatch from being too large.

## How good is the policy found?

Strongly depends on the restart distribution mu.

Lemma: For any pi1, pi2 and mu,
eta_mu(pi2) - eta_mu(pi1) = 1/(1-gamma) E(a,s)~(pi2 dpi2,mu) [A_pi1(s, a)]

Even if the optimal policy advantage is small wrt pi and D, the advantages may not be small under d_pi*,D. This motivates the use of a more uniform distribution mu than D.

How far from optimal is pi (return by the algo) wrt an arbitrary measure mubar?

eta_mubar(pi*) - eta_mubar(pi) <= eps/(1-gamma) || d_pi*,mubar / d_pi,mu ||_infty

## Discussion

An algorithm that finds an "approximately" optimal solution in polynomial time in eps, but not in the size of the state space (!).

(a) Finding a greedy policy chooser is a regression problem.

(b) How is eta_D improved? Improving both eta_D and eta_mu at each update is not possible. Not solved here.

(c) The bounds show that we should ensure the agent starts in states where pi* tends to visit. Use prior knowledge to choose mu.
