---
title: "RL Math: Markov Decision Process"
author: ["Hung Pham"]
date: 2020-06-21
categories: ["rl", "math"]
toc: "t"
draft: false
---

Some notes on Markov Decision Process.

## Markov Decision Process

At the heart of every reinforcement learning (RL) problem is a Markov
Decision Process. This section reviews the notations associated with
this concept.

### Terminologies

| Symbol(s)                                                | Meaning(s)                                       |
|----------------------------------------------------------|--------------------------------------------------|
| t = 0, 1, ... T                                          | Discrete time steps                              |
| \\(S\_i \in \mathcal{S}\\)                               | State in time step i                             |
| \\(A\_i \in \mathcal{A}\\)                               | Action taken in time step i                      |
| \\(R\_{i+1}\\)                                           | Reward received at the begining of time step i+1 |
| \\(G\_{i} =\sum\_{k=0}^{\infty} \gamma^k R\_{k+i+1}\\) | Reward-to-go from time step i                    |
| \\(\pi\_{i}(A\_{i}\mid S\_{i})\\)                        | Policy at time step i                            |

### Markov Property

A process is said to satisfy Markov Property if the state of the current
time-step and the action taken in this time step determines completely
the probability distribution of the state of next step. That is,
\\\[\mathbf{Pr}(S\_{t+1} = s', R\_{t+1} = r \mid S\_0, A\_0, R\_1, S\_1,
..., S\_t, A\_t) = \mathbf{Pr}(S\_{t+1} = s', R\_{t+1} = r \mid S\_t,
A\_t)\\\]

### MDP

Directly from the Markov property, a Markov Decision Process can be
specified by two mappings.

1.  Transition probability \\(\rho:
    \mathcal{S}\times\mathcal{A}\times\mathcal{S}\rightarrow
    \mathbb{R}\\) which is defined as \\\[\rho(s', a,
    s):=\mathbf{Pr}(S\_{t+1} = s'\mid S\_t=s, A\_t=a)\\\]
2.  Expected reward \\(r:
    \mathcal{S}\times\mathcal{A}\times\mathcal{S}\rightarrow
    \mathbb{R}\\) which is defined as \\\[r(s', a,
    s):=\mathbf{E}(R\_{t+1}\mid S\_{t+1} = s' S\_t=s, A\_t=a)\\\]

In Sutton and Barto (1998), an alternative formulation is that a single
conditional distribution function is used:

\begin{equation}
  \rho(s', r \mid s, a) := \mathbf{Pr}(S\_{i+1}=s', R\_{i+1}=r \mid
  S\_i=s, A\_i=a)
\end{equation}

Additionally, a MDP can have finite horizon, that is a limited number of
time step or an infinite horizon and a discount factor.

It is often useful to indicate terminal states. These are states from
which only self-looping action is available.

Finally, note that we implicitly assume transition function \\(\rho\\)
and reward function \\(r\\) do not depend on time.

### Finite MDP and Exact solution

A finite MDP can be solved relatively easily using Dynamic Programming
(Bellman (2013)). In fact, using DP to solve MDP is fairly common
strategy to solve optimal control problems (Bertsekas (2007)).

Here I briefly explain Value Iteration, a **classical** way to solve
MDP. Though, be warned that nobody does it nowaday because it doesn't
scale well. Define value function and action-value function at time step
\\(i\\)

\begin{equation}
  \begin{aligned}
  v\_i(s) &:= \mathbf{E}\_{\pi^\*\_{i}} \left[G\_i\mid S\_i=s\right]   \\\\\\
  q\_i(s, a) &:= \mathbf{E}\_{\pi^\*\_{i}} \left[G\_i\mid S\_i=s, A\_i=a\right]   \\\\\\
  \end{aligned}
\end{equation}

where \\(\pi^{\*}\_{i}\\) is the optimal policy at time step \\(i\\).

The famous principle of optimality states the following exact relation

\begin{equation}
  v\_i(s)=\max\_{a\in \mathcal{A}}q\_i(s, a)
\end{equation}

Using this principle, the finite horizon MDP can be solved via the
following recursive relations

\begin{equation}
  \begin{aligned}
    v\_{T}(s) &= 0\\\\\\
  v\_{i-1}(s) &= \max\_{a\in \mathcal{A}} \left[ \sum\_{s'\in \mathcal{S}}\rho(s, a, s') \left< r(s,a, s') + \gamma v\_i(s') \right>\right]
  \end{aligned}
\end{equation}

### Infinite MDP

**Good news**. The subscript indicating time step disappear. Both value
and action-value functions are time independent now. This leads to an
unique set of optimal functions.

**Bad new**. Theoretically, these functions might not exist. In fact,
the commonly used discount factor is include precisely to forms upper
bounds and ensures convergence and existence.

### Comments on MDP

Policty \\(\pi\\) is a mapping from the current state to action. In
another word, it is the controller. In practice, a robot controller does
not have access to the state of the problem; instead, it can only use
the measure outputs \\(\mathbf{y}\\) as shown in the below figure.

{{< figure src="/ox-hugo/general-control-problem.png" >}}

The question is then, how applicable is MDP to an actual control
problem? It seems to me the answer is somewhat murky. Basically most
control algorithms learn controllers that do not require states as
inputs but observation/measure outputs instead. The issue with that is
that the MDP theory no longer applied and that there is no more
theoretical guarantee on the final policy or the algorithm that computes
that policy.

An extension of MDP that tries to remain theoretically inclined is
Partially-Observable MDP. Most notably in mobile robots.

## A "practical" Markov Decision Process

At the momement, most RL framework and algorithms assume a similar
structure of the environment. Instead of defining a MDP or POMDP
explicity, say with transitional and reward probabilities, a black-box
environment is given. This environment can be **stepped** forward using
**action** and produces **reward** and **observation**. To be
mathematically precise, it's a POMDP, not MDP as commonly described in
the literature.

The most popular framework is OpenAI gym. This library provides a common
interface to defining RL environment, which is very convenient. A basic
environment look like this:

``` python
# example from https://gym.openai.com/docs/#environments
import gym
env = gym.make('CartPole-v0')
for i_episode in range(20):
    observation = env.reset()
    for t in range(100):
        env.render()
        print(observation)
        action = env.action_space.sample()
        observation, reward, done, info = env.step(action)
        if done:
            print("Episode finished after {} timesteps".format(t+1))
            break
env.close()
```

To define a new environment, I find this is a good
[instruction](https://github.com/openai/gym/blob/master/docs/creating-environments.md#how-to-create-new-environments-for-gym)
to follow. Basically it iss sufficient to follow the interface defined
by the abstract class
[gym.core.Env](https://github.com/openai/gym/blob/abef916ca2e858a3fe9765738e25209cd314cd14/gym/core.py#L8).
Almost all RL algorithms expect this interface, thus it is very easy to
test out different algorithms on the problem.

Also noted from my own experiment [RlTrialMovingFastToContact]({{< relref "rl-trial:-move-fast-to-contact" >}}), the environment's
definition has a major effect on whether RL algorithms can effectively
solve it. There are a few considrations:

-   Actions and observation should be normalized to around 1.
-   For discrete-time problem, a shorter horizon (\~100 steps) is easier
    to learn comparing to a much longer horizon of 1000 steps.
-   Reward shaping is very important to achieving good performance.

## RL Algorithms

Here let's review the juicy part: the math of RL algorithms. Now I must
note that understanding the math is probably 30% of the task. To
implement a RL algorithm, the implementation and the tricks constitute
the rest of the work.

## Related

[DeeplearningMainEntrypoint]({{< relref "deeplearning-main-entrypoint" >}})

[MovingFastToContact]({{< relref "moving-fast-to-contact" >}})

## References {#references}

<div id="refs" class="references hanging-indent">
  <div></div>


<div id="ref-bellman2013dynamic">
  <div></div>

Bellman, Richard. 2013. *Dynamic programming*. Courier Corporation.

</div>

<div id="ref-bertsekas1995dynamic">
  <div></div>

Bertsekas, Dimitri P. 2007. *Dynamic Programming and Optimal Control*.
Athena Scientific Belmont, MA.

</div>

<div id="ref-sutton1998reinforcement">
  <div></div>

Sutton, R S, and A G Barto. 1998. "Reinforcement learning: an
introduction." MIT press. <https://doi.org/10.1109/TNN.1998.712192>.

</div>

</div>
