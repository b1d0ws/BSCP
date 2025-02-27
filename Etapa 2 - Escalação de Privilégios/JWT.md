# JWT

A extensão JWT Editor ajuda bastante para resolver alguns laboratórios.

Probabilidade: $${\color{green}baixa/média}$$.

<br>

## Laboratórios

**JWT Authentication Bypass via Unverified Signature**

Simplesmente altere o JWT.

<br>
<br>

**JWT authentication bypass via flawed signature verification**

Altere o token e remova a parte da assinatura, mantendo o último ponto final.

<br>
<br>

**JWT Authentication Bypass via Weak Signing Key**

Atenção: Chaves frases são identificas pelo scanner passivo do BurpSuite.

Utilize o Hashcat para descobrir a chave que assina o token com essa [wordlist](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list):
```
hashcat -m 16500 -a 0 <full_jwt> /usr/share/wordlists/rockyou.txt
```

<br>
<br>

**JWT Authentication Bypass via JWK Header Injection**

1. Com a extensão JWT Editor carregada, na aba do Repeater, vá para **JWT Editor Keys**.
2. [Gere uma nova chave RSA](https://portswigger.net/burp/documentation/desktop/testing-workflow/session-management/jwts#adding-a-jwt-signing-key).
3. Envie uma request válida do histórico contendo um JWT para o Repeater
4. No message editor, mude para a aba JSON Web Token e [modiique o payload do token](https://portswigger.net/burp/documentation/desktop/testing-workflow/session-management/jwts#editing-jwts) como bem quiser.
5. Clique **Attack**, e selecione **Embedded JWK**. Quando solicitado, insira a nova chave RSA gerada.
6. Envie a request para ver como o servidor responde.

<br>
<br>

**JWT Authentication Bypass via Kid Header Path Traversal**
1. Crie uma nova **chave simétrica** e defina o valor "k" como um byte nulo em base64: `AA==`.
2. No header do token, defina o parâmetro `kid` para `../../../../../../../dev/null`.
3. Assine o token com essa nova chave.
