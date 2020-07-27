---
title: /
layout: default
permalink: /
---

## ‎¡Hola!

Bem vindo a meu blog. Veja os últimos artigos que publiquei abaixo:

<p>

{%- if site.posts.size > 0 -%}
  <ul style="margin-left:-40px">
    {%- for post in site.posts limit:6 -%}
    <li style="list-style-type:none">
      {%- assign date-format = "%d-%m-%Y" -%}
      <a href="{{ post.url | relative_url }}">[ {{ post.date | date: date-format }} ] {{ post.title | escape }}</a>
      <br>
      <ul>
          <li style="list-style-type:none">
              {{ post.description | escape }}
          </li>
          <li style="list-style-type:none; font-size:80%; font-color:light-gray;">
              Categoria: {{ post.tags }}
          </li>
      </ul>
    </li>
    {%- endfor -%}
  </ul>
{%- endif -%}

</p>
