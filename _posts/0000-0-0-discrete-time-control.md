---
title: "DSP 1: The absolute fundamentals"
author: ["Hung Pham"]
date: 2020-06-12
toc: "t"
draft: false
---

In this short note, I recap the absolute minimum understanding to work
with discrete-time signals and systems. These are by no mean complete,
and might not be completely technically correct, as sometimes it's
easier to state a less precise statement for the sake of clarity. Now,
for a very nice course on this topic have a look at [this MIT open
courseware][1] or this [coursera course from EPFL][coursera]


## Discrete-time signal {#discrete-time-signal}

A discrete-time signal \\(x[n]\\) is essentially an array of
floats. Though if you are familiar with thinking an array starts
from 0, it's important to notice that in DSP, a signal is consider
from time negative infinity to infinity. That is

\\[x[n], \text{where } -\infty < n < \infty.\\]


## Linear time-invariant discrete-time system {#linear-time-invariant-discrete-time-system}

A discrete-time system is an abstract mapping that map a
discrete-time signal to another, in mathematical notation we can
write

\\[y[n] = \mathcal{H}\\{x[n]\\}.\\]

Most engineering marvels rest on simple, powerful mathematical
structures. Linearity is perharps the most important
characteristics. We restrict our attention to the class of **linear
discrete-time systems**, which intuitively satisfies:

\\[\mathcal{H}\\{\alpha x[n] + \beta y[n]\\} = \alpha \mathcal{H}\\{x[n]\\} + \beta \mathcal{H}\\{y[n]\\}\\]

We even restrict further to time-invariant systems, which satisfies:

\\[\mathcal{H}\\{x[n - k]\\} = y[n - k] \\]


## Convolution Operation and the Impulse Response {#convolution-operation-and-the-impulse-response}

These previous two assumptions might seem to be relatively abstract. Though, the consequences of
these two points are really cool! Now, recall that the impulse \\(\delta[n]\\) is filled with zero
except at \\(n=0\\), the impulse response of a system is the output of the system given a impulse
input. For Linear Time-Invariant (LTI) system, it is known that the output of a system given
arbitrary input is the convolution of its impulse response with the given input, that is:

\\[
  y[n] = \mathcal{H}\\{x[n]\\} = \mathcal{H}\\{\delta[n]\\} \* x[n] = h[n] \* x[n]
  \\]

This consequence has massive application.  For instance, we can now represent arbitrary filter by
an impulse response, then apply the convolution operation on the input signal to compute the
output signal. In particular,

\\[
  y[n] = \sum\_{k=0}^{\infty} x[n - k] h[k]
  \\]

Notice that for all non-negative \\(k\\), \\(x[n-k]\\) is "in the past" and can be used. In fact, [Finite
Impulse Response filter](<https://en.wikipedia.org/wiki/Finite%5Fimpulse%5Fresponse>) is a very powerful
concept that makes use of this exact property. In my own research, I have designed controller for
discrete-time system by design the filter's impulse response too.

The easiest way to implement a discrete-time system is to transform it into state-space
form. Consider a filter that maps \\(u[n]\\) into an output signal \\(y[n]\\), there exist, potentially
many, state space systems that realize the filter's behavior:

\begin{align}
   x[n + 1] &= A x[n] + B u[n], \\\\\\
   y[n] &= C x[n] + D u[n].
\end{align}

where \\(A,B,C,D\\) are matrices.

In fact, the input and output signals are limitted to being scalar. Thus we can even combine
several filters into a single state-space system, which greatly simplify the
implementation. Indeed, we have a simple implementation below:

```python
class StateSpaceController:
    """A statespace controller."""
    def __call__(self, u):
        xnext = np.dot(self.A, self.x) + np.dot(self.B, u)
        y     = np.dot(self.C, self.x) + np.dot(self.D, u)
        self.x = xnext
        return y
```

The difficult thing in working with discrete-time systems is usually deriving the coefficient
matrices. For that, it's useful to remember the following rules.

-   For scalar system, find the z-transform of the filter, then use \`python-control\` to derive the
    coefficients matrices. The z-transform of a FIR filter is has 1 as the numerator and the taps as
    coefficients of the denominator.
-   For combination of systems, find the state-space form of each of the subsystem, then use
    [\`python-control\`][python-control] combination functions such as \`feedback\` or \`parallel\`.
-   Many libraries for control are available. Look at [python-control] and [slycot], they are very
    nice Python libraries for linear control. [scipy.signal] also contains many useful
    functions. And as the last resort, look at [matlab's Control System Toolbox][matlab].

If these steps work for you, that's great! Though sometimes it doesn't. When this happens, I guess
you just have to sit down and understand the math and try to resolve it. For example,
[python-control] is unable to realize the state-space form of **very long** FIR filter [without
causing numerical issues]().


## Conclusion {#conclusion}

This barely scratches the fundamental of Digital Signal Processing. There are many other potential
venues to explore further. As examples, we could explore the z-transform, then go to the
z-transform of Linear Constant Coefficient filters. From this we can go to the state-space
representation of such filter, which is very useful for either analysis, design or simulation. The
state-space form is ideal for some optimal control such as the Linear Quadratic Regulator, or
concepts such as controllability or observability. This area of engineering is full of interesting
ideas.

That said, until next time!


## Related {#related}

[Dsp2Systems]({{< relref "20200611132621-dsp_2_systems" >}})
