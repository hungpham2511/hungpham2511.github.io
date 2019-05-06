---
title:  "Window manager, display manager and what?" 
date:   2019-5-5
tags: os
layout: single
published: true
---


`X` is the graphical server running on a linux machine. `X` does not
gives you the graphical user interface directly, but provides the
necessary interface to do that.


*Display managers* provide the log in screen. There are many different
diplay managers. A quick search gives: `gdm3`, `lightdm`, ...

*Window managers* provide the look and feel of the machine. The one I
use is *Awesomewm*.

You can freely change between display manager, window manager on a
linux box. The key takeaway is that you need to modify the script
`.xsession` to load the window manager you want, as well as other
programs. My current `.xsession` file look like this:

``` shell
#!/usr/bin/env bash

setxkbmap -option ctrl:nocaps  # Map control

ibus-daemon &
nm-applet &
dropbox start &

gnome-settings-daemon &
gnome-keyring-daemon &

exec awesome
```

# Creating a .desktop entry

Modern display managers can choose between different desktop
environment. Notice that the little button when you sign in that
allows you to choose between `gnome`, `kde` or `awesome`. That's it!

Each environment is defined by a *.desktop file in
`/usr/share/desktop`. See for example my entry:

```
[Desktop Entry]
Name=awesome
Comment=Highly configurable framework window manager
TryExec=awesome
Exec=awesome
Type=Application
Icon=/usr/share/pixmaps/awesome.xpm
Keywords=Window manager
```

A very good reference: https://wiki.ubuntu.com/CustomXSession

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
