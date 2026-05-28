---
title:       "Python in VS Code on MacOS"
description: ""
tags:        [Python, MacOS, Jupyter, VS Code]
redirect_from:
 - /2024/06/18/python-environment-setup.html
---

Xcode is not a good IDE for Python.  It can be done[^4], but VS Code is a better plan.

- TOC
{:toc}

## Actionable Takeaways
- Python is installed along with the Xcode command line tools: `xcode-select --install`
- [Download VS Code](https://code.visualstudio.com/Download) from Microsoft's site.
- [Download the Jupyter extension](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter) in the VS Code Extension Marketplace.
- Create a virtual environment to work in (on the command line): `python3 -m venv env_name`
  - This will create a directory named `env_name` in the current directory.
- Activate the virtual environment: `source ./env_name/bin/activate`
  - You should see the command line prompt change to indicate your current environment.
- If not already installed, install Jupyter:  `python -m pip install ipykernel jupyter`
  - This takes about 250MB of space.
- Open the directory in VS Code and create a new Jupyter notebook.
- Click `Select Kernel` on the right side of the window.
  - Select `Python Environments`
  - You should see `env_name` listed.  Select it.

Use `deactivate` to get out of the virtual environment.

## Why `venv`?
It is a good idea to be able to containerize a project's Python modules for easy disposal.

Apple uses Miniconda in their developer documentation[^2], but I dislike installing extra software when `venv` does everything I need.



[^1]: [anaconda.com - Should I use Anaconda Distribution or Miniconda?](https://docs.anaconda.com/distro-or-miniconda/)

[^2]: [Apple Developer - Accelerated PyTorch training on Mac](https://developer.apple.com/metal/pytorch/)

[^3]: [anaconda.com - Quick command line install](https://docs.anaconda.com/miniconda/#quick-command-line-install)

[^4]: [Medium - Using Python in Xcode](https://betterprogramming.pub/using-python-in-xcode-aa30f6fbc8cd)

[^5]: [anaconda.com - Managing environments - Creating an environment with commands](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands)

[^6]: [anaconda.com - Managing environments - Activating an environment](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)

[^7]: [virtualstudio.com - Jupyter Notebooks in VS Code](https://code.visualstudio.com/docs/datascience/jupyter-notebooks)
