---
title: "Moving fast to contact"
author: ["Hung Pham"]
date: 2020-06-12
categories: ["control"]
toc: "t"
draft: false
---

## Introduction {#introduction}

Consider the following problem: a position control robot, moving to
make contact with a stiff, spring-like environment with stiffness \\(K\\),

-   what is the fastest speed \\(v\\) that the robot can move down without
    incurring large force, assuming that the robot has a time-delay of
    \\(T\\)?
-   What is the best strategy for moving down?


## Deriving the optimal contact force {#deriving-the-optimal-contact-force}

Consider the following naive, but optimal, strategy: Robot moves down
with velocity \\(v\\), and stops immediately when it measures a small
force. At this instance, the robot has actually been in contact with
the surface for \\(T\\) seconds.  Following this strategy, the robot will
stop exactly \\(T\\) seconds after it actually is in contact, the contact
force is therefore \\[ F\_{\max} = T v K \\]

Suppose \\(T\\) is 10ms, \\(K\\) is 50 N/mm we have
\\[
F\_{\max} = 0.01 \* 50 v = 0.5v
\\]
where \\(v\\) is in mm per seconds. That is to say if the robot moves down
at 20 mm/s, the smallest force that it can receive is 10N.

Summary: If the time-delay is 10ms, the smallest possible force one
can obtain for every 1mm/s of velocity is 0.5N

Is this strategy **reasonable**? Notice that the robot has to make the
decision to stop even when the measured force is very small. If the
robot is actually under a linear control law, which usually produce
small input command given small force measurement, this means the gain
must be very large. In most case, this would lead to instability. This
argument applies too all linear controllers.

Relevant to [2002SwitchingBetweenStabilizingControllers]({{< relref "hespanha2002switching" >}}).


## Linear control law {#linear-control-law}

It's interesting to see what is the maximum force given a velocity
command.  We can try out some simulation. Suppose the robot is under a
linear control law: \\[ v = K\_g (f\_d - f) + v\_d.\\]

The system (robot/environment) dynamics is given by two equations: \\[
x = e^{-T s} \frac{v}{s}, f = K x \\] We can then simulate the instance
to find the maximum force exerted on the robot as it moves down.

```python
import control as co
import matplotlib.pyplot as plt
import numpy as np

s = co.tf([1, 0], [1])
z = co.tf([1, 0], [1], 1e-3)
# x = G v
G = z ** (-10) * co.c2d(1 / s, 1e-3)


ymax = []
gains = np.arange(0.1, 3.0, 0.1)
for gain in gains:
    # f * H = G g f_d + v_d
    H = 1 / 50.0 + G * gain
    # f = M v_d
    M = G / H
    t, y = co.step_response(M, T=np.arange(0, 1, 0.001))

    # plt.plot(y)
    # plt.show()
    ymax.append(np.max(y))
plt.plot(gains, ymax)
plt.xlabel('gain')
plt.ylabel("max force amplitude")
plt.grid(True)
plt.savefig('gain-force-compare.png')
plt.show()
```

<a id="org9c56047"></a>

{{< figure src="/ox-hugo/gain-force-compare.png" caption="Figure 1: Maximum reaction force when moving at 1mm/s at different linear gain" >}}

It should be noted that at gain around 2.0, the system exhibit dynamic
instability.

From this graph, it's clear that the more reponsive the robot is, the
smaller force it feels. This is quite intuitive. What is perharp less
intuitive is that if the gain is too small, the robot might feels a
very large force because it's not **fast enough** to react to the
measurement.


## CCS? {#ccs}

Optimizing a controller specifically for reducing the maximum force
using [ConvexControllerSynthesis]({{< relref "20200322230104_convex_controller_synthesis" >}}) is an interesting idea. I tried it
out and manage to reach approximately 1N minimum force.  The
resulting controller, however, is not at all optimized for other
objectives. Thus, it is not suitable for other purposes.


## A high-level control law {#a-high-level-control-law}

From the previous two sections, it seems like designing a single
controller that allows fast movement in freespace and stability and
performance in contact is simply impossible.

Applying a high-level control law, on the other hand, can achieve
high-performance contact very easily. Consider the following simple
high-level control law:

1.  move down with speed 20mm/s, f\_d = 0;
2.  when in contact immediately switch velocity back to 0mm/s and f\_d = 1N;
3.  similarly, we can adjust the reference velocity based on the
    measure force. For example we can have:

    ```nil
    if f > 5: v_d = 0
    else: v_d = 4 * (5 - f)
    ```

This control law gonna work just fine without much trouble. Putting
together with a linear controller, we thus have a simple
**non-linear** control law that will perform relatively well.


## Conclusion {#conclusion}

Even for the very simple task of making contact as fast as possible,
we learn that a high-level control strategy is
indispensible. Low-level control laws, i.e. CCS or linear
controllers, are insufficient to guarantee high performance.

For more complex task, such as 1 pin insertion, 2 pins insertion
slanted pin, it is clear that a good high-level control strategy is
even more important. Thus it would be very interesting to study this
question more closely.

**Question for future inquiry**:

-   How efficiently can a neural network, or a blackbox optimization
    algorithm can learn new strategy?
-   Can we combine strategy?
-   Can we use joint current instead of force signal? Since the system
    limits are in the robot dynamics and not the measurement, maybe
    it's possible to do very well even with crappy sensor
    measurements (Robotiq, joint current).


## Related {#related}

[ConvexControllerSynthesis]({{< relref "20200322230104_convex_controller_synthesis" >}})

In this note I solve this problem using RL [RlTrialMoveFastToContact]({{< relref "rl-trial:-move-fast-to-contact" >}}).
