---
title: "Transforming notes to blog posts"
author: ["Hung Pham"]
date: 2020-06-12
categories: ["blog", "hugo"]
draft: false
---

## Why?

I keep a lots of notes containing different ideas, thoughts and
exploratory pieces; but they are not well maintained. Hence, they are
not as useful for myself or for others as they can be. I think it would
be great if these notes can be polished and made available online.

Currently my [personal site](https://hungpham2511.github.com/) is made
with [Jekyll](https://jekyllrb.com/docs/posts/), which is a really
powerful framework for static site and definitely can do things that I
surely would neve need. Jekyll, however, does not support generating org
notes to blog posts. I also find implementing this functionality
non-trivial, and thus decided to even try it out.

## Hugo

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

## Generating blog post from `.org` files {#generating-blog-post-from-dot-org-files}

For each org note that is to be published, fill in the front matter as
shown below. When satisfied with the note, use \`org-export\` (C-c C-e H
h) to generate the markdown post. After doing this, there should be a
new mardown file in \`publish\content\posts\` with the same name as the
org file but in hugo markdown format.

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
