越来越多的软件，开始采用云服务。

云服务只是一个统称，可以分成三大类。

![](/assets/import.png)

IaaS

* ：基础设施服务，Infrastructure-as-a-service
* PaaS
  ：平台服务，Platform-as-a-service
* SaaS
  ：软件服务，Software-as-a-service

从左到右，自己承担的工作量（上图蓝色部分）越来越少

对应软件开发，则是下面这张图：

![](/assets/import2.png)

SaaS 是软件的开发、管理、部署都交给第三方，不需要关心技术问题，可以拿来即用。

普通用户接触到的互联网服务，几乎都是 SaaS，下面是一些例子。

> * 客户管理服务 Salesforce
> * 团队协同服务 Google Apps
> * 储存服务 Box
> * 储存服务 Dropbox
> * 社交服务 Facebook / Twitter / Instagram

PaaS 提供软件部署平台（runtime），抽象掉了硬件和操作系统细节，可以无缝地扩展（scaling）。开发者只需要关注自己的业务逻辑，不需要关注底层。

下面这些都属于 PaaS。

> * Heroku
> * Google App Engine
> * OpenShift

IaaS 是云服务的最底层，主要提供一些基础资源。

它与 PaaS 的区别是，用户需要自己控制底层，实现基础设施的使用逻辑。下面这些都属于 IaaS。

> * Amazon EC2
> * Digital Ocean
> * RackSpace Cloud

参考链接

* [SaaS, PaaS and IaaS explained in one graphic](https://m.oursky.com/saas-paas-and-iaas-explained-in-one-graphic-d56c3e6f4606)
  , by David Ng
* [When to use SaaS, PaaS, and IaaS](https://www.computenext.com/blog/when-to-use-saas-paas-and-iaas/)
  , by Eamonn Colman



