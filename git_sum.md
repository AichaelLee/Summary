* 克隆服务器上的项目：
 `$ git clone <url>`
* 创建自己的分支，并在自己的分支进行开发
`$ git brach -b <yourbranch>`
` $ git checkout <yourbranch> `
* 向服务器提交代码：
`$ git status` 查看当前文件的修改状态
`$ git add <filename>` 添加某文件到暂存区 或 git add . 添加所有修改文件到暂存区
`$ git commit -m "此次提交的目的，便于日后查阅和他人理解"`  添加所有修改文件到本地仓库
`$ git push origin yourbranch:yourbranch `(把本地仓库代码提交到远程分支)

* 如果你想查看本地所有分支：
`$ git branch` 
* 如果你想切换分支
`$ git checkout <name>`
* 如果你想删除分支：
`$ git branch -d <name>`
* 如果你想更新你的本地仓库至最新改动，执行：
`$ git pull`
* 如果你想了解本地仓库的历史记录，执行：
`$ git log`
* 如果你想每一条提交记录只占一行的输出:
`git log --pretty=oneline`
* 如果你操作失误，你可以执行如下命令替换掉本地改动：
`git checkout -- <filename>`
* 如果你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：
`git fetch origin`
`git reset --hard origin/master`

