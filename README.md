# Estudos BSCP - Burp Suite Certified Practitioner

## Introdução
Esse repositório majoritariamente compila dois outros repositórios que tem relação com a BSCP:
* [Burp Suite Certified Practitioner Exam Study - botesjuan](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study)
* [BurpSuiteCertifiedPractitioner - DingyShark](https://github.com/DingyShark/BurpSuiteCertifiedPractitioner)

Dou grande crédito para os dois autores desses repositórios. A diferença desses links para esse repo é que aqui você vai encontrar o conteúdo em português e também centralizado.

A ideia é direcionar quem está estudando para a BSCP com as possíveis vulnerabilidades em cada etapa. Acho isso fundamental para conseguir passar no exame, visto que o principal desafio é o limite de tempo.

Em cada diretório você encontrará as vulnerabilidades possíveis para cada etapa, a resolução da maioria dos laboratórios e algumas dicas que podem te ajudar na prova.

<br>

> [!IMPORTANT]  
> Não utilize esse CheatSheet para aprender algo. Para estudar, faça os módulos da PortSwigger. Esse repositório serve apenas para compilar várias resoluções de laboratórios, dar algumas pequenas dicas e guiar o estudante nas possíveis vulnerabilidades que podem cair na BSCP.

<br>

Outros reviews e write-ups foram utilizados para compor esse guia, como:
* [BSCP Methodology](https://bscpcheatsheet.gitbook.io/exam)
* [How to Crush BSCP Exam in 75 Mins — BSCP Review - Ossama Yasser](https://medium.com/@ossamayasserr/how-to-crush-bscp-exam-in-75-mins-bscp-review-0b207a17e26d)
* [Burp Suite Certified Practitioner Exam Review - Micah Van Deusen](https://micahvandeusen.com/burp-suite-certified-practitioner-exam-review/)

<br>

## Exame

A prova é composta de dois aplicativos com 3 etapas cada, consecutivas, ou seja, você não pode chegar na última etapa sem passar pelas outras duas.

O tempo máximo para conclusão é de 4 horas.

1. Na primeira etapa, é necessário obter acesso a qualquer usuário de pouco privilégio.
2. Na segunda etapa, é preciso obter permissões administrativas, seja escalando privilégios, seja obtendo a acesso a conta do administrador.
3. Na terceira etapa, é necessário ler o arquivo localizado em `/home/carlos/secret` e submeter o valor obtido como solução da aplicação.

Não perca tempo testando os cookies `_lab` e `_lab_analytics`, eles não serão vulneráveis.

Todo aplicativo terá pelo menos um usuário ativo e logado. Esse usuário visitará a página home da aplicação a cada 15 segundos e também clicará em qualquer link e e-mail que ele receber da aplicação. Tenha isso em mente, em algum momento você terá que utilizar o Exploit Server para explorar algo que necessite interação da vítima.

<br>

## Vulnerabilidades
> [!NOTE]  
> Algumas vulnerabilidades podem estar em duas ou mais etapas do exame. Por exemplo, SQL Injection pode estar em qualquer etapa. Nesses casos, a vulnerabilidade será colocada na etapa mais provável.

> [!IMPORTANT]  
> De maneira alguma leve o que está aqui como verdade absoluta. Estamos falando de possibilidades, a qualquer momento a PortSwigger pode mudar o formato do exame e simplesmente adicionar diferentes tipos de vulnerabilidades.

<br>

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
* OS Command Injection
* SSTI - Server Side Template Injection
* Directory Traversal
* Deserialização Insegura
* Vulnerabilidades de Upload de Arquivo
* SSPP - Server Side Prototype Pollution

<br>

Essas vulnerabilidades são baseadas nos links do início, que também compartilham essas imagens:

![Acesso Inicial ((1)](https://github.com/user-attachments/assets/faa4597a-46b8-420e-bfe0-de61dbceeae0)

![image](https://github.com/user-attachments/assets/5fdc252f-befd-433c-b200-58c567fa4182)

<br>

## Extensões Úteis
Ao longo do processo você vai perceber que várias extensões são ditas como úteis, mas pra ser sincero apenas algumas de fato me trouxeram algum resultado:

#### Realmente úteis:
* Param Miner (facilmente a mais útil)
* Java Deserialization Scanner
* JWT Editor (mais para os labs)
* HTTP Request Smuggler

#### Até podem ajudar:
* Hackvertor
* Host Header Inchecktion
* Collaborator Everywhere
* Server-Side Prototype Pollution Scanner

#### Provavelmente não ajudará no exame:
* Web Cache Deception Scanner
* Logger++
* Active Scan++
* InQL - GraphQL Scanner
* Content Type Converter

<br>

Ferramentas terceiras também podem ser bem úteis, como:
* DOM Invader (Browser)
* ysoserial.jar
* SQLMap

> [!TIP]
> Aprenda a usar o SQLMap e utilize-o para resolver os laboratórios (depois de aprender o processo manual). Isso é extremamente importante, afinal o maior do desafio do exame é o tempo limitado, e explorar SQL Injection manualmente não é a coisa mais rápida do mundo.

<br>

## Estudos
Eu me preparei da seguinte forma para o exame:

Passei por todos os tópicos da PortSwigger (mesmo alguns que não são úteis pro exame como os que envolvem vulnerabilidades lógicas que envolvem saldo) e fiz os laboratórios **Apprentice** e **Practitioner**.

Conteúdos mais avançados e laboratórios Expert eu pulei todos.

Em seguida eu entrei nos CheatSheets mencionados acima e montei um compilado igual esse que está aqui, separando por etapas e possíveis vulnerabilidades.

Após, segui [este guia](https://portswigger.net/web-security/certification/how-to-prepare) da própria PortSwigger do que é necessário e recomendado para estar pronto para a prova. Os últimos três itens são obrigatórios para poder agendar o exame.

- [ ] Completar todos os [Practitioner Labs](https://portswigger.net/web-security/certification/how-to-prepare/practitioner-labs-prep-step-one) (opcional).
- [ ] Completar os labs do [passo 2](https://portswigger.net/web-security/certification/how-to-prepare).
- [ ] Completar cinco desafios de lab misterioso.
- [ ] Fazer e passar em um exame prático (simulado).

<br>

> [!CAUTION]
> Psiu, meia noite vou te contar um segredo. Talvez, mas apenas talvez, eu omiti algumas evidências que estão naqueles links iniciais de referência, pois uns dois exemplos eram idênticos ao que caiu na minha prova. Apenas 2 passos, não o exame todo, então você ainda precisa estudar! De todo modo, com certeza eles me ajudaram a agilizar meu exame... Não estou colocando aqui para evitar problemas, caso a PortSwigger considere aquilo como algum tipo de dump. Boa sorte!
