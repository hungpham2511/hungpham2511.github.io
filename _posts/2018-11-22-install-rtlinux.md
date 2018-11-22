---
title:  "HOWTO: TODO Install RT Linux Kernel"
date:   2018-11-22 23:24:01 +0800
categories: os
tags: abc
layout: single
classes: wide
---

I work with robots everyday. 

By working, I mean to write computer programs that listen to signals
from sensors, cameras; compute stuffs; and then send motion commands
to the robot controller. Everything happen very quickly, especially
the commanding part which should occur every 8 milliseconds exactly.

This needs of *exactness* requires the program sending motion commands
to be very punctual. Unfortunately, in a common Operating System (OS)
like Ubuntu, a program, or more accurately a process, is often disrupt
by the [scheduler](link to something to explain).

To avoid this issue, one needs a Real-Time Operating System. While
there are many industrial-grade RTOS available, they have a few
issues: 
- usually not free (QNX, vxwork);
- no ROS, nor the commonly supported software ecosystem;

Fortunately there are free alternative. Real-Time Linux is one such
alternative. In this post I will briefly capture steps I have taken to
achieve Real-Time capability with Linux.

# Achieving Real-Time with Linux: Overview

One thing to be clear: there is no dedicated Real-Time distributions
for Linux. (If I am wrong, please correct me!). Rather, one achieve
Real-Time capability by using a Real-Time Kernel. More precisely, a
Linux Kernel appropriately patched.

# Installation

The following steps are adapted from this extremely useful
StackOverFlow
[answer](https://stackoverflow.com/questions/51669724/install-rt-linux-patch-for-ubuntu).


# Readings

Some useful reads I came across while working on this:

- [Intro to Real-Time Linux for Embedded Developers](https://www.linuxfoundation.org/blog/2013/03/intro-to-real-time-linux-for-embedded-developers/)
