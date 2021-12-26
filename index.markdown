---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title: Home
---
# Welcome

I'm Ilkin Gasimov, known as [smodnix](/about). This is my technical blog, where I discuss mostly related to **web application security**. Also planning to write my personal notes and thoughts about various topics. 

## Writing
<ul>
  {% for post in site.posts limit:5 %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a> - {{ post.date | date_to_string: "ordinal", "US" }}
    </li>
  {% endfor %}
</ul>

You can check my open source projects at [github.com/smodnix/](https://github.com/smodnix){:target="_blank"}{:rel="noopener noreferrer"}.
