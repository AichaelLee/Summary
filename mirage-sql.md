What's 2WaySQL?
--------
 2WaySQL is just a plain old SQL-Template. You can specify parameters and conditions using special SQL comments. This way the template is executable using SQL client tools. This feature is based on the Japanese O/R mapping framework [S2JDBC](http://s2container.seasar.org/2.4/ja/s2jdbc.html).

1.参数
-----------
你可以使用`/*parameter_name*/`注释来指定参数，但是这个注释块**不能**包含空格或数据库特定的优化器提示，否则** Mirage-SQL **解析器将不会把其识别为**参数**

这有一个最简单的参数注释的用法：

```sql
SELECT * FROM BOOK
WHERE AUTHOR = /*author*/'Naoki Takezoe'
ORDER BY BOOK_ID ASC
```

**`Mirage-SQL`** 会使用默认为`?`的占位符替换所有像`/ * author * /`这样的参数，并且在替换SQL注释之后移除单词（```Naoki Takezoe'```） Mirage-SQL将会执行这样的SQL：

```sql
SELECT * FROM BOOK
WHERE AUTHOR = ?
ORDER BY BOOK_ID ASC
```

2.String Replacement
2.字符串替换
--------------------
 `/*$parameter name*/` performs a simple string replacement. See the following example:
 `/*$parameter name*/` 扮演了一个简单的字符串替换，看节下来的例子：

```sql
SELECT * FROM BOOK
ORDER BY /*$orderByColumn*/BOOK_ID ASC
```

**`Mirage-SQL`** replaces `/*$orderByColumn*/` with the given parameter string value (note the `$` character). This is mere string replacement, not place-holder as in the case of a the `PreparedStatement` above. So **`Mirage-SQL`** checks whether the semicolon is included in the string. If semicolon is included, Mirage-SQL throws an exception.

3.IF, ELSE, END
----------------
 You can assemble SQLs dynamically using `IF`, `ELSE` and `END` comments. The following SQL is a example of an `IF` comment usage. `IF` conditions are written as [OGNL](https://commons.apache.org/proper/commons-ognl/) expressions.

```sql
SELECT * FROM BOOK
/*IF author != null */
  WHERE AUTHOR = /*author*/'Naoki Takezoe'
/*END*/
ORDER BY BOOK_ID ASC
```

 The `ELSE` comment is a little special. You have to write it in a single line comment (not a block comment) because it becomes an executable SQL. See the following example. When you execute it with an SQL-Client tool, the `ELSE` statement is disabled because it's written as single line comment.

```sql
SELECT * FROM BOOK
WHERE
/*IF published == true*/
       PUBLISH_DATE <= SYSDATE
--ELSE PUBLISH_DATE >  SYSDATE
/*END*/
ORDER BY BOOK_ID ASC
```

4.BEGIN, END
------------
 At first, let's analyze the following SQL:

```sql
SELECT * FROM BOOK
WHERE
/*IF minPrice != null*/
      PRICE >= /*minPrice*/1000
/*END*/
/*IF maxPrice != null*/
  AND PRICE <= /*maxPrice*/2000
/*END*/
```

When `minPrice` is null and `maxPrice` is not null, this SQL becomes invalid as follows:

```sql
SELECT * FROM BOOK
WHERE
AND PRICE <= ?
```

Of course, when both of `minPrice` and `maxPrice` are null, it's also invalid:

```sql
SELECT * FROM BOOK
WHERE
```

In these cases, by using `BEGIN` and `END`, the comment can be rewritten as follows:

```sql
SELECT * FROM BOOK
/*BEGIN*/
  WHERE
  /*IF minProce != null*/
        PRICE >= /*minPrice*/1000
  /*END*/
  /*IF maxPrice != null*/
    AND PRICE <= /*maxPrice*/2000
  /*END*/
/*END*/
```

This way, `BEGIN` - `END` comment cuts the enclosed range when the all inner expressions are false. And it also removes `AND` if it's in a first approved expression.

As a result, the conditional expression can be written well by a `BEGIN` - `END`  and `IF` - `END` comment combination.
