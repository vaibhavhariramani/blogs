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
{% for post in site.posts %}
  <li>
   <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
   <p>{{ post.excerpt }}</p>
  </li>
{% endfor %}
</ul>