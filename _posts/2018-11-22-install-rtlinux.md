---
title:  "HOWTO: TODO Install RT Linux Kernel"
date:   2018-11-22 23:24:01 +0800
categories: setup
tags: os realtime HOWTO
layout: single
published: true
---

I work with robots everyday. 

By working, I mean to write computer programs that listen to signals
from sensors, cameras; compute stuffs; and then send motion commands
to the robot controller. Everything happen very quickly, especially
the commanding part which should occur exactly every 8 milliseconds.

This needs for *exactness* requires the program/process sending motion
commands to be very punctual. Unfortunately, in a common Operating
System (OS) like generic Ubuntu/Linux, a program/process is often
disrupt by other processes. These processes can be from the user or
the OS itself. This situation causes undesirable delays in the
critical real-time process.

To avoid this issue, one needs a Real-Time Operating System
(RTOS). While there are many RTOS available, they have a few issues:
- not free (QNX, vxwork);
- does not support the software ecosystem for robotics (ROS,
  OpenRAVE).

Fortunately, there are free alternatives. Real-Time Linux is one such
alternative. In this post I will briefly capture steps I have taken to
achieve Real-Time capability with Linux.

# Achieving Real-Time with Linux: Overview

There is one thing that needs to be clear: there is no such thing as a
Real-Time Linux distribution.  Rather, one can achieve Real-Time
capability by running a Fully Preemptible Linux Kernel instead of the
generic Kernel.  Fortunately, it is quite simple to do so.

First, choose a RT patch from this
[mirror](https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/). I
chose version 4.14.78-rt47. The lastest patch as of the time of this
writing is 4.19-rt3, unfornately does not work on my machine running
Ubuntu 16.04. In any case, the worst that can happen is you have to do
the whole compiling/install process again.

Note the Kernel version you have choosen, download the corresponding
kernel from this
[mirror](https://www.kernel.org/pub/linux/kernel/). Both steps can be
done via the terminal as below.
```bash
cd ~/Downloads
# download patch
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/4.14/patch-4.14.78-rt47.patch.xz
# download kernel
wget https://mirrors.edge.kernel.org/pub/linux/kernel/v4.x/linux-4.14.78.tar.xz
```

Unzipped the archives and patch the kernel with the RT patch.
```bash
tar xvf linux-4.14.78.tar.xz
cd linux-4.14.78
xzcat ../patch-4.14.78-rt47.patch.xz | patch -p1 --verbose
```

We now enable the option Fully Preemptible Kernel (RT). First,
install two necessary dependencies.
```bash
sudo apt-get install libncurses-dev libssl-dev
```
Configure the compilation by doing
``` bash
make menuconfig
```

You will see a graphical user interface (GUI) show up in the
terminal. This GUI contains configurations for compiling the
Kernel. The bit that we need is called **Preemption Model**:
- Navigate using arrow keys to **Processor type and feature**, then
  press Enter;
- look for **Preemption Model**, press Enter;
- choose **Fully Preemptible Kernel (RT)** option;
- return to the first screen by pressing Esc multiple times;
- save .config file, then exit.

Compile the Kernel and install.
```bash
make -j20
sudo make modules_install -j20
sudo make install -j20
```

And that's it. Reboot, remember to choose the patched Kernel in
Advanced Boot Setting, and enjoy!


# Readings

Some useful reads I came across while working on this:

- An extremely useful StackOverFlow
  [answer](https://stackoverflow.com/questions/51669724/install-rt-linux-patch-for-ubuntu).
- [Intro to Real-Time Linux for Embedded Developers](https://www.linuxfoundation.org/blog/2013/03/intro-to-real-time-linux-for-embedded-developers/)
