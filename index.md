---
title: "Home"
---

{% assign all_items = site.posts | concat: site.notes | sort: "date" | reverse %}

{{ site.description }}

[Search by tags](\tags.html) or use the chronological list:

{%- for post in all_items -%}
{%- unless post.draft == true %}

## [{{ post.title }}]({{post.url | prepend: site.baseurl }})

{{ post.excerpt }}

{% endunless -%}
{%- endfor -%}

{% raw %}<style>sup[role='doc-noteref'], div[role='doc-endnotes'] { display:none; }</style>{% endraw %}
