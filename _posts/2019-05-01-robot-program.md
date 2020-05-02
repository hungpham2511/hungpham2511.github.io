---
title:  "A note on software architecture for robotic applications" 
date:   2019-5-1
tags: programming
layout: single
published: false
---

This note addresses the question: How should one design the
architecture of a robotic application?


# A single, monolithic application

The simplest architecture consists of a single, monolithic
application. See the below figure for a visualization.

The clear advantages of this architectures:
- Simplicity: This architecture follows a simple main-style
  program. Every action is performed sequentially (if one opt to not
  use threading, of course).


However, it comes with many disadvantages:

- interaction with hardware;

- long running computations;

- coupling between different components of the system: multiple
  elements are using the same resource, potential points for bugs and
  so on.

- the coupling make it hard to extend a new feature, or modify an
  existing feature;

- computational requirements of long running processes, Python can't
  have true multiprocessing.


# A better solution: Service-oriented light-weight processes

- Each process provides several services, which are grouped based on
  whether they are related.

- Each service call should be state-less: all data required to process
  each service call is contained in the message itself, or is a state
  of the server. How to process the call should not depend on the
  client.

- A service call can be made to alter the state of the server, perform
  comptuation or both. While change the state of the server is
  sometimes not desirable, it is a fundamental feature of robotic
  applications. For example, consider a manipulator, calls to the
  manipulator moves it, which is basically a change of the
  manipulator's internal state.
  
- A process should, in most case, be capable of fire-and-forget
  operation. That if, even if an emergency occurs as the process is
  starting or handling a service call, it should still be abe to
  start. Or, in the later case, return correctly a message indicating
  failure. If not all secondary processes or devices are disconnected
  during its run, the process should remains alive, and reports
  correctly the current situation should it is asked.

- Service call should always return. A directly implication is that
  exception on server side is to be avoided at all cost.

- Design high-level application logic with a state machine, this shows
  the highest level of flexibility in handling edge cases or complex
  scenarios.
  
A major advantage of this design is exensibility.


