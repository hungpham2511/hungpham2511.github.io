---
title:  "Intuition on Multi-Threaded programming"
date:   2018-12-05 
categories: programming
tags: C realtime
layout: single
published: true
---

# What is a thread?

According to [Beginning Linux Programming](), "Multiple strands of
execution in a single program are called threads".

A strand of execution in a single program can be thought of a sequence
of controls that the program operate on its memory, or variable. Think
of a simple "Hello World" program that asks user for an integer `N`
and print `N` "Hello world!" to the screen. The sequence of controls
is relatively simple:
- define a new integer variable;
- get a value from user input, and assign it to the integer variable;
- loop `N` times and print out the sentence ach time.

The "multiple strands" are then interleaving sequences operating on
the same memory space, or the same set of variables. For instance, one
thread can ask user for a value `N`, while the other thread do some
computation on this input and assign the output value to yet another
variable.  A third thread then periodically look at the value of this
variable and print to the screen.

# Synchronizing multiple threads

The question of synchronization can be stated informally as to how to
make multiple threads work nicely with each other? Alternatively, what
can happen if there are multiple threads in a single process and how
can we address these issues?

## Problem number 1: Conflicts in accessing a common resource

One issue with multi-threaded programs is handling the conflict
between different threads in accessing a common resource. In another
word, there are variables that are accessible by all threads, and if
two threads attempt to change the variables' values at the same time,
the end behavior is undefined. This is undesirable.

The universally accepted solution for this problem is actually quite
straightforward. And yes it is mutex, if you are wondering. But it is
important to understand, not what is a mutex (it actually is a simple
integer-valued variable with atomic operations), but how to *think*
about it.

Let's be more specific.  Suppose now you have a set of variables that
might be accessed by several threads. Near this set, there is a
switch. (Yes, a hypothetical switch!). This switch is initially
on. Now, we will make the following rule:
- any thread that wants to read or change the values of any variable
  in this set of variables must wait until the switch is on;
- if the switch is on, turn it off, then do your stuffs on the
  variable;
- when the editing is finished, turn on the switch and continue.

If the switching mechanism can be made such that in step 2, "check if
it is on, then turn it off" is *atomic*.  That i,s if there are
multiple threads attempt to run this atomic instruction at the same
time, it will always happen sequentially. Then whenever a thread
manages to turn off the switch, it can freely access the variable
without worrying about undefined behaviors!

Notice that here the switch needs not be associated with the set of
variable. Rather, the threads must be designed such that they respect
the switching rule. Whenever a thread wants to access a common
resource, it should make sure to check the switch before editing.


Now, the switch is basically a mutex. Turning on the switch equals
lock the mutex, and so on.

*A common question:* if a thread lock a mutex, can another thread
unlock it?

*Answer*: The answer is: Yes, but you shouldn't do it. Let's return to
the switch analogy. Suppose a thread has just turned on the switch and
is doing its editing. Clearly, another thread can just turn off the
switch, then turn on again and do its editing. Better yet, it does not
even need to care about the switch at all: just go in and edit. Now,
while turning off the switch is possible technically, it is a serious
breach of the switch-rule, making the whole switching strategy fails
defeating the purpose of the synchronization strategy in the first
place.

## Problem number 2: Synchronizing sequence of computations

A second problem in multi-threading is making sure that the threads
process data in order. As an example, consider a simple computational
graph:

```
A ---> B1 ---> C
  \         /
   \-> B2 -/
```

Suppose that we have a very complex computation that involes several
steps. First, the main thread A receives an input from users.  It then
processes the input and sends the result to two threads (thread B1 and
thread B2). After both threads have finished, another thread (thread
C) takes the results from thread B1 and thread B2 and do some touching
up.

Clearly synchronization is required between the computations.

To achive synchronization, a possible strategy is as follows:

- A does her computation, and finishes it;
- A waits for B1 and B2 to come over and take the result;
- B1 tells A: "hey, I got it, you are done with this batch";
- B2 tells A: "hey, I got it, you are done with this batch";
- A starts working on a new computation.

- B1 and B2 starts computing stuffs.
- When they are done, they let C know; only when C has confirmations
  from both B1 and B2 it starts its computation;
- After C1 takes the results, B1 and B2 return to A to get things to
  work on.
  
This strategy is precisely what can be achieved with semaphores.

# Questions and Answers

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
