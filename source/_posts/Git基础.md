---
title: Git基础
toc: true
date: 2022-05-14 15:29:53
updated:
tags:
- Pro Git
- Git
categories:
---
<!--more-->

# Git基础
主要介绍Git的基本使用方法


## 获取Git仓库
两种方法，一个是从现有项目或目录导入所有文件到Git，一个是从服务器克隆一个现有Git仓库

### 在现有项目中初始化
进入项目目录并输入`git init`,会创建一个`.git`子目录
### 克隆现有仓库
格式`git clone [url]`，git会克隆该Git仓库服务器上几乎所有的数据，包括每一个文件，每一个分支每一个版本，而不仅仅是我们可能关心的最新版本。
例如：
```
git clone https://github.com/libgit2/libgit2
```
会在当前目录创建一个名为`libgit2`的目录，并将远程仓库所有文件克隆进来，如果不想是默认的文件夹名，还可以指定我们自己的文件夹名称，格式如下：
```
git clone https://github.com/libgit2/libgit2 mylibgit
```
这个会执行跟上一个命令差不多的操作，不同点在于本地创建的仓库名字变为了`mylibgit`

Git支持多种数据传输协议，比如http://协议，git://协议以及ssh传输协议等

## 记录每次更新到仓库
Git工作目录下每一个文件两种广义一些的状态
已跟踪：指已经被纳入了版本控制的文件，上一次快照中有记录，一段时间后可能处于未修改、已修改或者放入暂存区等状态。
未跟踪：除了所有已跟踪的文件都属于魏耿总文件，既不存在上次快照记录中，也没有放入暂存区

### 检查当前文件状态
`git status`可以查看当前文件处于什么状态

### 跟踪新文件
`git add`命令，会跟踪未追踪文件，并使之处于暂存状态
`changes to be committed`下面的文件就说明是已暂存状态。`git add`命令使用文件名或者目录作为参数，如果参数是目录，那么会递归地跟踪目录下的所有文件

### 暂存已修改文件
`git add`实际上git是一个多功能命令，即可以用来跟踪新文件，还可以把已跟踪的文件放到暂存区，还能用于合并时把冲突文件标记为已解决状态。可以将这个命令理解为添加内容到下一次提交中，比较合适。
注意`git add`只是记录了运行时文件的状态，加入我们运行git add后没有commit 又修改了当前文件，那么当前文件可能会同时出现在暂存区和非暂存区

### 状态简览
`git status`命令输出十分详细，如果想要更紧凑，可以用`git status -s`或者`git status --short`命令，可以得到更紧凑的状态输出报告， 例如
```
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```
- `??`: 表示新添加的未跟踪文件
- `A`: 表示新添加到暂存区的文件
- `M`: 表示文件被修改了，它有左右两个可能出现的位置，左边表示修改了并放入了暂存区，右边表示修改了没放入暂存区

### 忽略文件
部分文件不需要放入Git的管理，实际上放进去会导致我们的仓库过于臃肿，看起来不专业，通常都是一些自动生成的文件，比如日志文件和编译过程中创建的临时文件。

可以通过`.gitignore`文件来设置忽略的格式。例如：
```
$ cat .gitignore
*.[oa]
*~
```
第一行告诉Git忽略所有以.o或者.a结尾的文件，第二行表示忽略所有以`~`结尾的文件，此外通常我们还可以忽略log, tmp或者pid目录，用于cmake的build目录通常也可以忽略，应该养成开始就设置好`.gitignore`文件的习惯

`.gitignore`文件的格式规范如下：
- 所有的空行或者以`#`开头的行都会被Git忽略
- 可以使用标准的glob模式匹配
- 匹配模式可以以(/)开头防止递归
- 匹配模式可以以(/)结尾指定目录
- 要忽略指定模式以外的文件或者某，可以在模式前加`!`取反

glob模式就是指shell使用的简化后的正则表达式,`*`匹配零个或者多个任意字符；`[abc]`方括号用于匹配任何列在方括号中的字符，`?`匹配任意一个字符，如果在方括号内用短划线分割两个字符，表示两个字符范围内的都可以匹配，比如`[0-9`表示匹配所有0到9的数字。使用两个星号表示匹配任意中间目录，例如`a/**/z`可以匹配`a/z`,`a/b/z`等等

再来看一个`.gitignore`例子
```
# no .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in the build/ directory
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
```

### 查看已暂存和未暂存的修改
`git diff`命令，可以查看具体修改了什么地方，`git diff`用于比较工作目录中当前文件和暂存区域块找回自己按的差异，也就是指修改后还没有暂存起来的变化内容。如果想要查看已暂存起来的变化内容，可以用`git diff --cached`高版本支持`git diff --stage`效果基本一致。

### 提交更新
`git commit`可以提交暂存区域的文件，但是注意使用前确保我们想提交的都放进暂存区了，所以一般使用前配合`git status`使用

直接使用`git commit`会启动文本编辑器输入本次提交的说明，更常用的用法是`git commit -m "说明内容"`

### 跳过使用暂存区域
我们还可以简化掉`git add`这一步，直接给`git commit`加上`-a`命令，Git就会自动把所有的**已跟踪文件**暂存并一并提交。因此可以配合`git commit -am "说明内容"`简化步骤

### 移除文件
要从git中移除某文件，就必须从已跟踪文件清单中移除（从暂存区域移除), 然后提交， `git rm`命令可以完成这个工作，我们可以先删掉文件，然后用`git rm`命令从暂存区域移除，如此一来，下次提交时，该文件就不再纳入版本管理了。

另外一种情况，我们只是想把文件从暂存区移除，而不想从磁盘删掉，只需要单独进行`git rm`这一步即可，比如可以`git rm --cached README`可以将readme从暂存区移除，同样可以使用glob模式


### 移动文件
`git mv`命令，例如可以这样子对git中的文件改名`git mv file_from file_to`

## 查看提交历史
`git log`命令，默认会安装提交时间列出所有的更新，最近的更新排在前面，包括每个提交的SHA-1校验和、作者名字、电子邮件、提交时间和提交说明。

`git log`可以添加一些有用的选项，常用的是`-p`可以显示每次提交的内容差异，还可以添加上`-2`来仅显示最近两次的提交。

`--stat`选项可以看到每次提交的简略的统计信息，

`git log` 常用选项：
- `-p`: 按补丁格式显示每个更新间的差异
- `--stat`：显示每次更新的文件修改统计信息
- `--shortstat`：只显示`--stat`最后的行数修改添加移除统计
- `--name-only`：在提交系信息后显示已修改的文件清单
- `--name-status`: 显示新增、修改、删除的文件清单
- `--abbrev-commit`: 仅显示SHA-1的前几个字符，而非所有的40字符
- `--relative-date`：使用较短的相对时间显示，比如”two weeks ago“
- `--graph`：显示ASCII图形表表示的分支合并历史
- `--prety`：使用其它格式显示历史提交信息，可用选项包括oneline，short，full，fuller和format等等
- `-(n)`： 仅显示最近的n条提交
- `--since or --after`： 仅显示指定时间之后的提交
- `--until or -before`: 仅显示指定时间之前的提交
- `--author`： 仅显示指定作者相关的提交
- `--committer`: 仅显示指定提交者相关的提交
- `--grep`：仅显示包含指定关键字的提交
- `-S`: 仅显示添加或移除了讴歌关键字的提交
  
## 撤销操作
介绍几个常用的撤销命令，但是某些撤销操作不可逆，也是Git使用过程中，会因为操作失误导致**之前工作丢失**的少有地方之一，需要额外的注意

提交完发现几个文件没有添加，或者提交信息写错，可以通过`--amend`选项会从高新提交，例如：
```
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```
最终只会有一个提交，第二次提交覆盖第一次提交的结果

### 取消暂存的文件
`git reset HEAD <file>`可以取消暂存某个文件
注意:`git reset`命令加上`--hard`命令可能会十分危险，可能导致工作目录中所有当前进度丢失，但是不加选项的调用`git reset`没什么问题，因为只会修改暂存区域

### 撤销对文件的修改
如果想要撤销某个修改，将其还原成上次提交的样子，可以用`git checkout -- <file>`，这个命令也比较危险，因为当前修改并未保存到git，撤销后会丢失当前所有的修改，通常采用分支是更好的办法，

## 远程仓库的使用
远程仓库用于Git项目协作，通常是指我们托管在github上的仓库，我们可以拥有不止一个远程仓库（这个之前不知道）

### 查看远程仓库
`git remote` 会列出指定的每一个远程服务器简写，`-v`命令会显示远程仓库的简写以及对应的URL，如果有多个远程仓库，会全部列出来。

### 添加远程仓库
`git remote add <shortname> <url>`可以添加一个新的远程Git仓库并同时指定一个简写

### 从远程仓库中抓取和拉去。
`git fetch [remote-name]` 访问指定的远程仓库并拉取所有本地没有的数据，若我们的分支设置为跟踪一个远程分支，则可以使用`git pull`命令来自动抓取且合并远程分支到当前分支。

### 推送到远程仓库
`git push [remote-name] [branch-name]` 推送到指定远程仓库的特定分支，同时也可以设置好本地分支追踪远程某仓库后省略后面两个选项。若我们和其他人同时push，则需要先拉取其工作合并手动处理冲突后才能推送。

### 查看远程仓库
`git remote show [remote-name]`可以查看特定远程仓库的信息，包括远程仓库的URL与跟踪分支的信息，还可以看到在特定分支执行git push，会自动推送到哪一个远程分支。

### 远程仓库的移除与重命名
`git remote rename [old name] [new name]`修改一个远程仓库的简写名，注意会同时修改远程分支的名字。
如果要移除某一个远程仓库，可以使用`git remote rm [remote-name]`


## 打标签
Git可以给历史的某一个提交打上标签，表示重要性，通常可以用来标记发布节点之类。
### 列出标签
`git tag`会以字母顺序列出标签

### 创建标签
Git使用的标签主要有两种类型，轻量标签和附注标签
轻量标签只是一个特定提交的引用
附注标签是存储在Git数据库中的一个完整对象，可以被校验包含打标签这的名字电子邮件地址日期时间还有一个标签信息。
通常来说，附注标签使用频率更高一些，

#### 辅助标签
Git中创建附注标签只需要在`tag`命令加上`-a`选项，例如：
```
git tag -a v1.4 -m "my version 1.4"
```
`-m`选项指定了存储在标签中的信息，（类似commit时的`-m`）
通过`git show`命令可以看到标签信息与对应的提交信息,包括打标签者信息，日期实践附注信息以及具体的提交信息。

### 轻量标签
我理解轻量标签类似一个别名，只是一个commit的引用，show起来也只有提交信息，创建时`git tag [标签名]`即可

### 后期打标签
如果我们前期提交的时候忘记打标签了，也是可以后期配合`git log`找到对应的提交信息然后补上标签，具体操作是`git tag -a <标签名> <提交的校验和>` 也可以是部分交验和，例如：
```
git tag -a v1.2 9fceb02
```
### 共享标签

默认情况下，`git push`并不会把标签传送到远程仓库服务器上，而是必须显式推送，`git push <branch-name> <tag-name>`，也可以使用`--tag`选项将所有不在远程仓库服务器上的标签全部传送上去。


### 检出标签
Git上并不能真正检出一个标签，因为标签无法像分支一样来回移动，但是可以在特定标签版本上创建一个新分支，使得我们的工作目录和特定标签版本完全一致，标签版本就是新分支的起点。`git checkout -b [branch-name] [tag-name]`
```
git checkout -b version2 v2.2
```

## git别名
可以通过`git config`文件个命令设置别名，例如：
```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```
值得一提的是，一个比较有用的别名是取消暂存文件，因为那个命令很长，不好用，可以向以下这样子设置：
```
git config --global alias.sub 'reset HEAD --'
```
这样子就好记而且好敲，和add对应

还有一个是
```
git config --global alias.last 'log -1 HEAD'
```
如此一来就可以用`git last`命令查看最近一次提交




