---
title: Todos Posts
layout: default
---

<!-- All posts sorted by date -->
<ul class="my-posts">
{% for post in site.posts %}
    <li><a href="{{ post.url | absolute_url }}">{{ post.title}}</a>
    <span class="published-date">{{ post.date | date: "%b %-d, %Y" }}</span>
    </li>
{% endfor %}
</ul>
