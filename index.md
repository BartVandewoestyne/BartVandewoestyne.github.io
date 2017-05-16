---
layout: default
title: Bart Vandewoestyne's blog
---

### Home Page
This is my first attempt at setting up a website through Github pages.

### [About Me]({{ site.url }}/about)

### Recent Blog Posts
{% for post in site.posts limit:5 %}
* {{ post.date | date_to_string }} [{{ post.title }}]({{ post.url }})
{% endfor %}
