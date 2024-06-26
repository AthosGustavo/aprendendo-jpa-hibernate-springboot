 # Atualizacões sobre o estudo de jpa-hibernate e Spring boot

<details>
<summary>Entidades</summary>

<details>
<summary>Anotções da JPA</summary>

**@Entity**
 - Usada para marcar uma classe como uma entidade persistente.

**@Table(name = "nome da tabela no banco")**
 - Especifica detalhes sobre a tabela do banco de dados à qual a entidade está mapeada, como o nome da tabela, esquema, etc.

**@Id**
 - Marca um campo como a chave primária da entidade.

**@GeneratedValue(strategy = GenerationType.IDENTITY)**
 - Anotação para sinalizar um atibuto como autoincremento para a tabela

</details>

<details>
<summary>Mapeando relacionamento</summary>

 ### Relacionamento OneToMany - ManyToOne

 Iremos relacionar as entidades professor e disciplina
  - 1 professor pode estar associado a várias disciplínas
  - 1 disciplína pode estar associada apenas a um professor

```java
public class Disciplina(){

   private Long id
   private String nome
   private Integer semestre

   @ManyToOne   //ler-se:várias classe disciplinas pode estar associado a um professor
   private Professor professor
}

```

```java
public class Professor(){

   private Long Id
   private String nome

   @OneToMany   //ler-se:uma classe professor pode ter várias classe disciplina
   private List<Disciplinas> listaDisciplinas;
}

```

 - Após rodar a aplicação, o spring detecta um relacionamento e cria automaticamente uma tabela de associação,exemplo: professor_disciplina.

   - Caso o objetivo não seja esse,podemos sinalizar outro modo de relacionamento para o spring não for o objetivo,fazer a anotação @JoinColum

   - Além disso,a declaração do mapeamento sempre será feito no lado Many e o mapped será declarado no lado One

```java
public class Disciplina(){

   private Long id
   private String nome
   private Integer semestre

   @ManyToOne   //ler-se:várias classe disciplinas pode estar associado a um professor
   @JoinColumn(name = "professor_id")	//professor_id será a coluna que fará referência a chave primária da tabela professor. chave (estrangeira: professor_id)
   private Professor professor
}

```

```java
public class Professor(){

   private Long Id
   private String nome

   @OneToMany(mappedBy = "professor")
   private List<Disciplinas> listaDisciplinas;
}

```

 
 
 ### ManyToOne
 
 - Ao sinalizar que um atributo da classe produto será do tipo categoria, o hibernate identifica a ocorrência de um relacionamento.Após isso é necessário sinalizar a cima do atributo a cardinalidade do relacionamento(@ManyToOne).

```ruby
@Entity
@Table(name = "produtos")
public class Produto {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
private String nome;
private String descricao;
private BigDecimal preco;
private LocalDate dataCadastro = LocalDate.now();
	
@ManyToOne
private Categoria categoria;
```

```ruby
@Entity
@Table(name = "categorias")
public class Categoria {
	
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
private String nome;
	
```
### ManyToMany

 - Em um relacionamento ManyToMany que envolva uma terceira tabela intermediária, deve ser usada a anotação @JoinTable

 - SINTAXE @JoinTable
```ruby
@JoinTable(name = "nome_terceira_tabela",
joinColumns = @JoinColumn(name = nome da coluna que irá representar o lado palestra na terceira tabe; ("nome_id")),
inverseJoinColumns = @JoinColumn(name = nome da coluna que irá representar o lado palestrante na terceira tabela;("nome_id"))
```
 - A anotação mappedBy é usado na outra classe para sinalizar onde o mapeamento foi declarado
```ruby
@ManytoMany(mappedBy = "nomeTabela")
```
#### EXEMPLO

```ruby
import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "palestra")
public class Palestra {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private Long id;

    @Column(name = "titulo")
    private String titulo;

    @ManyToMany
    @JoinTable(name = "palestra_palestrante",
               joinColumns = @JoinColumn(name = "palestra_id"),
               inverseJoinColumns = @JoinColumn(name = "palestrante_id"))
    private Set<Palestrante> palestrantes = new HashSet<>();

}

```

```ruby
import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "palestrante")
public class Palestrante {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private Long id;

    @Column(name = "nome")
    private String nome;

    @ManyToMany(mappedBy = "palestrantes")
    private Set<Palestra> palestras = new HashSet<>();

   
}


```


temos a tabela pedidos, produtos e itens_pedidos em uma relação de muitos para muitos
itens_pedidos é a tabela intermediária

relacionamento bidirecional deve ser sinalizado



Em resumo, ao usar mappedBy = "palestrantes", estamos indicando que o mapeamento do relacionamento Many-to-Many é definido na classe Palestra

Isso permite que o Hibernate entenda como as tabelas e colunas estão interconectadas








</details>


 
</details>

<details>
<summary>Cíclo de vida de uma entidade</summary>

![cicli-de-vida-hibernate-3](https://github.com/AthosGustavo/jpa-hibernate/assets/112649935/0f224cdd-acf1-4e0e-938f-59ed413560ea)

 - TRANSIENT: O estado TRANSIENT representa o momento queo objeto foi instância, porém não foi persistido, não foi inserido no banco

 - MANAGED: Após persistir, o objeto passa para o estado MANAGED, gerenciado.Mesmo após persistido qualquer tipo de modificação feita antes do método flush ou commit, será aceito pelo objeto.
 - FLUSH: O método flush serve para sincronizar os dados com o banco.É utilizado em situações onde uma determinada modificação não será feita de uma vez só.
 - COMMIT: O método commit serve para sincronizar e confirmar as modificações.
 - CLOSE: O método close/clear serve para encerrar a operação.Então qualquer tipo de modificação feita após esse método não será aplicada.
 - MERGE: merge() volta uma entidade para  o estado de gerenciado.Isso permite remover e atualizar o objeto
    - O método merge(entidade) devolve uma nova referência do objeto celulares para manager e não a sua referência original.Sendo assim é necessário atribuir a variável original para a variável recente.
    - ``` variavelReferencia = objEntityManager.merge(variavelReferencia)```

</details>


<details>
<summary>Operação com SELECT</summary>

## Consultas com JPQL
 
 - Linguagem de consulta orientada a objetos.
 - A sintaxe é similar ao SQL, mas ao invés de referência tabelas e colunas, é usado classes e atributos

<details>
<summary>Consultas com parâmetros</summary>summary

```ruby
String jpql = "SELECT nomeObjeto FROM Classe nomeObjeto WHERE nomeObjeto.atributo = :nomeAtributo"
objetoEntityManager.createQuery(jpql, nomeClasse.class).setParameter("nomeAtributo", valorDesejado);
```
</details>

<details>
<summary>Consultas sem parâmetros</summary>

```ruby
String jpql = "SELECT nomeObjeto FROM Classe nomeObjeto"
```
 - createQuery
   - createQuery é um método de um objeto EntityManager
   - createQuery retorna um objeto
   - O objeto possui um método para retornar uma lista de resultados getResultList()
   - Outro para retornar um único resultado getSingleResult
 - Sintaxe createQuery
   ```ruby
    createQuery(nomeVarialQuery, nomeClasse.class)
   ```
   - nomeClasse.class serve para sinalizar ao método o tipo da lista que será devolvido

#### EXEMPLO

```ruby
public List<Produto> buscarTodos(){
  EntityManagerFactory emf = Persistence.createEntityManagerFactory("name persistence-unit");
  EntityManager em = emf.createEntityManager();

  String jpql = "SELECT p FROM Produto p";
  return em.createQuery(jpql, Produto.class).getResultList()
  em.close();
  emf.close();

};
```

```ruby
List<Produto> todos = produtoDao.buscarTodos();

todos.forEach(p -> System.out.println(p.getNome()));
```
</details>

<details>
<summary>Consultas utilizando .find()</summary>summary
 - .find() é um método da interface EntityManager que permite buscar uma entidade por usa chave primária
sintaxe do método.

 ```ruby
 objEntityManager.find(nomeClasse.Class, id);
```

#### EXEMPLO
```ruby
public Produto buscarPorId(Long id) {
		return em.find(Produto.class, id);
}

EntityManagerFactory emf = Persistence.createEntityManagerFactory("name persistence-unit");
EntityManager em = emf.createEntityManager();

ProdutoDao produtoDao = new ProdutoDao(em);
Produto p = produtoDao.buscarPorId(1l);
System.out.println(p.getPreco());
```
</details>

</details>

<details>
<summary>Operação de INSERT</summary>

### Método getTransaction()

```ruby
EntityManagerFactory emf = Persistence.createEntityManagerFactory('name persistence-unit');
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();
```

getTransaction é um método da classe EntityManager, EntityManager é uma interface usada para interagir com as operações de persistência, como salvar, atualizar, excluir ou consultar.Além disso a interface fornece métodos para controlar as transações que envolvem as operações de banco de dados; begin(),commit(),(rollback).

 - begin()
    - É sinalizado que uma sequencia de operações no banco de dados será iniciada.Essas sequências são tratadas como únicas e caso ocorrer erro em uma,toda a operação é cancelada, assim visando a integridade do banco de dados.

 - commit()
    - Após realizar as operações no banco de dados,a transação deve ser finalizada com commit();

### Método persist()
 - O método persist é usado para inserir um objeto no banco de dados
 - O método pertence ao objeto EntityManager

#### EXEMPLO
```ruby
public static void main(String[] args) {

        Pessoa p1 = new Pessoa(null, "Carlos da Silva", "carlos@gmail.com");
        Pessoa p2 = new Pessoa(null, "Joaquim Torres", "joaquim@gmail.com");
        Pessoa p3 = new Pessoa(null, "Ana Maria", "ana@gmail.com");

        EntityManagerFactory emf = Persistence.createEntityManagerFactory("exemplo-jpa");
        EntityManager em = emf.createEntityManager();

        em.getTransaction().begin();

        //Metodo para inserir o objeto no banco de dados
        em.persist(p1);
        em.persist(p2);
        em.persist(p3);

        em.getTransaction().commit();

        System.out.println("Pronto!");
        em.close();
        emf.close();
    }
}
```

#### Método getTransaction em operações de SELECT
Em operações de SELECT não é necessário iniciar ou commitar uma transação, pois não foi feito modificações no banco de dados.
</details>

<details>
 <summary>Operação de DELETE</summary>
 
 - Para remover um objeto, basta usar o método remove quando este objeto estiver no estado MANAGED
 
 ```ruby
objEntityManager.remove(nomeObjeto)
```
</details>

<details>
<summary>Spring boot</summary>

<details>
<summary>Configurando o arquivo pom.xml</summary>

**Spring Boot DevTools**
 - A dependência permiti fazer alterações no projeto sem a necessidade de reiniciar o servidor.Na IDE intellij ainda é necessári fazer uma configuracão no sistema.

**Lombok**
 - Permiti diminuir a declaração de códigos a partir de anotações.

**Spring Web**
 - Um série de dependênias abstratas que peritem desenvolver uma aplicação web contendo servidor, requisições e segurança.

**Spring Data JPA**
 - simplifica a interação com bancos de dados relacionais
 - introduz o conceito de repositórios, que são interfaces Java que definem métodos para acessar o banco de dados.

**Conector mysql**
 - permiti o java se conectar com o banco de dados mysql atraves do seu driver

</details>

<details>
<summary>Configurando o arquivo application.properties</summary>

```
spring.datasource.url=jdbc:mysql://localhost:3306/nomeDoSchema
spring.datasource.username=nomeUsuario
spring.datasource.password=senhaUsuario
spring.jpa.hibernate.ddl-auto=update
logging.level.org.springframework.web=TRACE	//Exibe logs HTTP no console
spring.jpa.generate-ddl=true			//Gera automaticamente o schema do banco de dados com base nas entidade
spring.jpa.show.sql=true			//Exibe o SQL de uma consulta
```

Configurando a propriedade spring.jpa.hibernate.ddl-auto
**create**
 - cria as tabelas no banco de dados sempre que o aplicativo for iniciado.

**update**
 - atualiza automaticamente o esquema do banco de dados sempre que há uma diferença entre o modelo de dados definido nas classes Java e o esquema real do banco de dados. Ele não recria as tabelas, mas pode adicionar, alterar ou excluir colunas.

**validade**
 - valida o esquema do banco de dados em relação às classes de entidade definidas, mas não faz alterações no banco de dados. Se houver alguma diferença entre o esquema do banco de dados e as classes de entidade, uma exceção será lançada.

**none**
 - desabilita completamente a geração e atualização automáticas de esquema pelo Hibernate. 

</details>

<details>
<summary>Lombok</summary>

### Lombok

**@Getter**
 - Anotação usada para gerar automaticamente os getter dos atributos

**@Setter**
 - Anotação usada para gerar automaticamente os setter dos atributos

**@NoArgsConstructor**
 - Anotação usada para criar um construtor padrão sem argumentos para entidade

**AllArgsConstructor**
 - Anotação usada para criar um construtor com todos os argumentos

</details>


<details>
<summary>Metodo POST</summary>

```java

@RequestMapping("medicos") 
public class MedicoController{

   @PostMapping
   public void cadastrar(@RequestBody String json){
    System.out.println(json);
  }
}
```

### @RestController
- usada para marcar uma classe como um controlador 

### @RequestMapping
- usada para sinalizar/ mapear metodos de um controlador.Define que todas as requisicoes feitas para um caminho (medicos) devem ser tratadas por esse controlador.

- public class MedicoController
classe do controlador.Contem os metodos que serao executados quando as requisicoes chegarem ao caminho mapeado
- A classe controller e mapeada para a url medicos
dentro da classe controller estaram contidos os metodos que utilizaram o endpoint medicos
	
### @PostMapping
- Usado para mapear métodos que respondem a requisições do tipo POST

### @RequestBody
 - O metodo cadastrar recebe um variável no parâmetro que representará os dados no corpo da requisição, no entanto o spring não sabe disso e é necessário usar esaa anotação para sinalizar.

#### EXEMPLO

```java
package med.voll.api.controller;

import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestBody;

@RestController
@RequestMapping("medicos")
public class MedicoController {

        @PostMapping
        public void cadastrar(@RequestBody DadosCadastroMedico dados) {
              System.out.println(dados);
    }

}
```
```java
package med.voll.api.medico;

public record DadosCadastroMedico(String nome, String email, String crm) {
}
```

- DadosCadastroMedico classe record que receberá os dados enviados do front-end

Concluindo o método post com repository
Para finalizar o método post, basta realizar a injeção de dependências com repository
o código atualizado ficará da seguinte forma

### Interface repoitory
 - Sintaxe: <Entidade, tipo-atributo-chave-primaria-entidade>

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface MedicoRepository extends JpaRepository<Medico, Long>{
}

```
 - Método construtor feito para receber os dados como parâmetro

```java
public Medico(DadosCadastroMedico dados){
  this.nome = dados.nome();
  this.email = dados.email();
  this.crm = dados.crm();

}
	
```

```java
package med.voll.api.controller;

import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestBody;

@RestController
@RequestMapping("medicos")
public class MedicoController {

	@Autowired
	private MedicoRepository repository

	@PostMapping
        public void cadastrar(@RequestBody DadosCadastroMedico dados) {
              repository.save(new Medico(dados))
    }

}
```
**@transactional**
 - Assemelha-se ao getTransaction utilizado na jpa com hibernate
 - A anotação deve ser colocada a cima da anotação @PutMapping ou @PostMapping
 - trata as operações como um átomo, para ser comitada, toda a operação deve ter sucesso, caso ocorrer erro em uma parte, toda a operação será cancelada e a opção de rollback será disponibilizada.Além disso, em casos de erro, o rollback é executado de forma automárica.diferente do metodo getTransactional da JPA


interface repository
public interface nomeInterface extends JpaRepository<nomEntidade, tipo atributo da chave primaria>
</details>
<details>
<summary>Método GET</summary>

@GetMapping

Como ja foi dito, a classe DTO é usada para transferir dados do front-end para o back-end e vice-versa
o metodo findAll  devolve uma lista do tipo da Entidade e essa entidade deve ser convertida para uma DTO


```java
package med.voll.api.medico;

public record DadosListagemMedico(String nome, String email, String crm, Especialidade especialidade) {

    public DadosListagemMedico(Medico medico) {
        this(medico.getNome(), medico.getEmail(), medico.getCrm(), medico.getEspecialidade());
    }

}

```

```java
@GetMapping
public List<DadosListagemMedicos> listar(){
  return repository.findAll().stream.map(DadosListagemMedicos:: new).toList();
}
```

public List<>
 
</details>

<details>
<summary>Método PUT</summary>

```java
@PutMapping
@Transactional
public void atualizar(@RequestBody DadosAtualizaAluno dados){
  var aluno = alunoRepository.getReferenceById(dados.idAluno());
  aluno.atualizarInfo(dados);
}
```
```java
public void atualizarInfo(DadosAtualizaAluno dados) {

  if(dados.idadeAluno() > 0){
    this.idadeAluno = dados.idadeAluno();
  }
  if(dados.nomeAluno() != null) this.nomeAluno = dados.nomeAluno();

}
```

</details>
<details>
<summary>Método DELETE</summary>

```java
@DeleteMapping("/{id}")
  public void excluir(@PathVariable Long id){
  alunoRepository.deleteById(id);
}
```
</details>




@Embbeded

<details>
<summary>DTO e RECORD</summary>

### DTO
 - Usada na transferência de dados entre camadas.Nesse caso é utilizado no java para receber dados do front-end
 - encapsulam os dados em formato de objeto
  - Exemplo de como é utilizada uma DTO:
No envio de dados de um formulario para o backend, a dto seria a classe que iria armazenar esses dados
por esse motivo,a depender da regra de negocio,seus atributos sao imutaveis, ou seja, seus valores nao podem ser alterados apos envio

#### Caracteristicas e usabilidade
 - Utilizada na transferencia de dados
 - Não possuem lógica de negócio

#### Estrutura de uma DTO
 - Possuem atributos e métodos construtores


### RECORD
 - O record foi criado para facilitar a declaracão de DTO`S. enquanto uma DTO é necessário declarar seus atributos no corpo da classe,
uma classe record recebe os atributos no parâmetro da funcão e por de baixo dos panos faz os métodos get, set e entre outros.

 - para acessar um atributo não é necessário sinalizar os get e set, além disso já reconhece os atributos como final
 - Cria construtores automáticos com os atributos passados em parâmetro
 - record aceitam métodos,atributos estaticos e também método estaticos

#### Classe DTO sem RECORD

```java
public class PessoaDTO {
    private String nome;
    private int idade;

    public PessoaDTO(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public int getIdade() {
        return idade;
    }

    public void setIdade(int idade) {
        this.idade = idade;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        PessoaDTO pessoaDTO = (PessoaDTO) o;
        return idade == pessoaDTO.idade && Objects.equals(nome, pessoaDTO.nome);
    }

    @Override
    public int hashCode() {
        return Objects.hash(nome, idade);
    }

    @Override
    public String toString() {
        return "PessoaDTO{" +
                "nome='" + nome + '\'' +
                ", idade=" + idade +
                '}';
    }
}


```

#### Classe DTO com RECORD

```java
public record PessoaDTO(String nome, int idade) {}

```

#### Particularidades da classe Record
 - Em uma classe record as variaveis passadas no parâmetro representam ao mesmo tempo os atributos da classe.
 - Além disso, a classe record é ao mesmo tempo um construtor padrao, basta observar a sua sintaxe.
 - Valores no parâmetro: Ao mesmo tempo sao atributos e variáveis.
 - Devido ao fato da classe ja ser um construtor, apenas é permitido criar outro construtor que declare e utilize os mesmo valores do construtor padrao.

**Casos em que será preciso iniciar o construtor com valores diferentes**
 - O Construtor recebe apenas variáveis e não objeto
    - Nesse caso cria-se um novo construtor que recebe o objeto
    - Usa-se o com parenteses para fazer referencia a todos os atributos/variaveis da classe record this()
    - Dentro do parenteses acessamos cada variavel atraves da notacao "objeto.getAtributo".

```java
public record getClienteDTO(String nomeCliente, String emailCliente, String telCliente, String enderecoCliente) {

  public getClienteDTO(Cliente cliente) {
    //Estrategia:construtor de chamada
    this(cliente.getNomeCliente(),cliente.getEmailCliente(),cliente.getTelCliente(),cliente.getEnderecoCliente());

  }
}
```
*Explicação*
 - Nesse caso cria-se um novo construtor que recebe o objeto
 - Usa-se o this() com parenteses para fazer referencia a todos os atributos/variaveis da classe record
 - Dentro do parenteses do this acessamos cada variavel atraves da notacao "objeto.getAtributo".

</details>



<details>
<summary>Flyway:migration</summary>


### Flyway


A ferramenta flyway é usada para controle de versionamento de banco de dados, assim como o git é usado com código

**Situações que migration é usada**

migration é usado em situações que pedem alterações no banco de dados após a criação do banco, exemplo:
 - criação e exclusão de tabelas ou colunas
 - correção de nomes e valores
 
**Passo a passo**
 
 - Para utilizar uma migration é necessário importar as suas dependencias no spring initializr
 - db.migration é a pasta onde ficará guardado o arquivo .sql com a query
 - dentro do arquivo sql vai a query
 - Para criar uma nova migration, basta criar um novo arquivo .sql dentro da pasta
 - OBS:Antes de realizar uma migration,é necessário parar o servidor.
</details>

</details>

</details>


</details>
<details>
<summary>Bean validation e Hibernate validation</summary>

### Bean Validation é uma api de validação de entrada de dados
```
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.2.0.Final</version> <!-- Use a versão mais recente disponível -->
</dependency>

```

**Anotações de validação**

- @NotNull
   -  usada para garantir que um campo não seja nulo
- @Size
   -  usada para verificar o tamanho de uma string,array e etc
- @Min
  - Usado para validar que um valor numérico deve ser igual ou superior a um valor estipulado
- @Max
  - Usado para validar que um valor numérico deve ser igual ou superior a um valor estipulado
- @Email
   - usada para verificar se o dado possui a formatação de um email
- @Valid
   - usado para ativar a validação de objetos em um método do controlador.Isso indica ao Spring que a validação deve ocorrer antes que o métdo do controlador seja executado.
- @NotBlank
   - Garante que o valor da string não seja nulo e que não consista apenas de espaços em branco.

**Outros tipos de validação**

- @NotEmpty: Garante que o valor do campo não seja nulo e que o tamanho da coleção ou da string não seja zero.

- @Pattern: Permite que você defina um padrão de expressão regular que o campo deve atender.

- @URL: Valida se o campo é uma URL válida.

- @Future e @Past: Verificam se a data está no futuro ou no passado, respectivamente.

- @Digits: Especifica o número máximo de dígitos e casas decimais permitidas em campos numéricos.

- @Range: Define um intervalo de valores permitidos para campos numéricos ou datas.

- @CreditCardNumber: Valida números de cartão de crédito.

- @CreditCardExpiration: Valida datas de validade de cartões de crédito.

- @AssertTrue e @AssertFalse: Verificam se uma expressão booleana é verdadeira ou falsa.

- @Valid: Usado para habilitar a validação no objeto enviado no corpo da solicitação.

#### EXEMPLO

```java
public class Usuario {
    @NotNull
    private String nome;

    @Email
    private String email;
}

```
```java
@RestController
public class UsuarioController {

    @PostMapping("/usuarios")
    public ResponseEntity<String> cadastrarUsuario(@Valid @RequestBody Usuario usuario) {
        // Lógica para cadastrar o usuário
        return ResponseEntity.ok("Usuário cadastrado com sucesso!");
    }
}


```
</detalis>





