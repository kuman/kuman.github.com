---
layout: index
title: Kuman Blog
tagline: Supporting tagline
---
{% include JB/setup %}

<dl class="posts">
  {% for post in site.posts %}
  <dt>
    <span>{{ post.date | date_to_string }} &raquo;
    <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
  </dt>
  <dd>
  {{ post.content | strip_html | truncatewords: 5 | xml_escape }}
  </dd>
  {% endfor %}
</dl>
