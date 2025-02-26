# Estudos BSCP - Burp Suite Certified Practitioner

## Introdução
Esse repositório majoritariamente compila dois outros repositórios que tem relação com a BSCP:
* [Burp Suite Certified Practitioner Exam Study - botesjuan](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study)
* [BurpSuiteCertifiedPractitioner - DingyShark](https://github.com/DingyShark/BurpSuiteCertifiedPractitioner)

Dou grande crédito para os dois autores desses repositórios. A diferença desses links para esse repo é que aqui você vai encontrar o conteúdo em português e também centralizado.

Outros reviews e write-ups foram utilizados para compor esse guia, como:
* [BSCP Methodology](https://bscpcheatsheet.gitbook.io/exam)
* [How to Crush BSCP Exam in 75 Mins — BSCP Review - Ossama Yasser](https://medium.com/@ossamayasserr/how-to-crush-bscp-exam-in-75-mins-bscp-review-0b207a17e26d)
* [Burp Suite Certified Practitioner Exam Review - Micah Van Deusen](https://micahvandeusen.com/burp-suite-certified-practitioner-exam-review/)

<br>

## Exame
O exame da BSCP é composto por 3 etapas e você encontrará outros arquivos que contém as possíveis vulnerabilidades de cada etapa.

1. Na primeira etapa, é necessário obter acesso a qualquer usuário de pouco privilégio.
2. Na segunda etapa, é preciso obter permissões administrativas, seja escalando privilégios, seja obtendo a acesso a conta do administrador.
3. Na terceira etapa, é necessário ler o arquivo localizado em `/home/carlos/secret` e submeter o valor obtido como solução da aplicação.

Não perca tempo testando os cookies `_lab` e `_lab_analytics`, eles não serão vulneráveis.

Todo aplicativo terá pelo menos um usuário ativo e logado. Esse usuário visitará a página home da aplicação a cada 15 segundos e também clicará em qualquer link e e-mail que ele receber da aplicação. Tenha isso em mente, em algum momento você terá que utilizar o Exploit Server para explorar algo que necessite interação da vítima.

<br>

## Vulnerabilidades
Nota: Algumas vulnerabilidades podem estar em duas ou mais etapas do exame. Por exemplo, SQL Injection pode estar em qualquer etapa. Nesses casos, a vulnerabilidade será colocada na etapa mais provável.

Aqui temos as possíveis vulnerabilidades para cada etapa:

#### ETAPA 1 - ACESSO INICIAL
* Enumeração
* XSS - Cross-Site Scripting
* Vulnerabilidades em DOM
* Autenticação
* Web Cache Poisoning
* Ataques de Host Header
* HTTP Request Smuggling

#### ETAPA 2 - ESCALAÇÃO DE PRIVILÉGIOS
* SQL Injection
* CSRF - Cross-Site Request Forgery
* OAuth
* JWT
* Vulnerabilidades de Controle de Acesso
* Prototype Pollution
* Outros

#### ETAPA 3 - EXFILTRAÇÃO DE DADOS
* SSRF - Server Side Request Forgery
* XXE Injection
* SSTI - Server Side Template Injection
* Directory Traversal
* Deserialização Insegura
* Vulnerabilidades de Upload de Arquivo
* SSPP - Server Side Prototype Pollution

<br>

Essas vulnerabilidades são baseadas nos links do início, que também compartilham essas imagens:

![Acesso Inicial ((1)](https://github.com/user-attachments/assets/faa4597a-46b8-420e-bfe0-de61dbceeae0)


![image](https://github.com/user-attachments/assets/5fdc252f-befd-433c-b200-58c567fa4182)

