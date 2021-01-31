---
title: "Transforming notes to blog posts"
author: ["Hung Pham"]
date: 2020-06-12
categories: ["blog", "hugo"]
draft: false
---

## Why?

As part of my daily routine, I keep and write notes that are ideas,
thoughts and exploratory pieces. These notes, however, are offline and
are [org](https://orgmode.org/) files; hence they are not as useful for
myself or for others as they can be. It would be ideal if I can publish
selected notes on my personal website.

Currently my [personal site](https://hungpham2511.github.com/) is made
with [Jekyll](https://jekyllrb.com/docs/posts/), which is a framework
for generating static site from markdown documents. It would be most
convenient What is truly useful for me, who uses emacs and org-mode for
all of my notes, is that one can generate markdown documents from org
files very easily.

## Generating markdown posts from org files for Jekyll

For each org note that is to be published, fill in the front matter as
shown below. When satisfied with the note, use `org-export` (C-c C-e H
h) to generate the markdown post. After doing this, there should be a
new mardown file exported to the jekyll `_posts` folder with the same
name as the org file but in markdown format.

``` org
#+TITLE: Transforming note to blog posts
#+HUGO_SECTION: posts
#+HUGO_CATEGORIES: blog hugo
#+HUGO_BASE_DIR: publish
#+DATE: 2020-06-12
#+toc: headlines 2
```

Alternatively, include the setup file:

``` org
#+TITLE: Multi-view geometry in Robotics
#+setupfile: ./hugo-setup.org
#+DATE: 2020-07-31
#+toc: headlines 2
```

I actually use my own fork of ox-hugo to do this generation because I
actually want it to work with jekyll. See the fork
[here](https://github.com/hungpham2511/ox-hugo).

## Handling citations

Using pandoc and pandoc-citeproc can be used to generate
[citation](https://ox-hugo.scripter.co/doc/pandoc-citations/). The org
file need to define a bibtex file

``` nil
#+hugo_pandoc_citations: t
#+bibliography: test.bib
```

To cite an article, use the below syntax. This is how a citation looks
like: Illingworth and Kittler (1988). And this is the verbatim source
code.

``` nil
@illingworth1988survey
```

## Related

[HowToTakeSmartNotesBook]({{< relref "20200322130256_how_to_take_smart_notes_book" >}})

## On using Hugo

[Hugo](https://gohugo.io/) is another framework for creating static
site, that just happens to have a working org notes to blog posts
open-source implementation.
[ox-hugo](https://github.com/kaushalmodi/ox-hugo) can be used to
generate new blog posts in hugo-style markdown. The concept is quite
simple. The org files are first exported to markdown files. Then hugo
builds the html site from these markdown files, which is then deployed
using Github pages.

To preview a site made with hugo, one simply do

``` sh
cd ~/org/publish && hugo serve
```

This starts a server publishing the built site locally, with
auto-refresh and other goodies. Very pleasant to work with.

Now I have gave Hugo a try. In the end, I couldn't really find a
configuration that I am happy with. So I went back to jekyll.

## References {#references}

<div id="refs" class="references hanging-indent">
  <div></div>


<div id="ref-illingworth1988survey">
  <div></div>

Illingworth, John, and Josef Kittler. 1988. "A Survey of the Hough
Transform." *Computer Vision, Graphics, and Image Processing* 44 (1):
87--116.

</div>

</div>
