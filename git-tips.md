### 待解决问题：限制其他人向主分支提交代码

# 高频词汇的介绍
git操作博大精深，细节很多，对于日常开发的我们而言，熟识几条常用的操作便可解决日常问题。对于初学者而言了解git基本词汇的含义和git的工作原理还是很有必要的，这样对解决git操作中的“bug”也非常有帮助。

* remote：指存放在远程的一个计算机，在一般指GitHub服务器。
* clone：指克隆，将github上的项目克隆（也可以说是复制）到自己的电脑上。
* origin：指远程的代码仓库（自己的仓库），即源文件。
* upstream：指上游，fork别人项目的地址，别人的项目就是你的上游，即上一层源文件。
* master：指主分支。
* pull：指拉代码，把你Github上的远程仓库拉到本地计算机上。
* push：指提交代码，把你本机上的项目提交到Github上的远程仓库。
### 特别注意：本地的 .git文件是指本地仓库，.git目录下存放着所有文件的版本和关联信息，该目录在默认条件下是隐藏的。

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

### 常见的git操作的流程：
### 一：克隆项目（可类比SVN上的checkout，从服务器获取工程代码）
* 1.在本地的workspace下右击Git Bash Here打开：
* 2.git clone + 仓库地址，把仓库里的文件克隆到本地：
这时本地出现了一个HerokuFW的文件夹
* 3.cd 进入文件夹即可进行相关的git操作
### 二：创建属于自己的分支，在自己分支进行开发，防止错误的代码可能污染主分支（非常重要，严禁向主分支提交代码！）
### 三：git提交的通用过程（写完代码后想要向服务器端工程提交时要进行的操作）
* 1.git status （查看当前文件的修改状态）；
* 2.git add 某文件（添加某个修改文件）/ git add .(添加文件所有修改文件)；
* 3.git commit -m "注释"（提交文件并备注本次提交的目的,便于日后查阅和他人理解）；
* 4.git push -u origin yourbranchename（把暂存区的文件发送到git服务器上）
代码提交完成！




#### 前提：只有进入到相应的文件目录后才能对git进行相关的操作




FAQ:
* git push origin master中origin是什么意思？
> git为你默认创建了一个指向远端代码库的origin，在控制台内输入`git remote -v` 可以看到origin指向的位置就是远程代码库，总结来说origin就是一个名字，当你clone 托管在git服务器上的代码仓库时，git为你默认创建的指向这个远程代码库的标签

* 那origin 和 master 有什么区别呢？
> origin 指向的是 repository ，master只是repository中默认创建的第一个分支，我们看到git push 的时候因为origin和master都是默认创建的，所以可以这样省略，但是这不是一个最佳实践，而且当你换一个branch再git push的时候，也会出现问题。
# git操作流程
你的本地仓库由 git 维护的三棵“树”组成。第一个是你的`工作目录`，它持有实际文件；第二个是`暂存区（Index）`，它像个缓存区域，临时保存你的改动；最后是 `HEAD`，它指向你最后一次提交的结果。
** 添加和提交 **
你可以提出更改（把它们添加到暂存区），使用如下命令：
* `git add <filename>`
* `git add *`
这是 git 基本工作流程的第一步；使用如下命令以实际提交改动：
* `git commit -m "代码提交信息"`
现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库
  
  
 ** 推送改动 **
你的改动现在已经在本地仓库的 HEAD 中了。执行如下命令以将这些改动提交到远端仓库：
* `git push origin master`
可以把 master 换成你想要推送的任何分支。

**分支**
分支是用来将特性开发绝缘开来的。在你创建仓库的时候，master 是“默认的”分支。在其他分支上进行开发，完成后再将它们合并到主分支上

创建一个叫做“feature_x”的分支，并切换过去：
git checkout -b feature_x
切换回主分支：
git checkout master
再把新建的分支删掉：
git branch -d feature_x
除非你将分支推送到远端仓库，不然该分支就是 不为他人所见的：
git push origin <branch>
  
  
  更新与合并
要更新你的本地仓库至最新改动，执行：
git pull
以在你的工作目录中 获取（fetch） 并 合并（merge） 远端的改动。
要合并其他分支到你的当前分支（例如 master），执行：
git merge <branch>
在这两种情况下，git 都会尝试去自动合并改动。遗憾的是，这可能并非每次都成功，并可能出现冲突（conflicts）。 这时候就需要你修改这些文件来手动合并这些冲突（conflicts）。改完之后，你需要执行如下命令以将它们标记为合并成功：
git add <filename>
在合并改动之前，你可以使用如下命令预览差异：
git diff <source_branch> <target_branch>
  
  
  标签
为软件发布创建标签是推荐的。这个概念早已存在，在 SVN 中也有。你可以执行如下命令创建一个叫做 1.0.0 的标签：
git tag 1.0.0 1b2e1d63ff
1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。可以使用下列命令获取提交 ID：
git log
你也可以使用少一点的提交 ID 前几位，只要它的指向具有唯一性。


如果你想了解本地仓库的历史记录，最简单的命令就是使用: 
git log
你可以添加一些参数来修改他的输出，从而得到自己想要的结果。 只看某一个人的提交记录:
git log --author=bob
一个压缩后的每一条提交记录只占一行的输出:
git log --pretty=oneline
或者你想通过 ASCII 艺术的树形结构来展示所有的分支, 每个分支都标示了他的名字和标签: 
git log --graph --oneline --decorate --all
看看哪些文件改变了: 
git log --name-status
这些只是你可以使用的参数中很小的一部分。更多的信息，参考：
git log --help

替换本地改动
假如你操作失误（当然，这最好永远不要发生），你可以使用如下命令替换掉本地改动：
git checkout -- <filename>
此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到暂存区的改动以及新文件都不会受到影响。

假如你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：
git fetch origin
git reset --hard origin/master


实用小贴士
内建的图形化 git：
gitk
彩色的 git 输出：
git config color.ui true
显示历史记录时，每个提交的信息只显示一行：
git config format.pretty oneline
交互式添加文件到暂存区：
git add -i



