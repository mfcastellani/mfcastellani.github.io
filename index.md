---
title: /
layout: default
permalink: /
---

## ‎¡Hola!

Bem vindo a meu blog. Veja os últimos artigos que publiquei abaixo:

<p>

{%- if site.posts.size > 0 -%}
  <ul>
    {%- for post in site.posts -%}
    <li>
      {%- assign date_format = "%d-%m-%Y" -%}
      [ {{ post.date | date: date_format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </li>
    {%- endfor -%}
  </ul>
{%- endif -%}

</p>
