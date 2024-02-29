## Trabalho 2 - *Tester*

O *Tester* tem como objetivo permitir o teste do trabalho prático 2, de forma sistemática e uniforme para todos os grupos.
Os alunos poderão testar a correção da sua implementação, usando o *Tester*.

Para executar o tester, os alunos com sistemas operativos Linux ou MacOS devem usar o script 
[test-sd-tp2.sh](https://raw.githubusercontent.com/preguica/sd2122-tp-api/main/test-sd-tp2.sh),
enquanto que os alunos com Windows 10 devem usar o script [test-sd-tp2.bat](https://raw.githubusercontent.com/preguica/sd2122-tp-api/main/test-sd-tp2.bat).

**Nota**: Todos os ficheiros mencionados estão dispopíveis em: [https://github.com/preguica/sd2122-tp-api](https://github.com/preguica/sd2122-tp-api).

### Preparação
Antes de executar o tester e criar a imagem docker, devem adicionar e atualizar o 
fichero [trab.props](https://raw.githubusercontent.com/preguica/sd2122-tp-api/main/trab.props) 
na raiz do vosso projeto com a informação correta:


```
USERS_REST_SERVER_MAINCLASS=classe do servidor Users REST (e.g. sd2122.tp2.server.RESTUsersServer)
USERS_REST_PORT=porto do servidor Users REST (e.g. 8080)
USERS_SOAP_SERVER_MAINCLASS=classe do servidor Users SOAP (e.g. sd2122.tp2.server.SOAPUsersServer)
USERS_SOAP_PORT=porto do servidor Users SOAP (e.g. 8080)
USERS_EXTRA_ARGS=parâmetro adicional a passar ao servidor Users, se algum
DIR_REST_SERVER_MAINCLASS=classe do servidor Directory REST (e.g. sd2122.tp2.server.RESTDirServer)
DIR_REST_PORT=porto do servidor Directory REST (e.g. 8080)
DIR_SOAP_SERVER_MAINCLASS=classe do servidor Directory SOAP (e.g. sd2122.tp2.server.SOAPDirServer)
DIR_SOAP_PORT=porto do servidor Directory SOAP (e.g. 8080)
DIR_EXTRA_ARGS=parâmetro adicional a passar ao servidor Directory, se algum
FILES_REST_SERVER_MAINCLASS=classe dos servidores Files REST (e.g. sd2122.tp2.server.RESTFilesServer)
FILES_REST_PORT=porto dos servidores Files REST (e.g. 8080)
FILES_SOAP_SERVER_MAINCLASS=classe dos servidores Files SOAP (e.g. sd2122.tp2.server.SOAPFilesServer)
FILES_SOAP_PORT=porto dos servidores Files SOAP (e.g. 8080)
FILES_EXTRA_ARGS=parâmetro adicional a passar aos servidores Files, se algum
DISCOVERY_MULTICAST_IP=endereço multicast para descoberta (e.g. 226.226.226.226)
DISCOVERY_MULTICAST_PORT=porto para descoberta (e.g. 2266)
USERS_SERVER_KEYSTORE=nome do ficheiro com o keystore do servidor Users (e.g. userserver.ks)
USERS_SERVER_KEYSTORE_PWD=password do keystore do servidor Users
DIR_SERVER_KEYSTORE=nome do ficheiro com o keystore do servidor Directory (e.g. dirserver.ks)
DIR_SERVER_KEYSTORE_PWD=password do keystore do servidor Directory
FILES_SERVER_KEYSTORE=nome do ficheiro com o keystore do servidor Files (e.g. filesserver.ks)
FILES_SERVER_KEYSTORE_PWD=password do keystore do servidor Files
CLIENT_TRUSTSTORE=nome do ficheiro com o truststore para os clienes (e.g. client-truststore.ks)
CLIENT_TRUSTSTORE_PWD=password do trustore
FILES_PROXY_SERVER_MAINCLASS=classe do servidor Proxy Files REST (e.g. sd2122.tp2.server.rest.DropboxFilesServer)
FILES_PROXY_PORT=porto do servidor Proxy Files REST (e.g. 8080)
FILES_PROXY_EXTRA_ARGS=argumentos extra a passar ao servidor PROXY Files
DIR_REP_SERVER_MAINCLASS=classe do servidor Directory REST, com replicação (e.g. sd2122.tp2.server.rest.RepDirServer)
DIR_REP_PORT=porto do servidor Directory REST, com replicação (e.g. 8080)
DIR_REP_EXTRA_ARGS_FIRST=argumentos extra a passar ao primeiro servidor replicado
DIR_REP_EXTRA_ARGS_OTHER=argumentos extra a passar ao outros servidores replicados
```

Enquanto não tiverem a parte SOAP implementada, devem deixar as propriedades respetivas sem nenhum valor, mas não as devem apagar.
Podem igualmente testar o servidor Users sem terem os servidores Driectory e Files funcional, deixando a respetiva propriedade sem
nenhum valor.

Devem adicionar ou atualizar o ficheiro **Dockerfile** presente na raiz do vosso projeto
com o conteúdo presente no seguinte link: [Dockerfile](https://raw.githubusercontent.com/preguica/sd2122-tp-api/main/Dockerfile).

**Sempre que alteram o vosso trabalho (incluindo o ficheiro trab.props)** devem criar 
uma nova imagem Docker do vosso trabalho (usando o projeto Maven disponibilizado, 
tal consiste em executar: `mvn clean compile assembly:single docker:build`).

### Execução dos testes

Para executar o *Tester*, basta executar o seguinte comando, 
usando o nome da imagem do vosso trabalho (**substituir sd2122-tp2-xxxxx-yyyyy pelo 
nome da vossa imagem**):

```
sh test-sd-tp2.sh -image sd2122-tp2-xxxxx-yyyyy
```

*NOTA:* há uma secção sobre problemas com a execução deste comando mais tarde neste documento.

Para mais informação, ver a página do Tester do trabalho 1.


### Outras opções do *Tester*

O *Tester* possui algumas opções que podem ser especificadas ao corrê-lo, que modificam o seu comportamento.

* **-test \<num\>** : Permite omitir a execução de alguns testes. 
Por exemplo, se passarem o valor **-test 2b**, os testes começarão no 
teste 2b. Esta opção é útil quando já verificaram que o vosso trabalho 
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

![Imagen com debug a FINE](../trab1/images/tester-rundetail.png)

* **-timeout seconds** : Permite indicar os timeouts usados no vosso programa 
nos clientes de REST e SOAP (a indicação destes valores é importante para que 
a verificação do comportamento na presença de falhas funcione corretamente) – 
valor por omissão 10 segundos equivalente a -timeout 10.

* **-dump true|false** : Permite omitir as mensagens de erro dos servidor em caso 
de erro, usando **-dump false**.

* **-allProps true|false** : Ignora verificação se todas as proriedades estão definidas.

* **-interoptime miliseconds** : Tempo entre duas operações sucessivas no proxy -- 
use este valor caso tenha erros de demaidos pedidos, como formar de reduzir o número de pedidos
por segundo efetuados ao servidor.

* **-kafkasleep seconds** : Tempo de espera entre lançar os serviços externos - Kafka, Zoopkeeper - 
e lançar os servidores - valor por omissão 10 segundos.


### Códigos de erro

Os códigos de erro que os vossos servidores devolvem devem estar de acordo
com os comentários das respetivas interfaces, disponíveis 
em: [https://github.com/preguica/sd2122-tp-api](https://github.com/preguica/sd2122-tp-api).

### Versões

As versões do *Tester* são incrementais., i.e., a versão N do *Tester* executará todos os testes das versões anteriores.
Nesta secção indicam-se as funcionalidades testadas por cada versão.

#### Versão 1
Testa as seguintes funcionalidades usando TLS:
* TBC

### Notas finais

O facto dum trabalho passar os testes todos (ou o teste duma funcionalidade) não equivale a que tenha a cotação máxima. 
Primeiro, devem ter em atenção que os testes apenas estão a testar as funcionalidades indicadas na listagem anterior.
Segundo, a avaliação do trabalho terá em conta a qualidade do código.
