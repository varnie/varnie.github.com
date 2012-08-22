---
layout: page
title: Блог о программировании
description: "programming blog"
keywords: "perl,programming,coding,challenge,examples,howto,tutorial"
tagline: Supporting tagline
---
{% include JB/setup %}

{% if site.posts.size > 0 %}
{% for post in site.posts limit:5 %}
  {% include show_post.html %}
{% endfor %}

<h2>Старые посты</h2>

<ul class="posts">
  {% for post in site.posts offset:5 %}
    <li><span class="date">{{ post.date | date: "%b %d, %Y" }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

{% else %}
пока ничего нет
{% endif %}
