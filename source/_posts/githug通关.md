---
title: githug通关
date: 2020-11-16 14:00:49
tags:
---

公司新来了一个和我同姓的棒棒后端咖～举办了两次关于 git 的技术分享，为了巩固提高还布置了作业，作业链接在此：[Githug](https://github.com/Gazler/githug)。我照着通关攻略不紧不慢的通了关并将此安利给了我只会 add commit push 的小男友）偷笑。唉，下午竟然还有考试呢，中午 HR 让我们临阵磨枪，我脑子里都是：临阵磨枪，再快也慌～ 那就温习下 githug ～～～～一个学习 git 的小游戏吧！

## Githug 安装和使用方法

先将 github 上的 githug 项目 clone 到本地，安装好 ruby 的运行环境，接着 gem install githug，输入 githug 创建新目录，cd git_hug 切换到 git_hug 目录下开始闯关
**闯关内部命令**

```
githug play：验证是否通过此次关卡，可省略play
githug hint：寻求提示，help、help、help、
githug reset：重置本次关卡，推倒重来～
githug levels：查看各自名称
```

## 通关历程

#### 第 1 关 init

![](/images/githug1.jpg)
命令：git init

#### 第 2 关 config

![](/images/githug2.jpg)
命令：git config --add (--local/--global) user.name/user.email

#### 第 3 关 add

![](/images/githug3.jpg)

```
git add 文件名
git add *.md：扩展名为md的文件都添加到暂存区
git add ./-A：工作区文件都添加到暂存区
```

#### 第 4 关 commit

![](/images/githug4.jpg)
命令：git commit -m "my commit message"

#### 第 5 关 clone

![](/images/githug5.jpg)
命令：git clone 远程仓库地址

#### 第 6 关 clone to folder

![](/images/githug6.jpg)
命令：git clone 远程仓库地址 自定义目录名

#### 第 7 关 ignore

![](/images/githug7.jpg)
命令：vim .gitignore、i、输入\*.swap、esc、:wq、

#### 第 8 关 include

![](/images/githug8.jpg)
命令：vim .gitignore、i、输入\*.a、回车、输入!lib.a、esc、:wq

#### 第 9 关 status

![](/images/githug9.jpg)
命令：git status (-s)

```
untracked：新建的工作区文件
modified：修改的工作区文件
staged：暂存区文件
```

#### 第 10 关 number_of_files_committed

![](/images/githug10.jpg)
命令：同上

#### 第 11 关 rm

![](/images/githug11.jpg)
命令：git rm 文件名：删除远程仓库文件

#### 第 12 关 rm_cached

![](/images/githug12.jpg)
命令：git rm --cached 暂存区文件：撤销暂存区文件到工作区

#### 第 13 关 stash

![](/images/githug13.jpg)

```
git stash
git stash list
git stash pop
```

#### 第 14 关 rename

![](/images/githug14.jpg)
命令：git mv 旧文件名 新文件名

#### 第 15 关 restructure

![](/images/githug15.jpg)
命令：mkdir src、git mv \*.html ./src/

#### 第 16 关 log

![](/images/githug16.jpg)
命令：git log --pretty=oneline

#### 第 17 关 tag

![](/images/githug.jpg)

```
git tag my-tag：给最近一次commit打tag
git tag my-tag hash：给指定commit打tag
git tag：查看所有tag
git tag -d my-tag：删除指定tag
```

#### 第 18 关 push_tags

![](/images/githug18.jpg)
命令：git push --tags

#### 第 19 关 commit_amend

![](/images/githug19.jpg)

```
先add
git commit --amend -m "new message"：替换原有的commit说明
git commit --amend -C HEAD：使用最近一次的commit 说明
git commit --amend：自行编辑提交说明
```

#### 第 20 关 commit_in_future

![](/images/githug20.jpg)
命令：git commit --date="具体日期/10 minutes ago/last friday/noon yesterday" -m "commit message"

#### 第 21 关 reset

![](/images/githug21.jpg)
命令：git reset 文件名

#### 第 22 关 reset soft

![](/images/githug22.jpg)
命令：git reset --soft HEAD^：撤销最后一次 commit 到暂存区

#### 第 23 关 checkout file

![](/images/githug23.jpg)
命令：git checkout 文件名：放弃工作区的某个文件

#### 第 24 关 remote

![](/images/githug24.jpg)
命令：git remote：查询连接了哪些远程仓库

#### 第 25 关 remote_url

![](/images/githug25.jpg)
命令：git remote -v：查询远程仓库地址（包含 fetch 和 push）

#### 第 26 关 pull

![](/images/githug26.jpg)
命令：git pull remote-name branch-name

#### 第 27 关 remote_add

![](/images/githug27.jpg)
命令：git remote add remote-name remote-url

#### 第 28 关 push

![](/images/githug28.jpg)

```
git push remote-name branch-name
git push -u remote-name branch-name（记住仓库名和分支名）
git push（记住之后下次直接可以输入此命令）
```

命令：git rebase（本地 commit 会排在远程 commit 之后）

#### 第 29 关 diff

![](/images/githug29.jpg)
命令：git diff [文件名]

#### 第 30 关 blame

![](/images/githug30.jpg)
命令：git blame 文件名：会列出每一行的 hash 值、作者、时间

#### 第 31 关 branch

![](/images/githug31.jpg)
命令：git branch branch-name

#### 第 32 关 checkout

![](/images/githug32.jpg)

```
git checkout branch-name：切换分支
git checkout -b branch-name：创建并切换分支
git chekcout -：切换到上个分支
```

#### 第 33 关 checkout_tag

![](/images/githug33.jpg)
命令：git checkout tag-name

#### 第 34 关 checkout_tag_over_branch

![](/images/githug34.jpg)
命令：git checkout tags/tag-name

#### 第 35 关 branch_at

![](/images/githug35.jpg)
命令：git branch branch-name hash-code

#### 第 36 关 delete_branch

![](/images/githug36.jpg)
命令：git branch -d branch-name

#### 第 37 关 push_branch

![](/images/githug37.jpg)
命令：git push remote-name branch-name

#### 第 38 关 merge

![](/images/githug38.jpg)
命令：git merge branch-name

#### 第 39 关 fetch

![](/images/githug39.jpg)

```
git fetch
git branch -r：查看远程分支
git log remote-name/branch-name：查看远程分支log
```

#### 第 40 关 rebase

![](/images/githug40.jpg)
命令：git checkout feature、git rebase master 当前分支的 commit 会在最上面

#### 第 41 关 rebase_onto

![](/images/githug41.jpg)
命令：git rebase --onto master wrong_branch

#### 第 42 关 repack

![](/images/githug42.jpg)
.git 用来存放 Git 管理仓库要用到的文件
命令：ls .git/objects/、git repack -d（打包并删除作废的对象）

#### 第 43 关 cherry-pick

![](/images/githug43.jpg)
命令：git cherry-pick hash-code：选取其中一条提交到主线

#### 第 44 关 grep

![](/images/githug44.jpg)
命令：git grep keyword [file-name]：在当前项目/指定文件中查找关键词

#### 第 45 关 rename_commit

![](/images/githug45.jpg)
命令：git rebase -i 前一条 hash-code、reword

```
pick：执行此次提交
reword：执行此次提交，但要修改备注内容
squash：合并此次提交
drop：删除此次提交
```

#### 第 46 关 squash

![](/images/githug46.jpg)
命令：git rebase -i 前一条 hash-code、squash

#### 第 47 关 merge_squash

![](/images/githug47.jpg)
命令：git merge branch-name --squash

#### 第 48 关 reorder

![](/images/githug48.jpg)
命令：git rebase -i 前一条 hash-code、调整顺序

#### 第 49 关 bisect

![](/images/githug49.jpg)
![](/images/githug_bisect.jpg)

#### 第 50 关 stage_lines

![](/images/githug50.jpg)
命令：git add file-name --edit

#### 第 51 关 find_old_branch

![](/images/githug51.jpg)
命令：git reflog：可以查看在 git 上的历史操作

#### 第 52 关 revert

![](/images/githug52.jpg)
命令：git revert bad-hash-code [--no-edit]自动生成一个提交信息

#### 第 53 关 restore

![](/images/githug53.jpg)
命令：git reflog、git reset --hard hash-code

#### 第 54 关 reflict

![](/images/githug54.jpg)
命令：git merge branch-name、

#### 第 55 关 submodule

![](/images/githug55.jpg)
命令：git submodule add module-url

#### 第 56 关 contribute

![](/images/githug56.jpg)
恭喜通关！！！

## 总结与提高

考试成绩下来了）滑稽
![](/images/githug57.jpeg)
再来看看另一个同学的，正所谓，没有对比，就没有开心）再次滑稽
![](/images/githug58.jpeg)
如何删除倒数第二次提交（未提交到远程）？
答：见第 45 关的 drop 或 调整下倒数第一和倒数第二的顺序然后 reset 一次
你的提交已经 push 到远程了，删除倒数第二次提交
答：上题一顿操作后 push -f（目前还没想到其它比较好的方法）
