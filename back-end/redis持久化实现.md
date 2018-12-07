## Redis支持RDB和AOF两种持久化机制。
持久化功能有效地避免因进程退出造成的数据丢失问题，下次重启时利用之前持久化的文件即可实现数据恢复。



## RDB：RDB持久化是把当前进程数据生成快照保存到硬盘的过程，触发RDB持久化过程分为手动触发和自动触发。
### 手动触发分别对应save和bgsave命令

   save命令：阻塞当前Redis服务器，知道RDB过程完成为止，对于内存比较大的实例会造成长时间阻塞，线上不建议使用。

    DB saved on disk

    bgseve命令：Redis进程执行fork操作创建子进程，RDB持久化过程有子进程负责，完成后自动结束。阻塞只发生在fork阶段，一般时间很短。

        * Background saving started by pid 3151

        * DB saved on disk

        * RDB: 0 MB of memory used by copy-on-write

        * Background saving terminated with success
### 自动触发

        以下场景下会触发

       1）使用save相关配置，如“save m n”。表示m秒内数据集存在n次修改时，自动触发bgsave。

       2）如果从节点执行全量复制操作，主节点自动执行bgsave生成RDB文件并发送给从节点，更多细节见6.3节介绍的复制原理。

       3）执行debug reload命令重新加载Redis时，也会自动触发save操作。

       4）默认情况下执行shutdown命令时，如果没有开启AOF持久化功能则自动执行bgsave。


## 使用AOF

开启AOF功能需要设置配置：appendonly yes，默认不开启。文件名通过appendfilename配置设置，默认appendonly.aof。

**命令写入**

   AOF命令写入的内容直接是文本协议格式

      文本有良好的兼容性

      开启AOF后，所有写入命令都包含追加操作，直接采用协议格式，避免二次处理开销

      文本协议具有可读性，方便直接修改处理

  AOF为什么把命令追加到aof_buf中？

      Redis使用单线程相应命令，如果每次写入AOF文件命令直接追加到硬盘，那么性能完全取决于当前硬盘负载。先写入缓存aof_buf中，可以提供多种缓冲区同步硬盘的策略

**重写机制**

随着命令不断写入AOF，文件会越来越大。Redis引入AOF重写机制压缩文件体积。

    重写后AOF文件为什么变小

     1、进程内已经超时数据不再写入文件

     2、旧的AOF文件含有无效命令

     3、多条命令合并
