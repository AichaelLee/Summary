# quartz
## 特点：
### 强大的调度功能
### 灵活的应用方式
### 分布式和集群能力
## 主要用到的设计模式
### Builder模式
### Factory模式
### 组件模式
### 链式写法
## Quartz体系结构
* 三个核心概念：
调度
任务
触发器
* 重要组成：
Job、JobBuilder 、JobDetail、 JobStore(保存到内存中)、 Trigger（触发规则Simple trigger 和 crontrigger）、ThreadPool、TriggerBuilder、Scheduler、calendar（一个Trigger可以和多个Calendar关联，以排除或者包含某些时间点）、监听器（JobListener、TriggerListener、SchedulerListener)

quartz job concurrent 并发设置
2015年12月06日 15:52:34
阅读数：5534
Your job class needs to implement StatefulJob, or alternatively, if you are using Quartz 2.x, be annotated with @DisallowConcurrentExecution.

This is how concurrent execution can be controlled in Quartz, and in particular MethodInvokingJobDetailFactoryBean just uses different job classes depending on the value of concurrent.


quartz job有时我们希望当前的job执行后，再执行下次的job，有两种方法可实现
1、job类实现StatefulJob接口
2、
 在job类前加上@DisallowConcurrentExecution



有时我们不希望程序一次动就执行job等
 待下次触发，在quartz.properties中加上：
 org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread=true