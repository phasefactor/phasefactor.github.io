---
title:       "YAML Syntax Cheat Sheet"
description: ""
tags:        [YAML, Cheat Sheet]
redirect_from:
 - /2024/06/20/yaml-cheat-sheet.html
---

Summary of CloudBees' YAML tutorial[^1].

- TOC
{:toc}

## Structure and Formatting
- YAML documents start with `---` and ends with three periods `...`.
  - The end operator is usually optional.
- Whitespace is part of YAML's formatting. 
- Unless otherwise indicated, newlines indicate the end of a field. 
- YAML documents are structured with indentation. 
  - The indentation level can be one or more spaces. 
  - Tabs are forbidden for indentation. 

- Comments use a `#`, and can be on separate lines or with code.

- *Advice:* Enclose strings in quotes, leave numbers unquoted, and let the parser figure it out.

## Data Types
- Key-value pairs are YAML's basic building block. 
  - Every item in a YAML document is a member of at least one dictionary. 
- The key is always a string. 
- The value is assumed to be a scalar by default, but can be any data type. 

### Numbers
- YAML recognizes floating point and integer numbers.  
  - Integers can be expressed in decimal, hexadecimal (`0x` prefix), or octal (leading `0`).
  - Floating points expressed as exponentials are understood (e.g.: `12.3015e+05` equals `1230150.0`).
- Special values are: `.inf`, `-.Inf`, and `.NAN`.


### Strings
- YAML strings are Unicode encoded.  
- Quotes are not required, but will force the string data type if used.
  - Double quotes are required if escape sequences (e.g.: `\n`) need to be handled.
  - Single quotes will not handle escape sequences, but will force the string data type.
- Multi-line strings can be specified by using the fold (greater than, `>`) or block (pipe, `|`) character following the key name and colon.
  - Multi-line strings using the fold character ignore newlines.
  - Multi-line strings using the pipe character preserve the newlines.
  - Multiline values may end with whitespace. 
    - To save the last character, add a preserve operator (plus, `+`) following the fold or block operators.
    - To strip the character, add a strip operator (minus, `-`) following the fold or block operators.


### Boolean
- YAML indicates boolean values with the keywords True, On and Yes for true. False is indicated with False, Off, or No.


### Arrays
- Arrays/lists can be specified on a single line or multiple lines.
  - Single line: place items inside of square brackets (`[` and `]`), separated by a comma.
  - Multiple lines: indent one level and prefix each item with `- `.
    - The multiple line format is useful for lists that contain complex objects instead of scalars.
- An array can contain any valid YAML value--i.e.: the items do not have to be the same type.

### Dictionaries
- Dictionaries can also be specified in single- or multi-line formats.
  - Single line dictionaries use curly-braces (`{` and `}`).
  - Multiple line dictionaries are just key-value pairs, at the same indentation level, on new lines.

### Null
- Null values can be specified with a tilde(~) or the unquoted null string literal.



[^1]: [CloudBees - YAML Tutorial](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started#key-value-pairs-and-dictionaries)













