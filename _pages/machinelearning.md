---
layout: posts
permalink: /machine-learning/
title: "Machine Learning posts"
author_profile: true
header:
  image: "/images/fort point.png"
---

<ul>
{% for post in site.categories.news %}
  <li>
   <a href="">{{post.title }}</a>
   <p>{{news.meta}}</p>
  </li>
{% endfor %}
</ul>