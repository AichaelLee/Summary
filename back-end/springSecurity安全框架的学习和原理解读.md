## Spring security框架简介

     ### 简介

一个能够为基于Spring的企业应用系统提供声明式的安全訪问控制解决方式的安全框架（简单说是对访问权限进行控制），
应用的安全性包括用户认证（Authentication）和用户授权（Authorization）两个部分。用户认证指的是验证某个用户是否为系统中的合法主体，
也就是说用户能否访问该系统。用户认证一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认证过程。
用户授权指的是验证某个用户是否有权限执行某个操作。在一个系统中，不同用户所具有的权限是不同的。
比如对一个文件来说，有的用户只能进行读取，而有的用户可以进行修改。一般来说，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的权限。 
spring security的主要核心功能为 认证和授权，所有的架构也是基于这两个核心功能去实现的。

### spring security框架原理
     众所周知 想要对对Web资源进行保护，最好的办法莫过于Filter，要想对方法调用进行保护，最好的办法莫过于AOP。
     所以springSecurity在我们进行用户认证以及授予权限的时候，通过各种各样的拦截器来控制权限的访问，从而实现安全。
        如下为其主要过滤器  

        WebAsyncManagerIntegrationFilter 
        SecurityContextPersistenceFilter 
        HeaderWriterFilter 
        CorsFilter 
        LogoutFilter
        RequestCacheAwareFilter
        SecurityContextHolderAwareRequestFilter
        AnonymousAuthenticationFilter
        SessionManagementFilter
        ExceptionTranslationFilter
        FilterSecurityInterceptor
        UsernamePasswordAuthenticationFilter
        BasicAuthenticationFilter
        
       ###   3、框架的核心组件

      SecurityContextHolder：提供对SecurityContext的访问
      SecurityContext,：持有Authentication对象和其他可能需要的信息
      AuthenticationManager 其中可以包含多个AuthenticationProvider
      ProviderManager对象为AuthenticationManager接口的实现类
      AuthenticationProvider 主要用来进行认证操作的类 调用其中的authenticate()方法去进行认证操作
      Authentication：Spring Security方式的认证主体
      GrantedAuthority：对认证主题的应用层面的授权，含当前用户的权限信息，通常使用角色表示
     UserDetails：构建Authentication对象必须的信息，可以自定义，可能需要访问DB得到
      UserDetailsService：通过username构建UserDetails对象，通过loadUserByUsername根据userName获取UserDetail对象 （可以在这里基于自身业务进行自定义的实现  如通过数据库，xml,缓存获取等）           

--------------------- 
作者：liushangzaibeijing 
来源：CSDN 
原文：https://blog.csdn.net/liushangzaibeijing/article/details/81220610 
版权声明：本文为博主原创文章，转载请附上博文链接！

--------------------- 
作者：liushangzaibeijing 
来源：CSDN 
原文：https://blog.csdn.net/liushangzaibeijing/article/details/81220610 
版权声明：本文为博主原创文章，转载请附上博文链接！
--------------------- 
作者：liushangzaibeijing 
来源：CSDN 
原文：https://blog.csdn.net/liushangzaibeijing/article/details/81220610 
版权声明：本文为博主原创文章，转载请附上博文链接！
