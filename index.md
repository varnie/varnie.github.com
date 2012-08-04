---
layout: page
title: Блог о программировании
tagline: Supporting tagline
---
{% include JB/setup %}

Всем привет. здесь буду постить заметки на интересующие меня технические темы.

Список постов:
---------------------
{% if site.posts.size > 0 %}
<ul class="related">
{% for post in site.posts %}
<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
{% else %}
пока ничего нет
{% endif %}


