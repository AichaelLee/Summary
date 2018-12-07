## redis集群的搭建
关于mac下搭建redis集群，根据官方文档，有两种做法，其中有一种需要用到gem，因为电脑上空间也不太多了，再加上配置了很多环境，ruby也不经常使用，
所以用creat-cluste 工具创建了集群
### 大致步骤
1.创建集群：
`create-cluster create`
2.第一次创建集群后不需要启动了，在集群关闭再次需要启动时：
`create-cluster start`
3.关闭集群：
create-cluster stop.
4.创建集群成功后就可以开心地连接了：
`redis-cli -c -p 30001`
