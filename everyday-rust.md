---
title: Everyday Rust
layout: default
permalink: /everyday-rust
---

## Everyday Rust

Uma coletanea de artigos sobre Rust.

<p>

{%- if site.posts.size > 0 -%}
  <ul>
    {%- for post in site.posts -%}
        {%- if post.tags[0] == "Rust" -%}
            <li>
                {%- assign date-format = "%d-%m-%Y" -%}
                [ {{ post.date | date: date-format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
            </li>
        {%- endif -%}
    {%- endfor -%}
  </ul>
{%- endif -%}

</p>
