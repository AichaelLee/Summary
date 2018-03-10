## One-Off Dyno

在学习heroku的过程中，官方文档指明，在30s内系统接受不到请求，就会造成连接超时，所以在一些需要很长时间响应的业务，就要把它异步化，官方推荐的方式为使用MQ中间件。在add-on中有CLOUD_AMQP这一附加组件，添加成功后，就可以使用云端的rabbit-mq了。需要长时间处理的业务逻辑通过消息队列发送到worker dyno进行处理。

在Procfile中声明Dyno类型，并且通过执行heroku ps:scale命令依靠dyno管理器进行管理。这些dynos负责应用的通用流程（比如处理Http请求或者处理后台工作等）
当你把工程推送到heroku的时候，slug 编译器就会编译包含你的应用的一个slug，这个应用可能包含许多部分，比如web 服务器，控制应用，初始化数据库的脚本等。然而web dyno会在Procfile中定义并由平台进行管理，控制台和脚本只会在被需要的时候执行，这些就是One-Off dyno。
One-off dynos和其他类型的dyno一起运行，正像web worker或者其他formation dynos一样，他们会得到dyno动态隔离的所有好处。
每一个dyno都有自己短暂的不与其他dyno共享的文件管理系统，当你断开的时候就会失效，这个文件系统使用slug归档文件，因此one-off dynos可以充分使用应用上的所有
文件
在one-off dynos (run with heroku run) 和 formation dynos (run with heroku ps:scale)主要有四种区别：
1.one-off dyno通过终端连接，为STDIN和STDOUT提供逐个字符的TCP连接，这使得你可以使用控制台等交互式的进程，由于STDOUT将进入你的终端，因此日志中仅仅会记录下来
dyno的启动和终止
2.One-off dynos在你摁下Ctrl+C 或者 断开本地终端就会被终止，One-off dynos绝不会自动重启，无论它是自动终止还是你手动关闭。
3.One-off dynos以run.N方法命名而不是以<process-type>.N的形式命名。
4.One-off dynos不会接收到HTTP请求，因为路由器只有把流量导入到命名为web.N的dyno上面。
除了这些差异外，one-off dynos和其他formation dynos在管理上面没有区别。


当你使用heroku run的时候就会创建One-off dynos

One-off dyno execution syntax
heroku run takes two types of parameters. You can either supply the command to execute, or a process type that is present in your application’s Procfile.
If you supply a command, either a script or other executable available to your application, then it will be executed as a one-off dyno, together with any additional arguments. For example, to execute the Python interpreter with a file dowork.py supplied as an argument, then execute heroku run python dowork.py.
If you instead supply a process type, as declared in a Procfile, then its definition will be substituted, and executed together with any additional arguments. For example, given the following Procfile:
myworker:  python dowork.py
Executing heroku run myworker 42 will run python dowork.py 42 as a one-off dyno.
