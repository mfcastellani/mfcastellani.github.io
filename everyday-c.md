---
title: Everyday C
layout: default
permalink: /everyday-c
---

## Everyday C

Uma coletanea de artigos sobre C.

<p>

{%- if site.posts.size > 0 -%}
  <ul>
    {%- for post in site.posts -%}
        {%- if post.tags[0] == "C" -%}
            <li>
                {%- assign date-format = "%d-%m-%Y" -%}
                [ {{ post.date | date: date-format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
            </li>
        {%- endif -%}
    {%- endfor -%}
  </ul>
{%- endif -%}

</p>
