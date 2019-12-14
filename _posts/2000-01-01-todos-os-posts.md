---
title: "Todos os posts"
published: True
---

### Todos os posts:

<ul>
  {% for post in site.posts %}
  <li>
    <a href="{{ post.url }}">{{ post.title }}</a>
  </li>
  <p></p>
  {% endfor %}
</ul>