---
title:       "Using `<details>` with GFM and kramdown/Jekyll"
description: "The `details' tag is very useful, but GitHub Flavored Markdown and kramdown treat it slightly differently."
tags:        [Jekyll, kramdown]
modified:    2024-01-25
redirect_from:
  - /2024/01/07/details-with-kramdown.html
---

The `<details>` tag is a useful addition that came with HTML5, but markdown inside of a `<details>` tag is treated differently by GitHub and kramdown (which is used by Jekyll).

- TOC
{:toc}


## For GitHub
GitHub just needs blank lines between any markdown that should be parsed and the `<details>`, `<summary>`, or `</details>` tags:
```
<details><summary>A summary of the contents.</summary>

- a markdown
- list to parse

</details>
```

## For kramdown / Jekyll
The `<details>` tag is a block element[^1] and by default kramdown[^2] does not parse markdown inside of HTML block elements.  Parsing can be enabled in three different ways.

### For a specific block element 
Add the attribute `markdown="1"` to the element[^3]:
```
<details markdown="1"><summary>A summary of the contents.</summary>
Some **longer** and more *detailed* contents that includes markdown...
</details>
```

### For a whole markdown file
Add `parse_block_html: true` to the `kramdown:` options in the file's front matter:
```
---
title: Some Title
kramdown: 
  parse_block_html: true
---
```

### For all documents on a Jekyll site
Add `parse_block_html: true` to the `kramdown:` options in `_config.yml`:
```
markdown: kramdown

kramdown: 
  parse_block_html: true
```



[^1]: [MDN Documentation Page for \<details>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details)
[^2]: [kramdown Documentation](https://kramdown.gettalong.org/options.html)
[^3]: [Related Stack Overflow Question](https://stackoverflow.com/questions/22291211/jekyll-how-to-get-markdown-parsing-inside-blocks-using-kramdown)
