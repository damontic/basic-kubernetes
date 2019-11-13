# Kubernetes Basics
Slides explaining Kubernetes in an easy way.

## Generate slides
To generate the slides you can use the pandoc md to slides alternatives.
For example to generate the reveal.js slides execute:

For now two files for each language...

In spanish:
```bash
$ pandoc -s -t revealjs --template="templates/template.html" KubernetesBasico.md -o KubernetesBasico.html --slide-level 4  --variable revealjs-url="https://revealjs.com"  --variable theme="white" --no-highlight --css css/seven.css
```

In english:
```bash
$ pandoc -s -t revealjs --template="templates/template.html" KubernetesBasics.md -o KubernetesBasics.html --slide-level 4  --variable revealjs-url="https://revealjs.com"  --variable theme="white" --no-highlight --css css/seven.css
```
