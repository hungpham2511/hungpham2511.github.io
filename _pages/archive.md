---
layout: single
author_profile: false
title: ""
permalink: /archive/
---
# Archive

{% for tag in site.categories %}
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}

{% assign postsByYearMonth = site.posts | group_by_exp:"post", "post.date | date: '%Y %b'"  %}
{% assign postsByYear = site.posts | group_by_exp:"post", "post.date | date: '%Y'"  %}

<!-- {{ postsByYearMonth }} -->
<!-- {{ postsByYear }} -->

{% for yearMonth in postsByYear %}
## {{ yearMonth.name }}

{% for post in yearMonth.items %}
  - {{ post.date | date: '%Y/%m/%d'}} [{{ post.title }}]({{ post.url }})
{% endfor %}

{% endfor %}
