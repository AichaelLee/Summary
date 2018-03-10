## 准备：

在本机安装并配置JAVA8，和Maven

## 注册

[官方网站](https://signup.heroku.com/login) （目前QQ邮箱和163等邮箱不能注册，需要使用sina、gmail、foxmail等进行注册）

## 安装CLI

[点击](https://devcenter.heroku.com/articles/getting-started-with-java#set-up)下载系统对应的安装包，`Heroku CLI`是用于创建、管理`Heroku`上应用的命令行工具

安装成功后，bin目录将被添加到`PATH`环境变量，

下载并安装完成后，在 cmd 中输入`heroku login`，用创建`heroku`账号的`email`和`密码`登陆:

```
heroku login
Enter your Heroku credentials.
Email: java@example.com
Password:
```

主要如果本机开启防火墙可能导致连接失败，此时配置一下代理：

## 创建一个应用程序：

在这个步骤中，你需要准备一个可以部署到heroku的简单应用程序，现在我们在GitHub上克隆官方推荐的实例。在命令行窗口执行：

```
git clone https://github.com/heroku/java-getting-started.git 
cd java-getting-started
```

你现在有了一个包含pom.xml的本地仓库了，这是java的依赖管理工具maven用来管理项目的配置文件。

## 部署应用：

在在这一步中我们需要把刚才的应用部署到Hero ku上面，首先我们在 Heroku 创建一个 app, 以便 Heroku 准备好接收你的代码。

```
heroku create
```

当你使用这一命令在heroku上创建了一个app时，一个远程git仓库就会被同步创建并且关联到你的本地git仓库上。（当你需要把一个程序绑定到远端仓库上时，可以使用heroku git:remote -a \[你的应用名字\]进行绑定），默认 Heroku 会为你的 app 生成一个随机的名字, 或者 你可以通过可选参数指定你的应用名。如：`heroku create [NAME]。`注意名称必须以字母开始,只能包含小写字母,数字和连字符, 而且名称在 heroku 的所有程序中必须是唯一的。

现在你可以往Heroku上部署代码了：

`git push heroku master ✳️`必须将本地的 master push 到 heroku 的 master

应用现在已经部署了，确保至少一个 app 的实例正在运行:

`$ heroku ps:scale web=1`

现在，可以用创建的包含 app 名称的 URL 访问 app. 有一个便捷的方法, 你可以像这样打开web站点:

`$ heroku open`

## 查看日志

查看你正在运行的应用的日志流信息：

`heroku logs --tail`

日志进行过程中，CRTL + C 终止日志流

[点击此处](https://github.com/eskimo220/utadahikaru/wiki/【Log】Heroku平台上实现日志的输出20170901)在开发过程中查看程序日志和Heroku平台日志的方式

## 定义一个Procfile:

Heroku 需要知道使用哪个命令启动程序。命令在一个名为Procfile\(没有后缀\)的特殊文件中指定。这个文件必须放在程序的顶级文件夹中。里面的内容看起来像这样的：

> web: java -jar target/java-getting-started-1.0.jar

Procfile文件内容的格式很简单:在每一行中指定一个任务名,后跟一个冒号,然后是执行这个任务的命令。名为web的任务比较特殊,Heroku 使用这个任务启动 Web 服务器。

Heroku 会为这个任务提供一个PORT环境变量,用于设定程序监听请求的端口。如果设定了PORT变量, Gunicorn默认就会使用其中保存的值,因此无需将其包含在启动命令中。

部署程序后,Heroku 会运行 Procfile 中列出的所有任务。

[任务格式模板](https://devcenter.heroku.com/articles/procfile#process-types-as-templates)

程序可在 Procfile 中使用 web 之外的名字声明其他任务,例如程序所需的其他服务。

[声明任务类型的方式](https://devcenter.heroku.com/articles/procfile#declaring-process-types)

## 扩展应用

你可以使用ps命令来查看当前运行的Dyno数量：

`heroku ps`

默认情况下，你的应用会被部署到一个免费的dyno上面，如果半小时之内没有请求就会进入到休眠状态。这会使进入休眠状态后的第一次请求可能会有几秒的延迟，但是后续的请求会被正常响应。免费的Dyno每月会有固定的限额，在免费时间没有被使用完的情况下，你的应用都会一直运行在上面。

在Heroku平台上进行应用的扩展至需要改变运行Dyno的数量，例如把web dyno的数量变为零：

```
heroku ps:scale web=0
```

这时候你再次使用heroku open命令打开你部署在上面的应用时，会发现报错信息，因为没有任何web dyno去处理你所发出的请求了。我们再此扩展回来：

```
heroku ps:scale web=1
```

## 声明应用依赖：

Heroku通过存在根目录下的pom.xml来识别java应用程序，所以对于自己需要部署上去的应用，需要使用`mvn archetype:create`

命令来创建一个pom.xml。我们部署在heroku上面的这个示例程序已经包含了一个pom.xml，以下是它的形式：

> `<dependencies>`
>
>     `<dependency>`
>
>          `<groupId>org.springframework.boot</groupId>`
>
>          `<artifactId>spring-boot-starter-actuator</artifactId>`
>
> `</dependency>`
>
> `<dependency>`
>
>     `<groupId>org.springframework.boot</groupId>`
>
>           `<artifactId>spring-boot-starter-web</artifactId>`
>
>     `</dependency>`
>
>      ` ...`
>
> `</dependencies>`

pom.xml指定了你需要安装在应用中的依赖，当应用部署完成后，heroku读取这个文件，使用`mvn clean install`这个命令来安装此应用所需要的依赖。

另一个文件 system.properties决定要使用的java版本（heroku支持多种java版本）这个文件中的内容是可选的，如：

```
java.runtime.version=1.8
```

进入到你本地目录下，执行 `mvn clean install安装依赖，`以使这个应用在本地运行，注意这个应用需要JDK1.8版本，当然你部署自己的应用程序时可以使用任意的版本。

Maven 会根据依赖进行编译然后生成一个jar包，并放在target目录下，这个过程是在pom.xml中`spring-boot-maven-plugin`完成的

