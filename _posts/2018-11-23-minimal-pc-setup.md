---
title:  "Managing dotfiles"
date:   2018-11-22 23:24:01 +0800
categories: setup
tags: emacs vim dotfiles
layout: single
published: true
---


<!-- # Outline: -->

<!-- ## overview -->
<!-- - overview dotfile -->
<!-- - This post introduces my dotfiles system: -->
<!-- - my requirements ?? -->
<!-- - the ideal solution : stow -->

<!-- ## how stow works -->
<!-- - examples -->
<!-- - what happen? -->
<!--   - stow automatically symlink all files etc.. -->
<!-- - the idea is to store all configuration files in the proper directory -->
<!--   in a folder -->

<!-- ## my dotfile configuration system -->
<!-- - folder overview -->
<!-- - basic usage -->
<!-- - script usage on a new computer -->
<!-- - backup with git -->

# Overview

In Unix-like Operating Systems, we can configure most applications
with text files (e.g. `.tmux.conf` for `tmux`, `init.el` for emacs).
How to backup these files--the *dotfiles*--and restore them
subsequently is an usual concern.

In this post I describe my system for backup and restoring my
dotfiles.

I have three basic requirements when it comes to dotfiles:
- fast setup on a new computer;
- minimal dependencies; 
- easy versioning, ideally via github.

These are relatively simple requirements. Suprisingly, I was not able
to find a good solution for sometimes. Two solutions I have tried are:
- the *manual* method: copy and paste the dotfiles to and fro Dropbox;
- bare git repo method: [see
here](https://developer.atlassian.com/blog/2016/02/best-way-to-store-dotfiles-git-bare-repo/).
Both methods did not work very well. 

Only recently, I stumbled upon
[stow](https://www.gnu.org/software/stow/manual/stow.html) and found
that it is the ideal solution. I will describe my dotfiles management
system later, after briefly showing you how `stow` work.

# How `stow` works

Suppose you have the following file directory:

```
\home\hung
|-- dotfiles
   |-- app
     |-- .config
       |-- super_cool_app
         |-- config.file
```

If you change directory to `dotfiles` and run `stow app`:
``` shell
cd ~/dotfiles
stow app
```


The result is that all files in the folder `app` will be 
[symlinked](https://www.cyberciti.biz/faq/creating-soft-link-or-symbolic-link/) to
the home directory `/home/hung` with *the same folder structure*. In
this particular example, we will have a directory that look like this:
```
\home\hung
 |-- .config
   |-- super_cool_app
     |-- config.file
```

# My dotfiles configuration system

My system relies entirely on `stow`.

I store all dotfiles in a folder called `dotfiles` in the `HOME`
directory. For each application, I create a sub-folder with the
corresponding dotfiles in each. Then, suppose I want checkout the
dotfiles for the application `<app>`:
```shell
cd ~\dotfiles && stow <app>  # <app> is emacs, vim, etc...
```

For my own dotfiles I create two shell scripts corresponding to two
modes. In the scripts different stow commands are ran, depending on
the particular mode. This structure also makes it trivial to version
all dotfiles using `git`, backup and restore as you wish!

My own dotfiles live here [`github.com/hungpham2511/dotfiles2`]. Feel
free to take a look.


*Updated*: 2019-Feb-18


