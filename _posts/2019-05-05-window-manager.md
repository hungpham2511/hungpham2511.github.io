---
title:  "Setup a custom Desktop Environment for Linux." 
date:   2019-5-5
tags: os
layout: single
published: true
---
This is a short note on setting up a Desktop Environment.

[`X`](https://en.wikipedia.org/wiki/X_Window_System) is the graphical
server running on a Linux machine. `X` does not gives you the
graphical user interface directly, but provides the necessary
interface to do that.

*Display managers* provide the log in screen. There are many different
display managers. The two most popular display managers on the Ubuntu
Linux distribution is `gdm3` and `lightdm`.

On the other hand, *Window managers* provide the look and feel of the
OS. Here, a window refers to the GUI of an application such as `emacs`
or `google-chrom`. A *window manager* manages these windows by
providing methods for arranging, hiding and displaying them. The
window manager I use is [*awesomewm*](https://awesomewm.org).

When you start a Ubuntu machine, you will first be greeted with the
display manager. This is the screen that ask for your user name and
password. At this screen you can choose an existing X session
configuration and start the corresponding window manager.

You can freely change between display manager, window manager on a
Linux box. In fact, you probably should. I notice a marked improve in
productivity after switching to `awesomewm` from the default
[`gnome`](https://www.gnome.org/) window manager. In addition you can
choose applications to initialize at the beginning of the X session or
perform some setup. 

The simplest way to change to a new window manager and starting some
programs during initialization is to create a `.xsession` scripts. My
current `.xsession` file look like this:

``` shell
#!/usr/bin/env bash

setxkbmap -option ctrl:nocaps  # Map Caps Lock to control key

ibus-daemon &
nm-applet &
dropbox start &

gnome-settings-daemon &
gnome-keyring-daemon &

exec awesome
```

This file is quite straightforward. First I map the Caps Lock key to
another Control key. The next 5 lines start useful programs as
daemons. Finally the window manager is started.

Next we need to be able to select this new configuration somehow. This
is done by creating a .desktop entry.  Modern display managers can
choose between different desktop environment. Notice that the little
button when you sign in that allows you to choose between `gnome`,
`kde` or a custom desktop environment of your choosing. To create a
new entry that starts the desktop environment we defined in the
`.xsession` script, create a new file name `custom.desktop` in
`/usr/share/xsessions` with the following lines. You will probably
need to use root permission.

```
[Desktop Entry]
Name=custom
Comment=Hung's a custom X session 
Exec=/etc/X11/Xsession
TryExec=/etc/X11/Xsession
Type=Application
Icon=/usr/share/pixmaps/awesome.xpm
Keywords=Window manager
```

Note that the file `Name` is the name of the Desktop entry that will
be shown in the selection panel at the display manager.  `Exec` and
`TryExec` contains the executable to run that actually start the
desktop environment. Here by calling the Xsession command, we start
with the default script `~/.xsession`. More details regarding the
`/etc/X11/Xsession` script can be found
[here](http://manpages.ubuntu.com/manpages/xenial/man5/Xsession.5.html).

You are now all set. Log out (and probably restart the Linux box). You
will now see a new window manager selection called `custom`. Select it
and enjoy.

# Starting a single application in a Xsession

It is possible to load only a single application in a X session. Just
change the `Exec` field. For example, to start only google chrome, we
can do:

```
[Desktop Entry]
Name=Chrome
Comment=Only start Google Chrome.
Exec=google-chrome
TryExec=google-chrome
Icon=
Type=Application
DesktopNames=slack
```

However, most applications are not designed for this use-case. Hence,
do expect to do extra work to get everything working
perfectly. There are some alternatives:

- Start a minimal Window Manager such as awesomewm or openbox with all
bar, etc tripped off.

- Configure the application Kiosk-style
  ([link](https://tutorials.ubuntu.com/tutorial/electron-kiosk#0))
