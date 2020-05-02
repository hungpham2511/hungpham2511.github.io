---
title:  "HOWTO: Install Real-Time Linux Kernel"
date:   2020-5-1 24:00:00 +0800
categories: setup
tags: os realtime HOWTO linux ubuntu
layout: single
published: true
---

In this post I briefly recall the steps I have taken to install a
**Fully Preemptible Linux Kernel**, which allows processes to run with
Real-Time priority. It's important to remark that this a necessary
condition and is insufficient. Some other tricks are needed. I will
explain what I have learnt in a future post.


## Compiling a Fully Preemptible Linux Kernel

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

## Remark for 5.x version kernels
The **Fully Preemptible Kernel (RT)** option seems to have been
shifted from **Processor type and feature** to **General
setup**. Thanks Iain!


## Readings

Some useful reads I came across while working on this:

- An extremely useful StackOverFlow
  [answer](https://stackoverflow.com/questions/51669724/install-rt-linux-patch-for-ubuntu).
- [Intro to Real-Time Linux for Embedded Developers](https://www.linuxfoundation.org/blog/2013/03/intro-to-real-time-linux-for-embedded-developers/)
