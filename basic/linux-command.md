## ls命令
这是一个有关查看文件与目录的命令，是list的缩写，它的参数非常多，常见的如下：

> -l ：列出长数据串，包含文件的属性与权限数据等  
-a ：列出全部的文件，连同隐藏文件（开头为.的文件）一起列出来（常用）  
-d ：仅列出目录本身，而不是列出目录的文件数据  
-h ：将文件容量以较易读的方式（GB，kB等）列出来  
-R ：连同子目录的内容一起列出（递归列出），等于该目录下的所有文件都会显示出来  

## chkconfig 
chkconfig是管理系统服务(service)的命令行工具。所谓系统服务(service)，就是随系统启动而启动，随系统关闭而关闭的程序。

chkconfig可以更新(启动或停止)和查询系统服务(service)运行级信息。更简单一点，chkconfig是一个用于维护/etc/rc[0-6].d目录的命令行工具。

chkconfig 提供5个功能：

1. 设置service启动信息

```chkconfig name on/off/reset``` 
1
on、off、reset用于改变service的启动信息。 
on表示开启，off表示关闭，reset表示重置。 
默认情况下，on和off开关只对运行级2，3，4，5有效，reset可以对所有运行级有效。 
例如，

# chkconfig httpd on
1
2. 设置service运行级别

# chkconfig --level levels
1
例如，

# chkconfig --level 2345 httpd on
1
指定运行级为2,3,4,5 
等级0表示：表示关机 
等级1表示：单用户模式 
等级2表示：无网络连接的多用户命令行模式 
等级3表示：有网络连接的多用户命令行模式 
等级4表示：不可用 
等级5表示：带图形界面的多用户模式 
等级6表示：重新启动

3. 添加service

# chkconfig --add name
1
添加一个chkconfig管理的service，并在/etc/rc[0-6].d 目录下添加相应的符号链接(symbolic links)。

4. 移除service

# chkconfig --del name
1
从chkconfig 管理名单中删除该service，并且删除 /etc/rc[0-6].d 目录下所有与之关联的符号链接(symbolic links)。

5. 列出service的启动信息

# chkconfig --list [name]
1
如果不指定name，会列出所有services的信息。

每个service每个运行级别都会有一个启动和停止脚本；当切换运行级别时，init不会重启已经启动的service，也不会重新停止已经停止的service。

下面举例说明

(1).列出所有服务的启动情况

$ chkconfig --list
auditd          0:off   1:off   2:on    3:on    4:on    5:on    6:off
redis           0:off   1:off   2:off   3:off   4:off   5:off   6:off
restorecond     0:off   1:off   2:off   3:off   4:off   5:off   6:off
rpcbind         0:off   1:off   2:on    3:on    4:on    5:on    6:off
rpcgssd         0:off   1:off   2:off   3:on    4:on    5:on    6:off
rpcsvcgssd      0:off   1:off   2:off   3:off   4:off   5:off   6:off
rsyslog         0:off   1:off   2:on    3:on    4:on    5:on    6:off
saslauthd       0:off   1:off   2:off   3:off   4:off   5:off   6:off
smb             0:off   1:off   2:off   3:on    4:off   5:on    6:off
。。。
xinetd based services:
    rsync:          off
    swat:           off
1
2
3
4
5
6
7
8
9
10
11
12
13
14
(2)增加mysqld服务

$ chkconfig --add mysqld
1
(3)删除mysqld服务

$ chkconfig --del mysqld
1
(4)设置mysqld运行级别为2,3,4,5

$ chkconfig --level 2345 httpd on
1
(5)列出mysqld 服务启动信息情况

$ chkconfig --list mysqld
mysqld          0:off   1:off   2:on    3:on    4:on    5:on    6:off
1
2
(6)设置启动信息

$ chkconfig mysqld on
1
默认的运行级别为2,3,4,5 
实际上，与4中命令作用是一样的。

## grep命令
该命令常用于分析一行的信息，若当中有我们所需要的信息，就将该行显示出来，该命令通常与管道命令一起使用，用于对一些命令的输出进行筛选加工等，它的简单语法为:
> grep [-acinv] [--color=auto] '查找字符串' filename  

## find命令
find是一个基于查找的功能非常强大的命令，相对而言，它的使用也相对较为复杂，参数也比较多,它的基本语法：
> find [PATH] [option] [action]  
* 与时间有关的参数：  
-mtime n : n为数字，意思为在n天之前的“一天内”被更改过的文件；  
-mtime +n : 列出在n天之前（不含n天本身）被更改过的文件名；  
-mtime -n : 列出在n天之内（含n天本身）被更改过的文件名；  
-newer file : 列出比file还要新的文件名  
例如：  
find /root -mtime 0 # 在当前目录下查找今天之内有改动的文件   
 与用户或用户组名有关的参数：  
-user name : 列出文件所有者为name的文件  
-group name : 列出文件所属用户组为name的文件  
-uid n : 列出文件所有者为用户ID为n的文件  
-gid n : 列出文件所属用户组为用户组ID为n的文件  
 例如：  
find /home/ljianhui -user ljianhui # 在目录/home/ljianhui中找出所有者为ljianhui的文件  
与文件权限及名称有关的参数：  
-name filename ：找出文件名为filename的文件  
-size [+-]SIZE ：找出比SIZE还要大（+）或小（-）的文件  
-tpye TYPE ：查找文件的类型为TYPE的文件，TYPE的值主要有：一般文件（f)、设备文件（b、c）、  
             目录（d）、连接文件（l）、socket（s）、FIFO管道文件（p）；  
-perm mode ：查找文件权限刚好等于mode的文件，mode用数字表示，如0755；  
-perm -mode ：查找文件权限必须要全部包括mode权限的文件，mode用数字表示  
-perm +mode ：查找文件权限包含任一mode的权限的文件，mode用数字表示  
例如：  
find / -name passwd # 查找文件名为passwd的文件  
find . -perm 0755 # 查找当前目录中文件权限的0755的文件  
find . -size +12k # 查找当前目录中大于12KB的文件，注意c表示byte  

## cp命令
> 该命令用于复制文件，copy之意，它还可以把多个文件一次性地复制到一个目录下，它的常用参数如下：
-a ：将文件的特性一起复制  
-p ：连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份  
-i ：若目标文件已经存在时，在覆盖时会先询问操作的进行  
-r ：递归持续复制，用于目录的复制行为  
-u ：目标文件与源文件有差异时才会复制 

## mv命令
> 该命令用于移动文件、目录或更名，move之意，它的常用参数如下：
-f ：force强制的意思，如果目标文件已经存在，不会询问而直接覆盖  
-i ：若目标文件已经存在，就会询问是否覆盖  
-u ：若目标文件已经存在，且比目标文件新，才会更新  

## ps命令
> 该命令用于将某个时间点的进程运行情况选取下来并输出，process的缩写，它的常用参数如下：
-A ：所有的进程均显示出来  
-a ：不与terminal有关的所有进程  
-u ：有效用户的相关进程  
-x ：一般与a参数一起使用，可列出较完整的信息  
-l ：较长，较详细地将PID的信息列出  
ps一般常用的搭配：
ps aux # 查看系统所有的进程数据  
ps ax # 查看不与terminal有关的所有进程  
ps -lA # 查看系统所有的进程数据  
ps axjf # 查看连同一部分进程树状态  
