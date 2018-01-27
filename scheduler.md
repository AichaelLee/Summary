### Heroku Scheduler
目录：
安装add-on
小时算Dyno费用
定义task
测试task
调度job
检查输出
长期运行job
Known issues and alternatives    

Scheduler是一个在计划好的时间间隔内运行任务的免费的add-on，它更像传统服务器环境上的cron

> :question: 虽然Scheduler是一个免费的add-on，但是它通过一次性dyno执行计划好的任务，这会产生费用

仪表盘允许你配置自己的任务每十分钟运行一次，每小时运行一次，或者每天运行一次，以指定的时间。
当调用的时候，这些任务将会在一次性d y n o上面运行，并且会作为一个名字像scheduler.X 一样的dyno输出到你的日志上。

### 安装add-on
为了使用heroku scheduler，你需要安装add-on：
`heroku addons:create scheduler:standard`
### 每小时的花费
Heroku scheduler运行在一次性dyno上面，会在你的月账单上产生费用。
Dyno-hours from Scheduler tasks are counted just like those from heroku run or from scaled dynos. 
They will appear with a “scheduler” dyno type in your Heroku invoice.

他们会以"scheduler“ dyno的类型出现在你的Heroku账单上

### 定义task

