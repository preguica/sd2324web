## Trabalho 2

#### Prazos
2º Trabalho (online - código + formulário):
* Data de entrega: 4 de junho, 23h59, 1 hora de tolerância 

Form para submissão do trabalho: [https://forms.gle/MnA7qpkpigcZ66oh8](https://forms.gle/MnA7qpkpigcZ66oh8)

 

### Objetivo

O objetivo do segundo trabalho é estender o primeiro trabalho, com as 
seguintes funcionalidades: 

- segurança;
- interação com serviços externos;
- tolerância a falhas - vamos implementar a tolerância a falhas no servidor
de feeds.

O desenho da solução, incluindo a arquitetura e as interfaces dos serviços permanecem 
idênticas às do primeiro trabalho, salvo as indicações em contrário neste enunciado.

A compatibilidade com as interfaces e operações pré-definidas tem que ser observada.


### Funcionalidades 

#### Segurança (máx: 4)

O objetivo desta funcionalidade é tornar o sistema seguro, impedindo que elementos 
não autorizados executem operações no serviço de feeds e serviço de utilizadores.
Para alcançar este objetivo, a solução deve incluir os seguintes mecanismos:

1. utilizar canais seguros, usando TLS, com autenticação do servidor. 
As operações dos clientes incluem uma password para verificar que o cliente está 
autorizado a efetuar a operação indicada (esta última parte já se verificava nas 
interfaces definidas no sistema);
2. caso existam operações executadas apenas entre os servidores, garantir que 
estas não podem ser invocadas por 
um cliente -- sugere-se a utilização dum segredo partilhado entre os servidores, 
o qual pode ser
passado como parâmtero ao arrancar o programa.

Para autenticar o servidor, é necessário que cada servidor tenha o seu certificado.
Assim, será necessário criar um keystore para cada servidor. Na página do Tester
está disponibilizada informação de quais os keystores a criar. 

  **Tests:** 102-104.


#### Interação com um serviço externo (alternativa E1)  (máx: 4.5)

O objetivo desta funcionalidade é ter um servidor de feeds, que funcione como um servidor
proxy para um servidor Mastodon, que disponibilize um serviço REST com autenticação O.Auth.
O domínio em que executa o servidor proxy terá apenas um utilizador e deverá funcioanr 
da seguinte forma:
* a operação de adicionar mensagem deve ser enviada para o servidor Mastodon - de notar
que será necessário *representar* no Mastodon as propriedades necessárias 
ao sistema que estão a desenvolver;
* as operações de obter mensagem ou listar as mensagens devem ser enviadas para o servidor 
Mastodon.

Nesta alternativa, não é possível ao utilizador subscrever outros utilizadores, nem a outros
utilizadores subscrever este utilziador. 

Para implementação desta funcionalidade sugere-se a utilização da 
biblioteca [ScribeJava](https://github.com/scribejava/scribejava), como apresentado nas
aulas práticas.

**NOTA:** Apenas é necessário fazer este servidor com uma interface REST.

**NOTA:** Para que seja possível testar o serviço de forma automática, 
usando o Tester, é necessário poder indicar ao servidor, quando arranca, 
que deve iniciar com o estado do serviço externo *limpo*. 
Para tal, o Tester passará como primeiro parâmetro do servidor que 
interage com o serviço externo o valor **true** para indicar que o estado 
anterior deve ser ignorado. 
Se o Tester passar o valor **false**, o estado gravado deve ser usado pelo servidor.

  **Tests:** 105ab.

#### Interação com um serviço externo (alternativa E2)  (máx: 6)

Idêntico à opção E1, mas permitindo que o utilizador do domínio do proxy
solicite seguir um outro utilizador mastodon.

  **Tests:** 105c.


#### Tolerância a falhas - servidor de feeds (alternativa D1)  (máx: 7)

Implementar uma solução que permita tolerar falhas numa máquina em que esteja a correr
um servidor de feeds dum domínio, replicando o servidor de feeds com uma solução de replicação 
de máquina de estados, recorrendo a um sistema de comunicação indireta - e.g. Kafka. 
A solução deve tolerar a falha de qualquer servidor de feeds.

A solução deve garantir que um cliente lê sempre o estado dum servidor que tem uma 
versão tão atual quanto a versão do servidor que foi acedido anteriormente. 
Para tal, o servidor pode adicionar *headers* às resposta a enviar aos clientes, os 
quais serão enviados nas próximas operações executadas pelo mesmo cliente (o Tester
reenviará todos os headers começados em **X-FEEDS**).

  **Tests:** 106-111.

#### Tolerância a falhas - servidor de feeds (alternativa D2a)  (máx: 6)

Implementar uma solução que permita tolerar falhas numa máquina em que esteja a correr
um servidor de feeds dum domínio, replicando o servidor de feeds com uma solução de replicação 
de máquina de estados, implementando o protocolo primário/secundário. 
A solução deve tolerar a falha de qualquer servidor; no caso da falha do servidor 
primário, o sistema deve garantir que continua a ser possível fazer leituras, 
mas não necessita permitir a execução de escritas.
                     
A solução deve garantir que um cliente lê sempre o estado dum servidor que tem uma 
versão tão atual quanto a versão do servidor que foi acedido anteriormente. 
Para tal, o servidor pode adicionar *headers* às resposta a enviar aos clientes, os 
quais serão enviados nas próximas operações executadas pelo mesmo cliente (o Tester
reenviará todos os headers começados em **X-FEEDS**).

**Nota:** O programa deve suportar a falha de 1 servidor, estando os protocolos 
implementados configurados para tal.

  **Tests:** 106-109.

#### Tolerância a falhas - servidor de feeds (alternativa D2b)  (máx: 10)

Implementar uma solução que permita tolerar falhas numa máquina em que esteja a correr
um servidor de feeds dum domínio, replicando o servidor de feeds com uma solução de replicação 
de máquina de estados, implementando o protocolo primário/secundário. 
A solução deve tolerar a falha de 
qualquer servidor, incluindo o servidor primário -- para tal, deve eleger um novo 
primário.

A solução deve garantir que um cliente lê sempre o estado dum servidor que tem uma 
versão tão atual quanto a versão do servidor que foi acedido anteriormente. 
Para tal, o servidor pode adicionar *headers* às resposta a enviar aos clientes, os 
quais serão enviados nas próximas operações executadas pelo mesmo cliente (o Tester
reenviará todos os headers começados em **X-FEEDS**).

**Nota:** O servidor deve suportar a falha de 1 servidor, estando os protocolos 
implementados configurados para tal.

  **Tests:** 106-111.

### Fatores depreciativos

O código entregue deverá seguir boas práticas de programação. A repetição 
desnecessária de código, inclusão de constantes mágicas, o uso de estruturas de 
dados inadequadas, etc., poderá incorrer numa penalização.
(max: 2 valores)

Falta de robustez e comportamentos erráticos da solução são motivo para penalização. 

**NOTA:** A solução deve continuar a contemplar as falhas temporárias dos canais de comunicação.

## Execução
O trabalho pode ser executado em grupo, de 1 ou 2 alunos.
Os alunos do mesmo grupo não precisam de pertencer ao mesmo turno prático, embora 
tal seja fortemente recomendado.

Os grupos neste segundo trabalho podem ser diferentes do primeiro trabalho.

Como base deste segundo trabalho, os alunos podem usar a sua implementação do 
primeiro trabalho ou a implementação disponível no seguinte link, fornecida
como tal:
[https://github.com/smduarte/sd2223-trab1](https://github.com/smduarte/sd2223-trab1)

 

## Avaliação
A avaliação do trabalho terá em conta os seguintes critérios:

- Funcionalidades desenvolvidas e a sua conformidade com a especificação, tendo como
base os resultados da bateria de testes automáticos;
- Qualidade da solução;
- Qualidade do código desenvolvido.

A classificação final do aluno é individual e será menor ou igual à classificação 
do trabalho, em função da discussão do
trabalho, a realizar durante a seguir à entrega do trabalho.

## Bateria de testes

A bateria de testes destinada a verificar a conformidade da solução com a 
especificação está disponível em **A DEFINIR**.

De notar que passar os testes não garante que o trabalho está correto, dado que não
se podem testar todas as situações.


# Ambiente de desenvolvimento

Todo o material de apoio fornecido pressupõe que o desenvolvimento será em ambiente 
Linux e Java 17.
A validação do trabalho por via da bateria de testes automática fará uso de tecnologia 
Docker.

# Histórico de alterações

## 7/5/23

### Enunciado
Versão inicial.

