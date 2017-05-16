---
layout: default
title: [Thoughts on Software Development]
---

# Blog Posts
{% for post in site.posts %}
* {{ post.date | date_to_string }} [{{ post.title }}]({{ post.url }})
{% endfor %}
