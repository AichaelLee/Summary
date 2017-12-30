# 高频词汇的介绍
git操作博大精深，细节很多，对于日常开发的我们而言，熟识几条常用的操作便可解决日常问题。对于初学者而言了解git基本词汇的含义和git的工作原理还是很有必要的，这样对解决git操作中的“bug”也非常有帮助。

* remote：指存放在远程的一个计算机，在一般指GitHub服务器。
* clone：指克隆，将github上的项目克隆（也可以说是复制）到自己的电脑上。
* origin：指远程的代码仓库（自己的仓库），即源文件。
* upstream：指上游，fork别人项目的地址，别人的项目就是你的上游，即上一层源文件。
* master：指主分支。
* pull：指拉代码，把你Github上的远程仓库拉到本地计算机上。
* push：指提交代码，把你本机上的项目提交到Github上的远程仓库。
特别注意：本地的 .git文件是指本地仓库，.git目录下存放着所有文件的版本和关联信息，该目录在默认条件下是隐藏的。

# git常用指令的用法
1.源文件：

* git remote -v ：指查看远程关联的仓库，包括源文件和上一层源文件。
* git remote add origin + 网址：指添加一个源文件。
* git remote add upstream + 网址：指添加一个上一层源文件，即fork项目的源文件。
* git remote remove origin/upstream + 网址：指删除一个源文件或上一层源文件。

2.初始化：

* git clone + 网址：指把网址中对应的项目克隆到自己本机上。
* git init：指初始化.git文件，里面没有链接信息，初始化后要重新建立与远程仓库的关联。
* git pull upstream master：指把获得原项目更新。
3.提交：

* git add + 文件名：指把修改的某个文件添加到暂存区（指.git仓库）；
* git add . :指把所有的修改文件添加到暂存区；
* git commit -m "认真写为什么要做这次提交"：便于日后查阅和他人理解；
* git push -u origin master:指把暂存区的文件提交到github中。
* git log：指查看提交的历史记录。
* dir：查看该文件的子目录。
4.分支：

* git status：查看所有情况；
* git branch：指查看分支信息；
* git branch + 分支名：指创建新的分支；
* git checkout + 分支名：指切换分支。

