---
title: Everyday Ruby
layout: default
permalink: /everyday-ruby
---

## Everyday Ruby

Uma coletanea de artigos sobre Ruby.

<p>

{%- if site.posts.size > 0 -%}
  <ul>
    {%- for post in site.posts -%}
        {%- if post.tags[0] == "Ruby" -%}
            <li>
                {%- assign date-format = "%d-%m-%Y" -%}
                [ {{ post.date | date: date-format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
            </li>
        {%- endif -%}
    {%- endfor -%}
  </ul>
{%- endif -%}

</p>
