# Atualizacões sobre o estudo de jpa-hibernate




<details>
<summary>Operação com SELECT</summary>

## Consultas com JPQL
 
 - Linguagem de consulta orientada a objetos.
 - A sintaxe é similar ao SQL, mas ao invés de referência tabelas e colunas, é usado classes e atributos

### Consultas com parâmetros
```ruby
String jpql = "SELECT nomeObjeto FROM Classe nomeObjeto WHERE nomeObjeto.atributo = :nomeAtributo"
objetoEntityManager.createQuery(jpql, nomeClasse.class).setParameter("nomeAtributo", valorDesejado);
```
### Consultas sem parâmetros
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
