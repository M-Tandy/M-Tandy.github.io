---
layout: post
title: "test post"
date: 2023-03-20 11:18:30 +0100
categories: parentcat childcat
author: "M Tandy"
---
# A title
Some post contents

To list posts, use:

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

## testing post_url
[this post]({% post_url 2023-03-20-example-post %}) is located at {% post_url 2023-03-20-example-post %}.
This should output: /parentcat/childcat/2023/03/20/example-post.html