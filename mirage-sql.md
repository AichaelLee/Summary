1.What's Mirage-SQL?
--------
 **`Mirage-SQL`** is an easy and powerful SQL centric database access library.

 There are many Java based ORMs already, but they can't handle complex queries very well. In real-world business applications there's still the requirement to handle native SQLs. Some ORMs do support native SQLs, but most of them can't handle dynamic queries, ([MyBatis](http://www.mybatis.org/mybatis-3/) being one of the few exceptions). Developers still need to assemble dynamic queries using error prone String operations, and due to the [PreparedStatement](http://docs.oracle.com/javase/7/docs/api/java/sql/PreparedStatement.html) shortcomings, the "indexed parameter hell" still remains an unsolved problem.

 **`Mirage-SQL`** supports dynamic queries with **named** parameters (not indexed), all this from external SQL files. Parameters and the dynamic parts are embedded into SQL comments. So basically, an externalized Mirage SQL file is a dynamic SQL template, but it's also a plain SQL, executable by any SQL client tool. This feature: the "dynamic SQL template" of Mirage-SQL is called [[2WaySQL]], and it solves all the issues mentioned above.

 Usually, update operations are simpler to develop than data selection, but applications that make use of allot of such updates are quite tedious to develop. Mirage-SQL also provides an easy way to insert / update / delete records using entity classes or maps (so without the need of writing SQLs).

2.Quick Start
--------
 The [[Configuration]] page details how to setup Mirage-SQL.

### A.First example of Mirage-SQL

 A basic example of searching using a SQL file. `SqlManager` is the main interface of **`Mirage-SQL`**. One can get instance of it from the `Session`. The `Session` is also responsible for transaction handling.

```java
Session session = SessionFactory.getSession();
SqlManager sqlManager = session.getSqlManager();
session.begin();

try {
  BookParam param = new BookParam();

  List<Book> result = sqlManager.getResultList(
      Book.class, new ClasspathSqlResource("/META-INF/selectBooks.sql"), param);

  // ...

  session.commit();

} catch(Exception ex) {
  session.rollback();

} finally {
  session.release();
}
```

Here the `Book` is a POJO to map each row of the ```ResultSet```:

```java
public class Book {
  public Long bookId;
  public String bookName;
  public String author;
  public Integer price;
}
```

The ```BookParam``` is in this case a parameter class:

```java
public class BookParam {
  public String author;
  public Integer minPrice;
  public Integer maxPrice;
}
```
but you can also use a ```HashMap``` with the required parameters as keys.

The corresponding external ```/META-INF/selectProducts.sql``` file that contains
the "dynamic" SQL:

```sql
SELECT * FROM BOOK
/*BEGIN*/
  WHERE
  /*IF author != null */
        AUTHOR = /*author*/'Naoki Takezoe'
  /*END*/
  /*IF minPrice != null */
    AND PRICE >= /*minPrice*/20
  /*END*/
  /*IF maxPrice != null */
    AND PRICE <= /*maxPrice*/100
  /*END*/
/*END*/
ORDER BY BOOK_ID ASC
```

### B.2WaySQL

Mirage supports a feature called [[2WaySQL]]. This is inspired by the Japanese O/R mapping framework [S2JDBC](http://s2container.seasar.org/2.4/ja/s2jdbc.html). 2WaySQL is a plain old SQL-template. You can specify parameters and conditions using simple SQL comments. Because of this, these SQLs can be run directly using any SQL client tool too.

For example, the following SQL statement has the SQL comment(`/*author*/`), but it's also directly runnable in any SQL tool since it has a default value of: (`'Naoki Takezoe'`).

```sql
SELECT * FROM BOOK
WHERE AUTHOR = /*author*/'Naoki Takezoe'
ORDER BY BOOK_ID ASC
```

At runtime, Mirage-SQL replaces `/*author*/` with the placeholder `?` and removes the default value of (`'Naoki Takezoe'`) when it creates a `PreparedStatement`, so **`Mirage-SQL`** would execute the following SQL instead:

```sql
SELECT * FROM BOOK
WHERE AUTHOR = ?
ORDER BY BOOK_ID ASC
```
and would bind the required parameter.

See [[2WaySQL]] to know more details about 2WaySQL.

### C.SQL less Update

Mirage-SQL can also select / insert / update / delete using entity objects or maps (so without using SQL).

The Entity Class must have a primary key field annotated with `@PrimaryKey` or use the convention of naming the primary key with `id`:

```java
public class Book {
  @PrimaryKey(generationType=GenerationType.IDENTITY)
  public Long bookId;
  public String bookName;
  public String author;
  public Integer price;
}
```

One should specify the generation type for primary keys, otherwise the default is used. **`Mirage-SQL`** supports the following generation types:

- GenerationType.APPLICATION

   Primary keys have to be set by the application before invocation of `SqlManager#insertEntity()`.

```java
@PrimaryKey(generationType=GenerationType.APPLICATION)
public Long id;
```

- GenerationType.IDENTITY

   Primary keys are set by database automatically. Database have to support `PreparedStatement.RETURN_GENERATED_KEYS` to acquire generated keys. Oracle does not support this feature.

```java
@PrimaryKey(generationType=GenerationType.IDENTITY)
public Long id;
```

- GenerationType.SEQUENCE

   Primary keys are set by Mirage-SQL using the sequence. With this strategy, you have to specify the sequence name in the generator attribute of `@PrimaryKey` annotation.

```java
@PrimaryKey(generationType=GenerationType.SEQUENCE, generator="USER_ID_SEQ")
public Long id;
```

Example of inserting an entity using `SqlManager#insertEntity()`:

```java
Book book = new Book();
book.bookName = "Mirage in Action";
book.author = "Naoki Takezoe";
book.price = 20;

sqlManager.insertEntity(entity);
```

You can also select an entity using `SqlManager#findEntity()`.

```java
Book book = sqlManager.findEntity(Book.class, bookId);
```

Please note that **`Mirage-SQL`** does not really support relationships handling between entities because it would make the framework far too complex.

### D.Iteration Search

`SqlManager#getResultList()` will instantiate all entities into a list, but for large result sets this might causes `OutOfMemoryError`.

In these cases, one should use `SqlManager#iterate()` instead of the  `SqlManager#getResultList()`:

```java
...
Integer result = sqlManager.iterate(
    Book.class,
    new IterationCallback<Book, Integer>() {

      private int result;

      @Override public Integer iterate(Book entity) {
        result = result + entity.price;
        return result;
      }

    },
    SQL_PREFIX + "SqlManagerImplTest_selectBooks.sql");
```

`SqlManager#iterate()` accepts a `IterationCallback` and invokes it per record. The return value of `SqlManager#iterate()` is the last returned value from the `IterationCallback`.

 If you want to break iteration search on the way, throw `BreakIterationException` from `IterationCallback`. By this exception, iteration search is stopped and `SqlManager#iterate()` returns the previous returned value from `IterationCallback`. If `BreakIterationException` is thrown at the first ```IterationCallback#iterate()``` invocation, than `SqlManager#iterate()` will return `null`.


### E.Stored Procedures / Functions

**`Mirage-SQL`** can also call stored procedures / functions in a database independent manner.

- `SqlManager#call()`

   Calls the procedure or the function which returns the single result.

- `SqlManager#callForList()`

   Calls the function which returns a result list.

```java
// Parameter class
public class GetCountParam {
  @In
  public long deptId;
}

// Creates a parameter object to be passed to the stored procedure / function
GetCountParam param = new GetCountParam();
param.deptId = 1;

// Calls a stored procedure / function and gets a result value.
Long count = sqlManager.call(Long.class, "get_emp_count", param);
```

Fields of the parameter class have annotations such as `@In`, `@Out`, `@InOut` and `@ResultSet`. These annotations indicate the parameter type.

- The field has no annotation or it has `@In` annotation, it means the IN parameter.
- The field has `@Out` annotation, it means the OUT parameter.
- The field has `@InOut` annotation, it means the INOUT parameter.
- The field has `@ResultSet` annotation, it's mapped to the result set which is not returned as a parameter. In the database which can return a result set as a parameter only such as Oracle or PostgreSQL, `@ResultSet` field means the OUT parameter.
