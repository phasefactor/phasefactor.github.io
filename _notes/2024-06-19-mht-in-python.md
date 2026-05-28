---
title:       "Extracting .MHT Contents with Python"
description: ""
tags:        [Python]
redirect_from:
  - /2024/06/19/mht-in-python.html
---

This is a dirty script to get the content out of `.mht` archives created by Internet Explorer.

It preserves directory structures but does not update any URLs inside of the dumped files.

It would probably be smarter to just use the `email` module to parse the file, but I wanted to understand how the `.mht` files were structured.

Only minimal error checking.

```
#!/usr/bin/env python3

from pathlib import Path
import re, sys, quopri, base64

INPUT_FILE = Path(sys.argv[1])

OUTPUT_DIR = INPUT_FILE.parent / INPUT_FILE.stem
OUTPUT_DIR.mkdir(exist_ok=True)

bound = ''

try:
    with open(INPUT_FILE) as f:
        for line in f:
            if line.startswith("\tboundary="):
                bound = '--' + line.split("boundary=")[1][1:-2]
                break

        name, encoding, lines = '', '', []

        for line in f:
            if name == '':
                if line.startswith('Content-Transfer-Encoding:'):
                    encoding = line.split('Content-Transfer-Encoding: ')[1][:-1]
                if line.startswith("Content-Location:"):
                    name = line.split("Content-Location: ")[1][:-1]
                    name = re.sub(r"[^A-Za-z0-9./-]", "_", name.split('//')[-1])
                print(name, encoding)
            else:
                if line.startswith(bound):
                    fullname = OUTPUT_DIR / name
                    
                    if fullname.exists():
                        print(f"{name} exists! Skipping...")
                    else:
                        print("Writing", name)

                        if encoding == 'quoted-printable':
                            qp = quopri.decodestring(''.join(lines))
                            qp = qp.decode('utf-8', errors='ignore')

                            fullname.parent.mkdir(parents=True, exist_ok=True)
                            fullname.write_text(qp, encoding='utf-8')
                        elif encoding == 'base64':
                            b64 = base64.b64decode(''.join(lines))

                            fullname.parent.mkdir(parents=True, exist_ok=True)
                            fullname.write_bytes(b64)

                    name, encoding, lines = '', '', []

                else:
                    lines.append(line)
                
    print('Done.')


except Exception as err:
    sys.exit(f'ERROR - Failure reading {sys.argv[1]}: {err}')

```

