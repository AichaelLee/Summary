## 简介

如今，软件通常会作为一种服务来交付，它们被称为网络应用程序，或软件即服务（SaaS）。12-Factor 为构建如下的 SaaS 应用提供了方法论：

* 使用标准化流程自动配置，从而使新的开发者花费最少的学习成本加入这个项目。和操作系统之间尽可能的划清界限，在各个系统中提供最大的可移植性

* 适合部署在现代的云计算平台，从而在服务器和系统管理方面节省资源。
* 将开发环境和生产环境的
  差异降至最低
  ，并使用
  持续交付
  实施敏捷开发。
* 可以在工具、架构和开发流程不发生明显变化的前提下实现
  扩展
  。

这套理论适用于任意语言和后端服务（数据库、消息队列、缓存等）开发的应用程序。

## 背景

本文的贡献者者参与过数以百计的应用程序的开发和部署，并通过

[Heroku](http://www.heroku.com/)

平台间接见证了数十万应用程序的开发，运作以及扩展的过程。

本文综合了我们关于 SaaS 应用几乎所有的经验和智慧，是开发此类应用的理想实践标准，并特别关注于应用程序如何保持良性成长，开发者之间如何进行有效的代码协作，以及如何

[避免软件污染](http://blog.heroku.com/archives/2011/6/28/the_new_heroku_4_erosion_resistance_explicit_contracts/)

。

我们的初衷是分享在现代软件开发过程中发现的一些系统性问题，并加深对这些问题的认识。我们提供了讨论这些问题时所需的共享词汇，同时使用相关术语给出一套针对这些问题的广义解决方案。本文格式的灵感来自于 Martin Fowler 的书籍：

[Patterns of Enterprise Application Architecture](http://books.google.com/books/about/Patterns_of_enterprise_application_archi.html?id=FyWZt5DdvFkC)

，

[Refactoring](http://books.google.com/books/about/Refactoring.html?id=1MsETFPD3I0C)

。

读者应该是哪些人？

任何 SaaS 应用的开发人员。部署和管理此类应用的运维工程师。

12-factors

[I. 基准代码](https://12factor.net/zh_cn/codebase)

一份基准代码，多份部署

[II. 依赖](https://12factor.net/zh_cn/dependencies)

显式声明依赖关系

[III. 配置](https://12factor.net/zh_cn/config)

在环境中存储配置

[IV. 后端服务](https://12factor.net/zh_cn/backing-services)

把后端服务当作附加资源

[V. 构建，发布，运行](https://12factor.net/zh_cn/build-release-run)

严格分离构建和运行

[VI. 进程](https://12factor.net/zh_cn/processes)

以一个或多个无状态进程运行应用

[VII. 端口绑定](https://12factor.net/zh_cn/port-binding)

通过端口绑定提供服务

[VIII. 并发](https://12factor.net/zh_cn/concurrency)

通过进程模型进行扩展

[IX. 易处理](https://12factor.net/zh_cn/disposability)

快速启动和优雅终止可最大化健壮性

[X. 开发环境与线上环境等价](https://12factor.net/zh_cn/dev-prod-parity)

尽可能的保持开发，预发布，线上环境相同

[XI. 日志](https://12factor.net/zh_cn/logs)

把日志当作事件流

[XII. 管理进程](https://12factor.net/zh_cn/admin-processes)

后台管理任务当作一次性进程运行

I. 基准代码
一份基准代码（Codebase），多份部署（deploy）

12-Factor应用(译者注：应该是说一个使用本文概念来设计的应用，下同)通常会使用版本控制系统加以管理，如Git, Mercurial, Subversion。一份用来跟踪代码所有修订版本的数据库被称作 代码库（code repository, code repo, repo）。

在类似 SVN 这样的集中式版本控制系统中，基准代码 就是指控制系统中的这一份代码库；而在 Git 那样的分布式版本控制系统中，基准代码 则是指最上游的那份代码库。

一份代码库对应多份部署

基准代码和应用之间总是保持一一对应的关系：

一旦有多个基准代码，就不能称为一个应用，而是一个分布式系统。分布式系统中的每一个组件都是一个应用，每一个应用可以分别使用 12-Factor 进行开发。
多个应用共享一份基准代码是有悖于 12-Factor 原则的。解决方案是将共享的代码拆分为独立的类库，然后使用 依赖管理 策略去加载它们。
尽管每个应用只对应一份基准代码，但可以同时存在多份部署。每份 部署 相当于运行了一个应用的实例。通常会有一个生产环境，一个或多个预发布环境。此外，每个开发人员都会在自己本地环境运行一个应用实例，这些都相当于一份部署。

所有部署的基准代码相同，但每份部署可以使用其不同的版本。比如，开发人员可能有一些提交还没有同步至预发布环境；预发布环境也有一些提交没有同步至生产环境。但它们都共享一份基准代码，我们就认为它们只是相同应用的不同部署而已。

