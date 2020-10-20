---
title: git版本控制
date: 2020-10-20 10:59:53
tags:
---
毛世玉小哥的git命令用的很6，于是我在此记下他在我电脑上操作的命令。
将多次提交合并成一次
```
git status
git log
git reset --soft HEAD~5回退版本，不清空暂存区
git commit -m "多次提交合并成一次"
git push -f（然后远程上的5次提交都会被覆盖）
```
哇塞，又看见世玉6的不行的git操作啦。
```
git pull出错
git reset --hard    git clean -dff
```
于是我赶紧趁着工作闲暇时跟着廖雪峰学学git，以下是我学git记录的知识点。
## git是什么
git是Linus团队用C语言编写的分布式管理控制系统，工作过程不用联网，具有强大的分支管理功能
## git操作
在平台上安装git后需进行身份配置，配置信息在C:\Users\Administrator\.gitconfig中
git config --global user.name "your name"
git config --global user.email "email@example.com"
--global表示在这台电脑的所有git仓库都能用
### 配置别名
git config --global alias.lg "log --graph --pretty=one --abbrev-commit"
git config --global alias.mg "merge --no-ff -m"
git config --global alias.st status
创建空目录 mkdir learngit  cd learngit pwd(显示当前目录)
git init初始化项目仓库，会多了.git目录（用来跟踪管理版本库）ls -ah可查看隐藏目录
git add .添加文件到暂存区
git commit -m <message>提交到版本库
git status查看工作区状态
git diff查看修改内容
![](/images/git1.png)
### 版本回退
git log查看提交日志
git log --pretty=oneline --graph --abbrev-commit单行显示、查看分支合并情况、提交码缩略
git log -1显示最后一条commit信息
git reflog查看命令历史，可用来回溯到未来版本
HEAD表示当前版本	HEAD^表示上一个版本 HEAD^^表示上上一个版本 HEAD~5往上5个版本
git reset --hard commitId前n(>=5)位   (修改HEAD指针)
git checkout -- file丢弃工作区修改（版本库替换工作区）
git reset HEAD <file>暂存区重新放回工作区
### 删除文件
要删除的文件已经在版本库中存在
git rm/add <file>
git commit -m "file从版本库中删除"
或者恢复误删文件git checkout --file
### 远程仓库
github支持SSH协议，只要知道了你的公钥，就能确保只有你自己才能推送
在自己的电脑上创建SSH Key   ssh-keygen -t rsa -C "your email"
将用户主目录C:\Users\Administrator\.ssh下生成的id_rsa.pub公钥添加到github上去
**先有本地库，后有远程库**
github上新建仓库，然后将远程仓库和本地关联
git remote add origin git@github.com:zhufenfen/repo-name.git关联远程仓库
git remote rm origin解除关联
git push -u origin master或者git push --set-upstream origin master本地仓库内容推送到远程仓库
git push origin branchname将新分支推送到github
git push origin :branchname删除远程分支
git remote -v查看远程库信息
**先有远程库，后有本地库**
git clone git@github.com:zhufenfen/repo-name.git
### 分支管理
master分支非常稳定，仅用来发布版本，平时都在dev上干活，每个人都有自己的分支，往dev上合并就可以了。
git branch <name>创建分支
git checkout <name>切换分支
git checkout -b <name>创建并切换分支
git checkout -b <name> origin/<name>在本地创建和远程分支对应的分支
git branch查看分支  git branch -a查看本地分支和远程分支  git branch -r查看远程分支
git merge <name>合并分支
git branch -d <name>删除分支，D表示强制删除分支
合并分支的时候，有冲突先解决冲突，然后git commit -am "conflict"
fast forward模式
![](/images/git2.png)
不采用fast forward模式时，git merge --no-ff -m "merge with no-ff" dev
![](/images/git3.png)
add merge为dev分支提交信息，merge with no-ff为主分支提交信息
with fast-forward为dev2分支提交信息
![](/images/git4.png)
可以看出当删除dev和dev2分支后，合并时采用fast-forward的分支信息丢失
![](/images/git5.png)
**bug分支**
修复bug时会创建新的bug分支进行修复，修复完成后合并删除
git stash工作区内容储存一下，可多次stash，然后恢复指定stash git stash apply stash@{0}
git stash list查看储存内容
git stash apply	git stash drop
git stash pop恢复工作区内容+删除stash内容
**feature分支**
开发一个新功能，最好新建一个分支
多人协作
git pull，如果提示no tracking information说明本地分支和远程分支没有建立关联
用命令git branch --set-upstream-to=origin/<branch> dev建立关联
如果有冲突先解决冲突，最后git push origin <branch-name>
git rebase可以将本地未push的分叉提交历史整理成直线
### 标签管理
标签是commitid的别名
git tag <tagname> <commitid(默认HEAD)>新建一个标签
git tag -a <tagname> -m <message>指定标签信息
git tag查看所有标签
git show <tagname>
git tag -d <tagname>删除本地标签
git push origin :refs/tags/<tagname>删除远程标签
git push origin <tagname>推送某个标签
git push origin --tags推送所有标签
## github
可以任意Fork开源项目，自己拥有Fork后的仓库的读写权限，可以在自己的仓库pull request给官方仓库来贡献代码
忽略某些文件时，需要将.gitignore添加到版本库
git add -f <file>强制添加
git check-ignore -v <file>检查规则
## 搭建git服务器
需要一个Linux平台
gitosis更方便管理公钥
gitolite用于权限控制

嗯...实践出真知