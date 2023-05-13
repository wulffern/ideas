---
layout: page
title: Analog
permalink: /ana/ 
---

I'd love to simplify analog integrated circuit design

<ul>
  {% for tt in site.analog %}
    <li><a href="{{site.baseurl}}/{{ tt.url }}">{{ tt.title }}</a></li>

  {% endfor %}
</ul>


