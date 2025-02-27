# Etapa 1 - Acesso Inicial

Nessa etapa você terá que conquistar o acesso ao usuário de **baixo privilégio**. 

Você não terá nenhuma credencial e normalmente o usuário se chama **carlos**, mas isso não é uma regra.

Se existir alguma vulnerabilidade de enumeração de usuários, o username pode ser outro.

Caso identifique user enumeration ou precise realizar brute-force, utilize essas duas wordlists:
* [Authentication Lab Usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
* [Authentication Lab Passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

<br>

Possíveis vulnerabilidades:
* XSS - Cross-Site Scripting
* Vulnerabilidades em DOM
* Autenticação
* Web Cache Poisoning
* Ataques de Host Header
* HTTP Request Smuggling
