## centos下安装jdk:
在服务器上安装jdk，一直出现wget下来后是一个空的文件。当时因为在AWS上的C9服务器上搭建环境，当时不能通过scp向服务器传输文件，中间甚至尝试先推到github然后在clone下来，到最后用的方式是把jdk上传到了S3，然后wget下来。<br>
事后仔细查找了相关资料，使用wget或curl命令下载jdk的时候，因为认证还有接受许可协议的原因才导致下载失败，所以头部注明就可以了。通过实验也成功安装。
```sh
sudo wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/jdk-8u151-linux-x64.rpm";
```

配置maven环境变量：同样，在配置环境变量的时候因为profile是只读的，所以一直配不上环境变量，到最后按照这个网址的教程，写好了.sh 创建一个文件夹是mkdir，创建一个文件时touch 
https://www.iteblog.com/archives/775.html
文章标识符：20171212centosjdk