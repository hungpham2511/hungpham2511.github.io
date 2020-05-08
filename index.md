---
layout: single
author_profile: true
title: ""

---

# Hello there!

My name is *Hung Pham*. Currently I am working as a robotics engineer
developing cool robots for industrial automation at [Eureka
Robotics](https://www.therobotreport.com/archimedes-robot-eureka-robotics-human-like-grip/).

My interests lie in motion planning and control for robots. I
investigate algorithms that can plan dynamically feasible motions for
robots and also control strategies that allow optimal executions.

I used to spend a great deal of time recently working on
[toppra](https://github.com/hungpham2511/toppra) -- a library for
planning **fast** motion for industrial robots. The algorithm can
account for dynamic constraints such as joint torques, friction force
or suction force from suction cup. See [this
video](https://www.youtube.com/watch?v=b9H-zOYWLbY) for a
demonstration of a robot picking objects using suction cup at the
*fastest possible speed*. The relevant papers can be found [here][5]
and [here](https://arxiv.org/abs/1809.03151).

# Selected works

Below are some of my favourite projects.  A more complete list of
works can be found in [publications][3] or [google
scholar](https://scholar.google.com/citations?user=dszBAKsAAAAJ&hl=en).

{% for post in site.posts %}
{% if post.researchMeta %}

<div>
 <a href="{{ post.url }}"> {{ post.title }} </a>   {{ post.excerpt }}
</div>

{% endif %}
{% endfor %}



[1]: http://www.ntu.edu.sg/home/cuong/
[2]: https://en.wikipedia.org/wiki/Reinforcement_learning
[3]: {{site.url}}/research
[4]: {{site.url}}/software
[5]: https://arxiv.org/abs/1707.07239

