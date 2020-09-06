---
title: "Installing Linux Kernels: generic, low-latency or PREEMPT_RT"
author: ["Hung Pham"]
date: 2020-09-05
categories: ["blog"]
draft: false
---

## Motivation: Achieving Real-Time (RT) capability {#motivation-achieving-real-time--rt--capability}

In this post I will briefly capture steps I have taken to achieve
Real-Time capability with Linux.

Remark: There is no such thing as a Real-Time Linux distribution.
Rather, one can achieve Real-Time capability by running a Fully
Preemptible Linux Kernel instead of the generic Kernel.
Fortunately, it is quite simple to do so.


## Compiling a Fully Preemptible Linux Kernel {#compiling-a-fully-preemptible-linux-kernel}

Fully-Preemptible Linux kernels are not available as precompiled
binaries; we need to compile from source.

First, choose a RT patch from this [mirror](https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/). I chose version
4.14.78-rt47 when writing this note. The lastest patch as of the
time of this writing is 4.19-rt3, unfornately does not work on my
machine running Ubuntu 16.04.

Note the kernel version you have choosen, download the corresponding
kernel source code from this [mirror](https://www.kernel.org/pub/linux/kernel/). Both steps can be done via the
terminal as below.

```sh
cd ~/Downloads
# download patch
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/4.14/patch-4.14.78-rt47.patch.xz
# download kernel
wget https://mirrors.edge.kernel.org/pub/linux/kernel/v4.x/linux-4.14.78.tar.xz
```

Unzipped the archives and patch the kernel with the RT patch.

```sh
tar xvf linux-4.14.78.tar.xz
cd linux-4.14.78
xzcat ../patch-4.14.78-rt47.patch.xz | patch -p1 --verbose
```

We now enable the option Fully Preemptible Kernel (RT). First,
install two necessary dependencies.

```sh
sudo apt-get install libncurses-dev libssl-dev
```

Configure the compilation by doing

```sh
make menuconfig
```

You will see a graphical user interface (GUI) show up in the
terminal. This GUI contains configurations for compiling the
Kernel. The bit that we need is called **Preemption Model**:

-   Navigate using arrow keys to **Processor type and feature**, then
    press Enter;
-   look for **Preemption Model**, press Enter;
-   choose **Fully Preemptible Kernel (RT)** option;
-   return to the first screen by pressing Esc multiple times;
-   save .config file, then exit.

Compile the Kernel and install.

```sh
make -j20
sudo make modules_install -j20
sudo make install -j20
```


### Remark for 5.x version kernels {#remark-for-5-dot-x-version-kernels}

The **Fully Preemptible Kernel (RT)** option seems to have been
shifted from **Processor type and feature** to **General
setup**. Thanks Iain!


## Obtaining generic and low-latency kernels {#obtaining-generic-and-low-latency-kernels}

Fully-Preemptible kernels are needed for time-critical applications
such as device and robot control. If the highest level of
"real-timeness" is not necessary, one can look at mainline generic
or low-latency kernels at [the kernel archive](https://kernel.ubuntu.com/~kernel-ppa/mainline/?C=N;O=D).  Low-latency versus
generic kernels are explained [here](https://unix.stackexchange.com/questions/553980/why-would-anyone-choose-not-use-the-lowlatency-kernel) and [here](https://docs.windriver.com/bundle/Wind%5FRiver%5FLinux%5FKernel%5FConfiguration%5Fand%5FBuild%5FLTS%5F1/page/dva1537990303268.html).

To find your architecture, run `arch`. Note that x86\_64 and amd64
are the same architectures.

To install pre-compiled kernels, download the required .deb
files. For example to install the generic 5.8.0 kernel, download the
following files:

```nil
linux-headers-5.8.0-050800_5.8.0-050800.202008022230_all.deb
linux-headers-5.8.0-050800-generic_5.8.0-050800.202008022230_amd64.deb
linux-image-unsigned-5.8.0-050800-generic_5.8.0-050800.202008022230_amd64.deb
linux-modules-5.8.0-050800-generic_5.8.0-050800.202008022230_amd64.deb
```

Install these debian files with `dpkg`.


## Configure the kernel {#configure-the-kernel}

Now that the kernel is compiled and installed, we need to tell the
bootloader about it. First, check that the new kernel can be found in
`/boot`, then run `sudo update-grub` to generate new grub config file
at `/boot/grub/grub.cfg`.

If you want to set a default kernel at boot or prevent the user from
selecting a different kernel, you will need to configure the grub
configuration file.  [Simple configuration](https://www.gnu.org/software/grub/manual/grub/grub.html#Simple-configuration) is the **easiest** way to
achieve this.  Edit `/etc/default/grub` then rerun `sudo
  update-grub` to regenerate the configuration. To see documentation
on the entries, use the command below for the documentation.

```sh
info -f grub -n 'Simple configuration'
```

Note that `GRUB_DEFAULT` specifies the default entries. If there are
sub menuentry, then the value of `GRUB_DEFAULT` must have the form
\`x>y\`, where x is the n-th main entry, and y is the n-th sub
entry. This is my current default grub configuration.

```nil
GRUB_DEFAULT="1>2"
GRUB_TIMEOUT_STYLE="countdown"
GRUB_TIMEOUT="0"
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```


## Try it out {#try-it-out}

And that's it. Reboot, remember to choose the patched Kernel in
Advanced Boot Setting, and enjoy! At this point `uname -a` should
print out the kernel name.


## References {#references}

Some useful reads I came across while working on this:

-   An extremely useful StackOverFlow [answer](https://stackoverflow.com/questions/51669724/install-rt-linux-patch-for-ubuntu)
-   [Intro to Real-Time Linux for Embedded Developers](https://www.linuxfoundation.org/blog/2013/03/intro-to-real-time-linux-for-embedded-developers/)
