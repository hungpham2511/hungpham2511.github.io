---
title:  "Threads in C"
date:   2018-11-04 24:00:00 +0800
categories: programming
tags: C realtime
layout: single
published: false
---

# What is a thread?

According to [Beginning Linux Programming](), "Multiple strands of
execution in a single program are called threads". What does this even
mean?

A strand of execution in a single program can be thought of a sequence
of controls that the program operate on its memory, or variable. Think
of a simple "Hello World" program that asks user for an integer `N`
and print `N` "Hello world!" to the screen. The sequence of controls
is relatively simple:
- define a new integer variable;
- get a value from user input, and assign it to the integer variable;
- loop `N` times and print out the sentence.

The "multiple strands" are then interleaving sequences operating on
the same memory space, or the same set of variables. For instance, one
thread can ask user for a value `N`, while the other thread do some
computation on this input and assign the output value to yet another
variable.  A third thread then periodically look at the value of this
variable and print to the screen.

# Synchronizing multiple threads

In another word, how to make multiple threads play nice with each
other? Alternatively, what can happen if there are multiple threads in
a single process and how can we address them?

## Problem number 1: Common resource

A central problem programmers have to deal with while working with
multiple threads is the simple fact that there are variables that are
accessible by all threads. For examples, global variables can be
accessed by all threads. Then, if both threads access a same variable
at the same time, the actual outcome is undefined. A very undesirable
situation.

The unversally accepted solution for this problem is actually quite
straightforward. Suppose now you have a set of variables that might be
accessed by several threads. Near this set, there is a switch. (Yes, a
hypothetical switch!). This switch is initially on. Now, we will make
the following rule:
- any thread that wants to access--read, write--the set of variables
  must wait until the switch is on, then attempt to turn it off;
- after successfully turning the switch off, go in a edit the values
  of the variables;
- when the editing is finished, turn on the switch and continue.

These simple rules will allow a multi-threaded process to access
common resources without any conflict. Despite its simplicity, this is
the universally accepted solution.

## Problem number 2: Synchronizing sequence

A second problem in a multi-threading process is in making sure that
the thread are processed in order. As an example, consider a simple
computation graph

```
A ---> B ---> C

```

Suppose we have a very difficult computation. First, the main thread
receives an input from a text file. Then, it does some initial
computation and sends the result to two threads (thread 1 and thread
2). After both threads finished, another thread (thread 3) takes the
results from thread 1 and thread 2 and do some touching up.

Without a kind of synchronization, this computation can be very error
prone. Suppose now thread 1 has finished, but thread 2 has not,
without any form of synchronization, thread 3 will just take whatever
computational results from the output of thread 1 and 2. 

Rules:
- A do her computation, and finish it;
- A waits for B to come over and take the result;
- B tells A: "hey, I got it, you are done with this batch";
- A starts working on a new computation.

Step 2 and step 3 are fundamental to synchronizing different threads.

# Semaphore


# Mutex

# Some Questions and their Answers

1. If I have two threads that share the same variable, one thread only
   read the variable value and does not write, do I need thread
   synchronization?

   The short answer is: yes, synchronization is still needed. The
   reason is not because when two threads access the variable at the
   same time the net behavior is undefined. This is not a problem
   because reading operation is "atomic". The main problem is that the
   compiler might rearrange the code before compiling, and the mutexes
   and semaphores are needed to create memory barriers to prevent this
   re-organization.
