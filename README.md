# Safe_RL

In a recent survey [1], the following definition has been provided: “Safe Reinforcement Learning can be defined as the process of learning policies that maximize the expectation of the return in problems in which it is important to ensure reasonable system performance and/or respect safety constraints during the learning and/or deployment processes.” In other words, we aim to design algorithm that guarantees an improvement of the policy performance over time.

Probably, the seminal work in this field is conservative policy iteration [2] which was successively extended in [3]. The concept of safe reinforcement learning was recently formalized in [4]-[5].

The goal of the project is to review the model, the theoretical solution/guarantees, and the algorithmic approaches with a particular attention to solution for continuous domains (e.g., [6]-[7]) or deep RL.

## References

[1] Garcıa, Javier, and Fernando Fernández. "A comprehensive survey on safe reinforcement learning." Journal of Machine Learning Research 16.1 (2015): 1437-1480.

--> Broad survey, a bit larger than what we want to study here.

[2] Sham Kakade, John Langford: Approximately Optimal Approximate Reinforcement Learning. ICML 2002

--> First (implicit) reference to safety in 2002. Defines CPI approaches, can't be used in practice. Focus on policy gradient.

[3] Matteo Pirotta, Marcello Restelli, Alessio Pecorino, Daniele Calandriello: Safe Policy Iteration. ICML (3) 2013

--> In 2013, extension of [2] with SPI algos instead of CPI. Brings both theoretical and experimental results. Focus on policy iteration.

[4] Philip S. Thomas, Georgios Theocharous, Mohammad Ghavamzadeh: High Confidence Policy Improvement. ICML 2015

--> In the wake of [2] and [3]. Formalize the concept of safe RL.

[5] Philip S. Thomas, Georgios Theocharous, Mohammad Ghavamzadeh: High-Confidence Off-Policy Evaluation. AAAI 2015

[6] Matteo Pirotta, Marcello Restelli, Luca Bascetta: Adaptive Step-Size for Policy Gradient Methods. NIPS 2013

[7] Mohammad Ghavamzadeh, Marek Petrik, Yinlam Chow: Safe Policy Improvement by Minimizing Robust Baseline Regret. NIPS 2016
