# Kubernetes Basics
Slides explaining Kubernetes in an easy way.

## Generate slides
To generate the slides you can use the pandoc md to slides alternatives.
For example to generate the reveal.js slides execute:

```bash
$ pandoc -s --mathjax -i -t revealjs Kubernetesbasics.md -o kubernetesbasics.html --slide-level 3
```
