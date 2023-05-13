---
title: Why
layout: home
---


I have ideas, most of the ideas amount to nothing, but some stuck, and been
valuable.

The idea is easy for me, but what takes time is to figure out whether it's a
good idea, or a bad idea.

I find that writing down the idea helps me think. The writing allows my mind to
explore the idea deeply, and see if there are caveats, or potential issues.

On this page I post the things that I'd love to dig into.

## Analog 

<ul>
  {% for tt in site.analog %}
    <li><a href="{{site.baseurl}}/{{ tt.url }}"> {{ page.date | date: '%B %d, %Y' }} {{ tt.title }}</a></li>

  {% endfor %}
</ul>
