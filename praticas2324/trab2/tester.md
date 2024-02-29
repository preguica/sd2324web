## Trabalho 2 - *Tester*

O *Tester* tem como objetivo permitir o teste do trabalho prático 2, de forma sistemática e uniforme para todos os grupos.
Os alunos poderão testar a correção da sua implementação, usando o *Tester*.

Para executar o tester, os alunos com sistemas operativos Linux ou MacOS devem usar o script 
[test-sd-tp2.sh](https://github.com/preguica/sd2223-trab1/raw/main/test-sd-tp2.sh),
enquanto que os alunos com Windows 10 devem usar o script [test-sd-tp2.bat](https://github.com/preguica/sd2223-trab1/raw/main/test-sd-tp2.bat).

**Nota**: Todos os ficheiros mencionados estão dispopíveis em:
[https://github.com/preguica/sd2223-trab1](https://github.com/preguica/sd2223-trab1).
 
### Preparação
Antes de executar o tester e criar a imagem docker, devem adicionar e atualizar o 
fichero [feeds.props](https://github.com/preguica/sd2223-trab1/raw/main/feeds.props) 
na raiz do vosso projeto com a informação correta:


```
USERS_REST_SERVER_MAINCLASS=classe do servidor Users REST (e.g. sd2223.tp2.server.RESTUsersServer)
USERS_REST_PORT=porto do servidor Users REST (e.g. 8080)
USERS_SOAP_SERVER_MAINCLASS=classe do servidor Users SOAP (e.g. sd2223.tp2.server.SOAPUsersServer)
USERS_SOAP_PORT=porto do servidor Users SOAP (e.g. 8080)
USERS_EXTRA_ARGS=parâmetro adicional a passar ao servidor Users, se algum
FEEDS_REST_SERVER_MAINCLASS=classe do servidor Feeds REST (e.g. sd2223.tp2.server.RESTFeedsServer)
FEEDS_REST_PORT=porto do servidor Feeds REST (e.g. 8080)
FEEDS_SOAP_SERVER_MAINCLASS=classe do servidor Feeds SOAP (e.g. sd2223.tp2.server.SOAPFeedsServer)
FEEDS_SOAP_PORT=porto do servidor Feeds SOAP (e.g. 8080)
FEEDS_EXTRA_ARGS=parâmetro adicional a passar ao servidor Feeds, se algum
DISCOVERY_MULTICAST_IP=endereço multicast para descoberta (e.g. 226.226.226.226)
DISCOVERY_MULTICAST_PORT=porto para descoberta (e.g. 2266)
FEEDS_PROXY_SERVER_MAINCLASS=classe do servidor Proxy Feeds REST (e.g. sd2223.tp2.server.ProxyFeedsServer)
FEEDS_PROXY_PORT=porto do servidor Proxy Feeds REST (e.g. 8080)
FEEDS_PROXY_EXTRA_ARGS=argumentos extra a passar ao servidor PROXY Feeds
MASTODOM_USERNAME=user name in Mastodom (e.g. nmp)
FEEDS_REP_SERVER_MAINCLASS=classe do servidor Feeds REST, com replicação (e.g. sd2223.tp2.server.RepFeedsServer)
FEEDS_REP_PORT=porto do servidor Feeds REST, com replicação (e.g. 8080)
FEEDS_REP_EXTRA_ARGS_FIRST=argumentos extra a passar ao primeiro servidor replicado
FEEDS_REP_EXTRA_ARGS_OTHER=argumentos extra a passar ao outros servidores replicados
USERS_KEYSTORES=keystores para os servidores users - 1 por cada servidor (e.g. users0-ourorg0,users0.jks,users0pwd users0-ourorg1,users1.jks,users1pwd ...)
FEEDS_KEYSTORES=keystores para os servidores feeds - 1 por cada servidor (e.g. feeds0-ourorg0,feeds0-0.jks,feeds0-0pwd feeds1-ourorg0,feeds0-1.jks,feeds1-0pwd ...)
CLIENT_TRUSTSTORE=nome do ficheiro com o truststore para os clienes (e.g. client-truststore.jks)
CLIENT_TRUSTSTORE_PWD=password do trustore
```

Enquanto não tiverem alguma parte implementada, devem deixar as propriedades respetivas sem nenhum valor, mas não as devem apagar.

Devem adicionar ou atualizar o ficheiro **Dockerfile** presente na raiz do vosso projeto
com o conteúdo presente no seguinte link: [Dockerfile](https://github.com/preguica/sd2223-trab1/raw/main/Dockerfile).

**Sempre que alteram o vosso trabalho (incluindo o ficheiro feeds.props)** devem criar 
uma nova imagem Docker do vosso trabalho (usando o projeto Maven disponibilizado, 
tal consiste em executar: `mvn clean compile assembly:single docker:build`).

### Keystores

Devem criar um keystore para cada servidor que poderá ser criado. Assim, devem criar keystores para os
servidores:
* users0-ourorg0
* feeds0-ourorg0
* feeds1-ourorg0
* feeds2-ourorg0
* users0-ourorg1
* feeds0-ourorg1
* feeds1-ourorg1
* feeds2-ourorg1
* users0-ourorg2
* feeds0-ourorg2
* feeds1-ourorg2
* feeds2-ourorg2

O certificado criado para cada servidor deve ter como DN o nome do servidor. Adicionalmente,
deve ter o "Subject Alternative Name" associado ao nome de DNS do servidor.

Isto pode ser feito usando a opção ```keytool ... -ext SAN=dns:users0-ourorg0```. 
Com esta opção e adicionando o certificado à trustore do cliente,
o cliente conseguirá validar o certificado, não sendo necessário alterar a verificação,
como tinha sido indicado na aula 8.


**IMPORTANTE**

Além do indicado acima, devem usados URIs de servidor com o *hostname* e não o endereço IP.
Na solução dada, na geração do URIs dos servidores REST e SOAP (anunciados via Descoberta) 
é, portanto, necessário substituir IP.hostAddress() por IP.hostname() (que terá de ser
programado e adicionado à classe utils.IP).

Sem esta alteração, irão aparecer erros do estilo:

`No subject alternative names matching IP address 172.19.0.4 found`

**NOTA:** Devem alterar o Dockerfile para copiar as keystores para a imagem docker. Para tal podem 
usar o comando [COPY](https://docs.docker.com/engine/reference/builder/#copy) ou [ADD](https://docs.docker.com/engine/reference/builder/#add).

### Execução dos testes

Para executar o *Tester*, basta executar o seguinte comando, 
usando o nome da imagem do vosso trabalho (**substituir sd2122-tp2-xxxxx-yyyyy pelo 
nome da vossa imagem**):

```
sh test-sd-tp2.sh -image sd2223-tp2-xxxxx-yyyyy
```

*NOTA:* há uma secção sobre problemas com a execução deste comando mais tarde neste documento.

Para mais informação, ver a página do Tester do trabalho 1.


### Outras opções do *Tester*

O *Tester* possui algumas opções que podem ser especificadas ao corrê-lo, que modificam o seu comportamento.

* **-test \<num\>** : Permite omitir a execução de alguns testes. 
Por exemplo, se passarem o valor **-test 102b**, os testes começarão no 
teste 102b. Esta opção é útil quando já verificaram que o vosso trabalho 
funciona corretamente até um dado teste e estão a corrigir erros num 
teste específico.
  
* **-sleep \<seconds\>** : Permite diminuir o tempo de espera entre serem 
lançadas os containers com o vosso trabalho e iniciar a execução dos 
testes. Podem ajustar este valor consoante a capacidade do vosso 
computador e as operações que estejam a fazer na fase de inicialização.

* **-log OFF\|ALL\|FINE\|FINEST** : Permite controlar o nível de mensagens 
gerado pelo programa. Por exemplo, ao usarem a opção **-log FINE**, o 
programa vai indicar todas as operações que está a fazer ao vosso sistema, 
indicando as mensagens recebidas e as esperadas, como se apresenta na 
imagem seguinte:

![Imagen com debug a FINE](../trab1/tester-rundetail.png)

* **-textsize \<len\>** : Permite indicar a dimensão máxoma do texto das 
mensagens criadas.

* **-timeout seconds** : Permite indicar os timeouts usados no vosso programa 
nos clientes de REST e SOAP (a indicação destes valores é importante para que 
a verificação do comportamento na presença de falhas funcione corretamente) – 
valor por omissão 10 segundos equivalente a -timeout 10.


* **-dump true\|false** : Permite omitir as mensagens de erro dos servidor em caso 
de erro, usando **-dump false**.

* **-allProps true\|false** : Ignora verificação se todas as proriedades estão definidas.

* **-interoptime miliseconds** : Tempo entre duas operações sucessivas no proxy -- 
use este valor caso tenha erros de demaidos pedidos, como formar de reduzir o número de pedidos
por segundo efetuados ao servidor.

* **-services true|false** : Usando **-services true**, os serviços externos (Kafka, Zookeeper)
são lançados em todos os testes.

* **-kafkasleep seconds** : Tempo de espera entre lançar os serviços externos - Kafka, Zoopkeeper - 
e lançar os servidores - valor por omissão 10 segundos.

* **-failsleep seconds** : Tempo de espera para descobrir que um servidor falhou - valor 
por omissão 15 segundos.

* **-skip true\|false** : Com **-skip true** não verifica as keystores.





### Códigos de erro

Os códigos de erro que os vossos servidores devolvem devem estar de acordo
com os comentários das respetivas interfaces, disponíveis em: [https://github.com/preguica/sd2223-trab1](https://github.com/preguica/sd2223-trab1).

### Versões

As versões do *Tester* são incrementais., i.e., a versão N do *Tester* executará todos os testes das versões anteriores.
Nesta secção indicam-se as funcionalidades testadas por cada versão.

#### Versão 1
Testa as seguintes funcionalidades em REST, usando TLS:
* Users / Feeds: 102

Testa as seguintes funcionalidades em SOAP, usando TLS:
* Users / Feeds: 103

Testa as seguintes funcionalidades em mix REST e SOAP, usando TLS:
* Users / Feeds: 104

Testa as seguintes funcionalidades no proxy REST, usando TLS:
* Adiciona utilizador, post, obtém mensagens e lista de mensagens (105a).

#### Versão 2
Testa as seguintes funcionalidades no proxy REST, usando TLS:
* Adiciona utilizador, post, obtém mensagens e lista de mensagens (105a).
* Adiciona utilizador, post, remove, obtém mensagens e lista de mensagens (105b).
* Adiciona utilizador, post, obtém lista de mensagens, faz subscribe e unsubscribe (105c).

Testa as seguintes funcionalidades com um domínio replicado REST, usando TLS, sem falhas:
* criação de mensagens, leitura de mensagens e feeds (106a)
* mix com remoção de mensagens (106b)
* mix com remoção de utilizadores (106c)
* mix com getMessage* que necessitam de forward (106d)

Testa as seguintes funcionalidades com um domínio replicado REST + um domínio não replicado, usando TLS, sem falhas:
* criação de mensagens, leitura de mensagens e feeds (107a)
* mix com remoção de mensagens (107b)
* mix com remoção de utilizadores (107c)
* mix com getMessage* que necessitam de forward (107d)

#### Versão 3
Testa as seguintes funcionalidades com um domínio replicado REST:
* mix de operações, falhas de comunicações entre réplica 0 e réplica 1 (108a)
* mix de operações, falhas de comunicações entre réplica 0 e réplica 2 (108b)
* mix de operações, crash na réplica 1 sem recuperação (109a)
* mix de operações, crash na réplica 1 com recuperação (109b)
* mix de operações, crash na réplica 1 com recuperação e sem esperar até executar operações (109c)
* mix de operações, crash na réplica 0 sem recuperação (110a)
* mix de operações, crash na réplica 0 com recuperação (110b)
* mix de operações, crash na réplica 0 com recuperação e sem esperar até executar operações (110c)

Testa as seguintes funcionalidades com um domínio replicado REST + um domínio não replicado, usando TLS, sem falhas:
* mix de operações, crash na réplica 0 sem recuperação (111a)
* mix de operações, crash na réplica 0 com recuperação (111b)


### Notas finais

O facto dum trabalho passar os testes todos (ou o teste duma funcionalidade) não equivale a que tenha a cotação máxima. 
Primeiro, devem ter em atenção que os testes apenas estão a testar as funcionalidades indicadas na listagem anterior.
Segundo, a avaliação do trabalho terá em conta a qualidade do código.
