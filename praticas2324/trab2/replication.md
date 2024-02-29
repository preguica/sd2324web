## Trabalho 2 - Algumas notas sobre *Replicação*

Esta página terá notas relacionadas com a implementação de replicação no trabalho.

Nas aulas teóricas, foi apresentado: 
(1) o algoritmo primário/secundário; 
(2) como é que se usa o zookeeper para eleger o primário; e 
(3) como é que se pode usar o Kafka para efetuar a replicação, com uma discussão dos 
problemas que surgem na implementação da replicação (não só quando se usa o Kafka, 
mas em geral).

Na última aula prática apresentaram-se exemplos de como interagir com o Kafka e com o 
Zookeeper.

Esta informação pode ser útil na implementação do trabalho.

## Replicação: questões genéricas

### Organização do código

Na implementação da replicação, de forma a que o código da replicação não fique
espalhado por todos os métodos que é necessário replicar, sugere-se que 
concentrem o código de replicação numa classe que trata da replicação.
As diferentes operações devem ser ser codificadas numa (ou mais) classes 
em que além da informação sobre o método e parâmetros poderá ser guardado
o número de sequência da operação.

Notem que será sempre necessário manter a lista de operações efetuadas para 
recuperar de falhas. 

Na versão primário/secundário, se organziarem o código desta forma, o objeto que
trata da replicação seria aquele que daria o número de sequência à operação
na réplica primária.

### Inicialização do cliente de Kafka

Quando um servidor REST é configurado usando uma classe (e.g. `config.register(FeedsResource.class)`),
o recurso apenas é instanciado quando o primeiro pedido ao servidor é efetuado.
A implicação disto é que na versão replicada, as réplicas apenas começariam a funcionar 
nesse momento, o que não é o desejado.

Assim, devem iniciar o servidor passando uma instância da classe (e.g. `config.register(new FeedsResource())`)


### Redirect dum pedido para outro servidor

Em algumas situações, pode ser interessante redirigir o pedido dum cliente para outro 
servidor (por exemplo, um servidor secundário que não tem a versão pretendida, pode 
querer rdeirigir o cliente para o primário). Isto pode ser feito da seguinte forma:


```java
throw new WebApplicationException(Response.temporaryRedirect( URI.create("https://...")).build());
```

**Nota:** O URL usado deve ser completo, includindo *query parameters*, quando existam.

### Headers nos pedidos REST

No enunciado diz-se que: "A solução deve garantir que um cliente lê sempre o estado dum 
servidor que tem uma versão tão atual quanto a versão do servidor que foi 
acedido anteriormente. Para tal, o servidor pode adicionar headers às resposta a 
enviar aos clientes, os quais serão enviados nas próximas operações executadas 
pelo mesmo cliente."

Uma forma de passar Headers num cliente REST Java é usando a função *header* disponível 
quando se cria o pedido, como no exemplo seguinte:

```java
WebTarget target = client.target( serverUrl ).path( PATH );

Response r = target.request()
		.header(FeedsService.HEADER_VERSION, 47)
		.accept(MediaType.APPLICATION_JSON)
		.get();
```

No servidor, para aceder ao valor dos headers enviados pelo cliente, pode-se usar a anotação 
*@HeaderParam* (de forma semelhante às outras anotações para parâmetros).
Para devolver um header pode-se alterar a forma como se devolve a resposta, usando uma Response. 
Por exemplo, o método
GetMessage passaria a ser definido da seguinte forma: 

```java
public interface FeedsService {
	public static final String HEADER_VERSION = "X-FEEDS-version";
    ...
	@GET
	@Path("/{" + USER + "}/{" + MID + "}")
	@Produces(MediaType.APPLICATION_JSON)
	Message getMessage(@HeaderParam(FeedsService.HEADER_VERSION) Long version, @PathParam(USER) String user, @PathParam(MID) long mid);
    ...
}
```

### Headers nas respostas REST - alternativa 1

Para adicioanr um header a uma resposta há várias alternativas. 

A primeira passa por adicionar os header na resposta de cada método. Assim, a 
implementação do recurso devolveria o resultado da seguinte forma:

```java
@Singleton
public class FeedsResource implements FeedsService {
	...
	@Override
	Message getMessage(Long version, String user, long mid) {
		Log.info("Received getMessage; Version: "+ v+")");
		...
		throw new WebApplicationException(Response.status(200).
			header(FeedsService.HEADER_VERSION, newversion).
            encoding(MediaType.APPLICATION_JSON).entity(msg).build());
	}
}
```

Ou usando como valor de retorno dos métodos *Response* :
```java
@Singleton
public class FeedsResource implements FeedsService {
	...
	@Override
	Response getMessage(Long version, String user, long mid) {
		Log.info("Received getMessage; Version: "+ v+")");
		...
		return Response.status(200).header(FeedsService.HEADER_VERSION, newversion)
			.encoding(MediaType.APPLICATION_JSON).entity(msg).build();
	}
}
```

### Headers nas respostas REST - alternativa 2

Uma segunda alternativa, mais simples, consiste em registar no servidor um filtro
que adiciona o header no momento em que a mensagem com o resultado é enviada 
ao cliente. Notem que neste caso não é possível controlar o valor que será
escrito na resposta, o qual já pode ter sido entretanto alterado.

```java
@Provider
public class VersionFilter implements ContainerResponseFilter {
    ReplicationManager repManager;

    VersionFilter( ReplicationManager repManager) {
        this.repManager = repManager;
    }

    @Override
    public void filter(ContainerRequestContext request, ContainerResponseContext response) 
                throws IOException {
    	response.getHeaders().add(FeedsService.HEADER_VERSION, repManager.getCurrentVersion());
    }
}
```

```java
	ReplicationManager repManager = ...
	ResourceConfig config = new ResourceConfig();
	config.register( FeedsResource.class ); 
	config.register( new VersionFilter( repManager));
    ...
```



**NOTA:** O Tester propagará todos os header que comecem por "X-FEEDS" entre 
pedidos sucessivos feitos aos servidores do mesmo domínio.







