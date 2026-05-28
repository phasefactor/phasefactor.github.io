---
title: "Home"
---

{{ site.description }}

[Search by tags](\tags.html) or use the chronological list:

{%- for post in site.posts -%}
{%- unless post.draft == true %}

## [{{ post.title }}]({{post.url | prepend: site.baseurl }})

{{ post.excerpt }}

{% endunless -%}
{%- endfor -%}

{% raw %}<style>sup[role='doc-noteref'], div[role='doc-endnotes'] { display:none; }</style>{% endraw %}
