---
layout: default
title: "Home"
---

# Welcome to My Blog

<div>
  {% for post in site.posts %}
    <div>
      <h1>{{ post.title }}</h1> 
      <div>{{ post.content }</div>
  {% endfor %}
</div>
