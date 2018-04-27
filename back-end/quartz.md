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