---
title: git学习笔记
toc: true
tags:
  - git
categories:
  - Linux学习
date: 2022-01-19 14:45:52
updated:
---
<!--more-->
## git介绍
git是一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。

## git基本概念
- 工作区：仓库的目录。工作区是独立于各个分支的；
- 暂存区：数据暂存点区域，类似于工作区写入版本库前的缓冲区。暂存区是独立于各个分支的；
- 版本库：存放所有已经提交到本体仓库的代码版本；
- 版本结构：树结构，树中的每一个节点代表一个代码版本。

## git常用命令
### 全局设置
1. `git config --global user.name xxx`: 设置全局用户名，信息记录在`~/.gitconfig`文件中
2. `git config --global user.email xxx`: 设置全局邮箱地址，信息记录在 `~/.gitconfig`文件中
3. `git init`: 将当前目录设置为git仓库，信息记录在隐藏的`.git`文件夹中
   

### 常用命令
1. `git status`：查看仓库状态
2. `git add xxx`：将xxx文件添加到暂存区
   - `git add .`：将所有修改过的文件添加到暂存区
3. `git commit -m "xxx"`：将暂存区内容提交到当前分支（持久化为一个节点，xxx为给自己看的备注信息）
4. `git push`：将当前分支推送到远程仓库的对应分支
5. `git branch`：查看所有分支和当前所处分支
6. `git remote add origin git@git.acwing.com:xxx/XXX.git`：将本地仓库关联到远程仓库
7. `git clone git@git.acwing.com:xxx/XXX.git`：将远程仓库xxx下载到当前目录下

### 查看命令
1. `git diff xxx`: 查看xxx文件相对于暂存区的修改内容
2. `git status`：查看仓库状态
3. `git log`：查看当前分支的所有版本
   - `git log --pretty=online`查看当前分支的所有版本（每个版本用一行来显示）
4. `git reflog`：查看HEAD指针的移动历史（包括被回滚的版本）
5. `git branch`：查看所有分支和当前所处分支

### 删除命令
1. `git rm --cached xxx`：将xxx文件从仓库索引目录删掉，不希望管理该文件
2. `git restore --staged xxx`：将xxx文件从暂存区里移除
3. `git restore xxx`:将xxx文件尚未加入暂存区的修改全部撤销（暂存区没有则恢复到当前分支的HEAD所指向的版本状态）
   - `git restore`：将所有文件尚未加入暂存区的修改全部撤销
4. `git remote rm origin`:删除现有的origin地址（然后可以重新关联一个远程仓库）

### 代码回滚
1. `git reset --hard HEAD^`或`git reset --hard HEAD~`：将代码回滚到上一个版本
2. `git reset --hard HEAD^^`：将代码向上回滚两次，以此类推
3. `git reset --hard HEAD~100`：往上回滚一百个版本
4. `git reset --hard 版本号`：回滚到特定版本（`git reflog`查看到的七位版本号）

### 分支命令
1. `git branch xxx`：创建xxx新分支
2. `git branch`：查看所有分支和当前所处分支
3. `git checkout -b xxx`：创建并切换到xxx分支
4. `git checkout xxx`：切换到xxx分支
5. `git merge xxx`：将分支xxx合并到当前分支上
6. `git branch -d xxx`：删除本地仓库的xxx分支
7. `git push -d origin xxx`：删除远程仓库的xxx分支
8. `git push --set-upstream origin xxx`：设置本地当前的分支对应远程仓库的xxx分支（同时在远程创建该分支，一般用于某个分支第一次push到远程仓库）
9. `git branch --set-upstream-to=origin/xxx xxx`：将远程仓库的某个分支对应本地的某个分支（一般用于将远程已存在的某个分支对应到当前的新建分支上）
10. `git pull`：将远程仓库的当前分支与本地仓库的当前分支合并
    - `git pull origin xxx`：将远程仓库的xxx分支与本地仓库的当前分支合并


### stash缓存
1. `git stash`: 将工作区和暂存区中尚未提交的修改存入栈中
2. `git stash apply`：将栈顶储存的修改恢复到**当前分支**，但不删除栈顶元素
3. `git stash drop`：删除栈顶储存的修改
4. `git stash pop`：将栈顶储存的修改恢复到**当前分支**，同时删除栈顶元素
5. `git stash list`：查看栈中的所有元素
----
>大部分源自yxc上课笔记，仅用作个人记录、查询
----
**欢迎讨论指正**