---
title: "Todos os posts"
published: True
---


<p></p>

<ul>
  {% for post in site.posts %}
  <li>
    <a href="{{ post.url }}">{{ post.title }}</a>, {{ post.date | date: "%b %d, %Y"}}.
  </li>
  <p></p>
  {% endfor %}
</ul>