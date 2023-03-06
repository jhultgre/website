---
title: Joe Hultgren - Technical Artist
---

<ul>
  {% for post in site.posts limit 4 %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>