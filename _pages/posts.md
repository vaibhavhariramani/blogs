---
layout: posts
permalink: /posts/
title: "posts"
entries_layout: grid
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