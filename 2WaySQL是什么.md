2WaySQL是什么?
--------
 2WaySQL就是一个普通的通用SQL模板，你可以通过特殊的SQL注释块指定参数和条件。这样当你使用SQL客户端工具的时候模板也是可以执行的。这一特性是建立在日本的O/R 映射框架[S2JDBC](http://s2container.seasar.org/2.4/ja/s2jdbc.html)之上的

1.参数
-----------
你可以使用`/*parameter_name*/`注释来指定参数，但是这个注释块**不能**包含空格或数据库特定的优化器提示，否则** Mirage-SQL ** 解析器将不会把其识别为**参数**

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

**`Mirage-SQL`** 使用给定的参数字符串值替换`/*$orderByColumn*/`（注意`$`符号）。这仅仅是字符串的替换，而不像上面的`PreparedStatement`是占位符。所以**`Mirage-SQL`** 会检查字符串中是否包含有分号，如果包含分号，Mirage-SQL就会抛出异常。

3.IF, ELSE, END
----------------
你也可以使用`IF`, `ELSE` and `END`注释动态地合成SQL语句，下面这条SQL语句就是使用IF注释用法的一个示例，`IF`条件写成OGNL表达式的形式：

```sql
SELECT * FROM BOOK
/*IF author != null */
  WHERE AUTHOR = /*author*/'Naoki Takezoe'
/*END*/
ORDER BY BOOK_ID ASC
```

 `ELSE`注释有一点特别，你必须写成单行注释的形式（而不能以注释块的形式），因为他将成为一条被执行的SQL语句。看下面的示例。当你使用SQL客户端工具执行的时候，因为它被写成单行注释的形式，所以`ELSE`语句是不执行的。

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
 首先，让我们分析下面的这条SQL语句：

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

当`minPrice`为空并且`maxPrice`不为空的时候，这条SQL语句实际是无效的：

```sql
SELECT * FROM BOOK
WHERE
AND PRICE <= ?
```

当然，当`minPrice`和`maxPrice`都为空的时候，它仍然是无效的：

```sql
SELECT * FROM BOOK
WHERE
```

在这些case中，通过使用`BEGIN`和`END`，注释可以被写成如下形式：

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
这样，当所有的内联表达式都是false的时候，`BEGIN` - `END`注释块就减少了封闭的范围，而且如果它在第一个通过表达式中，也会移除`AND`。

因此，通过`BEGIN` - `END`  and `IF` - `END`注释块的组合就能够很好地写出条件表达式了。
