
`前提：`因为工作需要，要对系统产生的所有SQL文进行监控并记录分析，目前定的解决方案是应用运行期间产生的SQL文输送到rabbitmq中，然后通过logstash插入到elasticsearch，然后通过kibana进行数据分析分析

## 1.安装rabbitMQ

我是在我的VPS上面安装的，系统版本是centos 6.8 ，因为系统版本的问题可能和centos7.X有些许区别，以下是步骤：
* 安装erlang语言环境
**下载erlang**：`wget https://github.com/rabbitmq/erlang-rpm/releases/download/v21.1.1/erlang-21.1.1-1.el6.x86_64.rpm
`
`rpm -ivh erlang-21.1.1-1.el6.x86_64.rpm 
`
**下载rabbitmq**
`wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.7.9/rabbitmq-server-3.7.9-1.el6.noarch.rpm
`
`rpm -ivh rabbitmq-server-3.7.9-1.el6.noarch.rpm 
`
这时候可能会提醒socat is needed by rabbitmq-server-3.7.9-1.el6.noarch，这是因为rabbitMQ需要依赖socat,如果yum源中不存在socat，我们就要自己根据版本下载并安装了
` wget http://download-ib01.fedoraproject.org/pub/epel/6/x86_64/Packages/s/socat-1.7.2.3-1.el6.x86_64.rpm
`
**生成配置文件**
`cp /usr/share/doc/rabbitmq-server-3.7.9/rabbitmq.config.example /etc/rabbitmq/rabbitmq.config
`
可以根据需要自定义配置文件，如端口号等
**启动rabbitMQ**
`/etc/init.d/rabbitmq-server start`
**开启管理页面插件**
`rabbitmq-plugins enable rabbitmq_management     //管理页面默认端口是15672
`
`rabbitmq-plugins enable rabbitmq_management
`
**添加管理员账号 **
`rabbitmqctl add_user admin test
`
**分配用户标签 **
`rabbitmqctl set_user_tags admin administrator
`
**创建和赋角色完成后查看并确认**
`
rabbitmqctl list_users 
`
**删除guest帐号**
`
rabbitmqctl delete_user guest
`
到此安装完成， 登录rabbitmq管理界面
浏览器输入地址：http://23.106.149.172:15672/#/
注意后两步骤其实在管理员界面操作也可以
完成以上步骤后，可能远程访问存在权限问题，在管理员界面创建新用户进行权限的分配就可以了，或者可以通过命令行记性解决`rabbitmqctl set_permissions -p /${user_name} user_admin ‘.*‘ ‘.*‘ ‘.*‘`
目前我对应logstash的账号是cnki,密码是logstash

> 之所以使用rabbitMQ作为中间件（或者kafka,redis等），都是起到缓冲作用的队列，也就是削峰填谷，如果直接写入到ES的话，由于ES的HTTP API处理能力有限，在日志写入频繁的情况下可能会超时，丢失相关的数据，所以用队列来缓冲同时起到保护数据的作用  [MQ的学习资料](https://www.jianshu.com/p/a6460b4b155f)


## 2.ELK的安装
这部分比较简单，直接在官方网站下载最新版本（6.6），然后冉庄就好了，Linux同理。安装完成后配置文件然后进入相应的bin目录进行启动就可以了。不再赘述
需要注意的问题：
**ElasticSearch开放外网**
> 安装完成后，ES可以通过localhost，访问但是通过IP地址是不行的，需要设置开放外网
> 配置流程
> 1. 进入elasticsearch主目录下
> vim config/elasticsearch.yml
> 2. 添加下面内容
> network.host: 0.0.0.0
> http.port: 9200
> 注：前面没有#注释，还有就是顶头加一个空格，冒号后也要加空格。 ==> 空格 network.host:空格 0.0.0.0

**logstash配置**
Logstash:
新建配置文件log_to_es.conf`
```xml
input {
  rabbitmq {
      type =>"all"
      durable => true
      exchange => "ex_logstash"
      exchange_type => "direct"
      key => "logstash"
      host => "localhost:5672"
      user => "root"
      password => "admin"
      queue => "faceJob-logstash"
      auto_delete => false
  }
}
output{
    elasticsearch {
    hosts => ["localhost:9200"]
    }
}
```
或者如果不适用rabbimq的话
```xml
input {
    tcp {
    ##host:port就是上面appender中的 destination，这里其实把logstash作为服务，开启8080端口接收logback发出的消息
        host => "172.28.207.2"
        port => 4560  
    #模式选择为server
        mode => "server"
        tags => ["tags"]

    ##格式json
        codec => json_lines
    }

}

output {
        elasticsearch {
        #ES地址
                hosts => "172.28.207.2"
        #指定索引名字
                index => "applog"
        }
        stdout { codec => rubydebug}
}
```
**启动logstash**
`./logstash -f ../config/log_to_es.conf`
**启动kibana**
`http://localhost:5601/app/kibana#/home?_g=()`

## 3.整合Rabbit与ELK
在项目中添加logback.xml的文件，并添加如下内容：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                {"time": "%date{ISO8601}", "thread": "%thread", "level": "%level", "class": "%logger{36}", "message": "%message"}\n
            </pattern>
        </encoder>
    </appender>

    <appender name="AMQP"
              class="org.springframework.amqp.rabbit.logback.AmqpAppender">
        <layout>
            <pattern>
                {
                "time": "%date{ISO8601}",
                "thread": "%thread",
                "level": "%level",
                "class": "%logger{36}",
                "message": "%message"
                }
            </pattern>
        </layout>

        <!-- RabbitMQ connection -->
        <host>23.106.149.172</host>
        <port>5672</port>
        <username>test</username>
        <password>test</password>

        <applicationId>zeus-framework</applicationId>
        <routingKeyPattern>zeus-framework</routingKeyPattern>
        <declareExchange>true</declareExchange>
        <exchangeType>direct</exchangeType>
        <exchangeName>ex_logstash</exchangeName>

        <generateId>true</generateId>
        <charset>UTF-8</charset>
        <durable>true</durable>
        <deliveryMode>PERSISTENT</deliveryMode>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="AMQP" />
    </root>

</configuration>

```
在pom文件中添加如下依赖：
```xml
<dependency>     
    <groupId>org.springframework.boot</groupId>     
    <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
<dependency>    
    <groupId>org.springframework.boot</groupId>    
    <artifactId>spring-boot-starter-data-rest</artifactId>
</dependency>

```
