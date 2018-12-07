## redis集群的搭建
关于mac下搭建redis集群，根据官方文档，有两种做法，但其实本质上是一样的，只不过使用脚本比较简单一些，接下来做一些记录
### 大致步骤
因为本质上还是使用了redis cluster的ruby脚本，所以要有ruby的依赖环境，在第一次进行集群创建的时候我就巧妙的忽略了这一点，导致创建集群出错
后来看了create-cluster的脚本之后，发现它里面本质上还是用了ruby脚本，所以创建之前先要gem install redis一下<br>
1.创建集群：

`create-cluster start`


`create-cluster create`


2.关闭集群：


`create-cluster stop.`


### 使用
创建集群成功后就可以开心地连接了：


`redis-cli -c -p 30001`
<br>
这时候可以设置一个key值进行测试了<br>
还有一些查看集群状态的命令，如：`cluster info`等




[官方文档地址](https://redis.io/topics/cluster-tutorial)
