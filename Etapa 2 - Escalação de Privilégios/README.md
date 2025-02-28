# Etapa 2 - Escalação de Privilégios

Nessa etapa você precisa escalar os próprios privilégios ou acessar a conta do administrador. A última opção é mais comum.

<br>

Possíveis vulnerabilidades:
* SQL Injection
* CSRF - Cross-Site Request Forgery
* OAuth
* JWT
* Vulnerabilidades de Controle de Acesso
* Prototype Pollution
* Outros

<br>

> [!WARNING]  
> Não delete o usuário carlos!!! Pode ser que quando você acessar a conta como administrator, você tenha a opção de fazer isso. Se você sem querer fazer isso (como eu), ainda não é o fim, pois isso deleta somente o usuário da aplicação e não do sistema operacional. O problema é que se você perder a sessão do admin, não tem mais como obter acesso a ele dependendo da vulnerabilidade que foi utilizada.
>
> A melhor dica é: Ao logar como administrator, troque o e-mail para o domínio do exploit server, desse modo você conseguirá trocar a senha dele e manter a sessão pelo resto do exame.
