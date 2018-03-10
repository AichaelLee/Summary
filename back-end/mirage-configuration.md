jdbc.properties
--------
To use **`Mirage-SQL`** as standalone library, you have to create a **jdbc.properties** file in the classpath root.

```
jdbc.driver=org.h2.Driver
jdbc.url=jdbc:h2:tcp://localhost:9092/test
jdbc.user=sa
jdbc.password=
```
With JDBC 4.0 driver, one could omit the `jdbc.driver` property.

Please note that **`Mirage-SQL`** does not provide connection pooling in it's default configuration. If you want to enable connection pooling, see `DBCPSessionImpl` or `HikariCPSessionImpl` for details, or use a data source that already has a connection pool configured with it.

You can get an instance of `SqlManager` from the `Session`. The `Session` is created by the `SessionFactory` by reading the properties from the `jdbc.properties` file.

```java
Session session = SessionFactory.getSession();
SqlManager sqlManager = session.getSqlManager();
```

As an alternative one can pass the connection (or connection pool) properties directly to the `SessionFactory`, without having the `jdbc.properties` file in the classpath:

```java
Propeties props = ... // get connection settings elswhere than jdbc.properties
Session session = SessionFactory.getSession(props);
SqlManager sqlManager = session.getSqlManager();
```

If you want to use **`Mirage-SQL`** with DI container such as Spring Framework or Google Guice, you have to configure Mirage for these container. See more details in [[Integration]].

OpenSessionInViewFilter
--------
**`Mirage-SQL`** can control transactions automatically in web applications using `OpenSessionInViewFilter`. Register it in your `web.xml` as follows:

```xml
<filter>
  <filter-name>OpenSessionInViewFilter</filter-name>
  <filter-class>com.miragesql.miragesql.filter.OpenSessionInViewFilter</filter-class>
</filter>

<filter-mapping>
  <filter-name>OpenSessionInViewFilter</filter-name>
  <url-pattern>*</url-pattern>
  <dispatcher>REQUEST</dispatcher>
</filter-mapping>
```

This filter begins the transaction at the head of request processing. If no exception in request processing is thrown, this filter will commit transaction by invoking `Session#commit()`. However if any exception is caused, this filter catches it and rollbacks the transaction by invoking `Session#rollback()`.
