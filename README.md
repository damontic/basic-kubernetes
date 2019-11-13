# Kubernetes Basics
Slides explaining Kubernetes in an easy way.

## Generate slides
To generate the slides you can use the pandoc md to slides alternatives.
For example to generate the reveal.js slides execute:

In spanish:
```bash
$ pandoc -s -t revealjs KubernetesBasico.md -o KubernetesBasico.html --slide-level 4 --template=templates/template.html --variable revealjs-url="https://revealjs.com" --css css/seven.css
```

In english:
```bash
$ pandoc -s -i -t revealjs KubernetesBasics.md -o KubernetesBasics.html --slide-level 4 --template=templates/template.html --variable revealjs-url="https://revealjs.com" --css css/seven.css
```
