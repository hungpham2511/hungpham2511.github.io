---
title:  "Managing dotfiles"
date:   2018-11-22 23:24:01 +0800
categories: setup
tags: emacs vim dotfiles
layout: single
published: true
---

In Ubuntu, or more generally Unix-like Operating systems, applications
can be configured using text files that start with a dot
(e.g. `.tmux.conf`) and are stored in the HOME directory or
HOME/.config directory. People like to configure their dotfiles to fit
their own workflow, and backup these files frequently.

Dotfiles are by definition personal; hence, a *dotfiles management
system* is usually personal.  A dotfiles management system that works
for a friend might not work for you and what work for you probably
won't work for her.  Note that here, by a management system I mean to
say a system for storing, backing up and restoring these
configurations files.

Let's not skip steps and ask: what are the requirements for a good
management system.  Mines are:
- fast setup on a new computer: ideally have a *light* mode that
  allows very minimal setup and a *dark* mode that setup the whole
  environment;
- minimal dependencies: should not contain task-specific dependencies;
  for example, I often work with [ROS](http://www.ros.org/) so
  obviously ROS library are dependencies. However, they are specific
  to writing codes for robots. Hence, the setup script should not
  install ROS libraries.
- editors setting: nothing beats emacs for general usage and vim for
  editing on the terminal;
- window manager: [awesome window manager](https://awesomewm.org/) is
  a must;
- terminal-related utilities: tmux, urxvt settings.
- easy versioning, ideally via github.

Relatively simple requirements. But I was not able to find a good
solution for sometimes. A few solutions I have tried are:
- the *manual* method: copy and paste the dotfiles to and fro Dropbox;
- bare git repo method: [see
here](https://developer.atlassian.com/blog/2016/02/best-way-to-store-dotfiles-git-bare-repo/).

Both methods did not work so well. No very suprising with the first
one because it is *manual* (we want something more elegant, don't
we). The second method works quite well until I wanted to have
different setup like the light/full modes. One possible solution is to
use different branch for different modes, but maintaining several
branches at the same times is less than ideal. Also, a bare git repo
does not play nice with [magit](https://magit.vc/), which is my git
interface of choice for its great functionalities and emacs, as well
as the zsh shell's autocomplete functionality.

Only recently, I stumbled upon
[stow](https://www.gnu.org/software/stow/manual/stow.html) and found
the ideal method for storing dotfiles.

# Store dotfiles with stow and git

stow works like this. Suppose you have the following file directory:
```
\home\hung
|-- dotfiles
   |-- app
     |-- .config
       |-- super_cool_app
         |-- config.file
```

If you change directory to `dotfiles` and run `stow app`, a symlink
will be created in the home directory and result a file directory that looks
like this:
```
\home\hung
 |-- .config
   |-- super_cool_app
     |-- config.file
```
That is, all files in the `app` folder will be symlinked as if `app`
is the HOME directory. 

Isn't this neat! We can now store all dotfiles
in a folder called `dotfiles` in the HOME directory that contains
different sub-folders corresponding to different applications. Then,
depending the need, an application's dotfile can be applied by doing
```shell
cd ~\dotfiles && stow <app>  # <app> is emacs, vim, etc...
```

For my own dotfiles I create two shell scripts corresponding to two
modes. In the scripts different stow commands are ran, depending on
the particular mode. This structure also makes it trivial to version
all dotfiles using `git`, backup and restore as you wish!

My own dotfiles live here [`github.com/hungpham2511/dotfiles`]. Feel
free to take a look.




