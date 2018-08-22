---
layout: default
---
<h1>{{ page.title }}</h1>
<p class="meta">{{ page.date | date_to_string }}</p>

<div class="post">
  {{ content }}
</div>

<div>
<h2>Comments</h2>
{% include disqus.html %}
</div>
