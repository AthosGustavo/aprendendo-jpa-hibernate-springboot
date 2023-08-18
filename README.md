S# Atualizacões sobre o estudo de jpa-hibernate

<details>
<summary>Entidades</summary>

<details>
<summary>Mapeando relacionamento</summary>

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

método post
```ruby


```

 
</details>
