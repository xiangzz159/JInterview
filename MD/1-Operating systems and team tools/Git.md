# Git分布式版本控制
- [Git分布式版本控制](#git分布式版本控制)
- [Git](#git)
  - [Git和SVN区别](#git和svn区别)
- [Git常用命令](#git常用命令)
  - [一、新建代码库](#一新建代码库)
  - [二、配置](#二配置)
  - [三、增加/删除文件](#三增加删除文件)
  - [四、代码提交](#四代码提交)
  - [五、分支](#五分支)
  - [六、标签](#六标签)
  - [七、查看信息](#七查看信息)
  - [八、远程同步](#八远程同步)
  - [九、撤销](#九撤销)
  - [十、其他](#十其他)
- [Git使用流程规范](#git使用流程规范)
  - [一、创建分支](#一创建分支)
  - [二、提交分支](#二提交分支)
  - [三、撰写提交信息](#三撰写提交信息)
  - [四、与主分支同步](#四与主分支同步)
  - [五、提交commit](#五提交commit)
  - [六、推送到远程仓库](#六推送到远程仓库)
  - [七、发出Pull Request请求](#七发出pull-request请求)
- [Git工作流程](#git工作流程)
  - [一、功能驱动](#一功能驱动)
  - [二、Git Flow](#二git-flow)
    - [特点：](#特点)
    - [评价](#评价)
  - [三、Github Flow](#三github-flow)
    - [流程](#流程)
    - [评价](#评价-1)
  - [四、Gitlab Flow](#四gitlab-flow)
    - [上游优先](#上游优先)
    - [持续发布](#持续发布)
    - [版本发布](#版本发布)
- [Git远程操作解析](#git远程操作解析)
  - [一、git clone](#一git-clone)
  - [二、git remote](#二git-remote)
  - [三、git fetch](#三git-fetch)
  - [四、git pull](#四git-pull)
  - [五、git push](#五git-push)

# Git
Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。  

Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

## Git和SVN区别
Git 不仅仅是个版本控制系统，它也是个内容管理系统(CMS)，工作管理系统等。

如果你是一个具有使用 SVN 背景的人，你需要做一定的思想转换，来适应 Git 提供的一些概念和特征。

Git 与 SVN 区别点：

- Git 是分布式的，SVN 不是：这是 Git 和其它非分布式的版本控制系统，例如 SVN，CVS 等，最核心的区别。

- Git 把内容按元数据方式存储，而 SVN 是按文件：所有的资源控制系统都是把文件的元信息隐藏在一个类似 .svn、.cvs 等的文件夹里。

- Git 分支和 SVN 的分支不同：分支在 SVN 中一点都不特别，其实它就是版本库中的另外一个目录。

- Git 没有一个全局的版本号，而 SVN 有：目前为止这是跟 SVN 相比 Git 缺少的最大的一个特征。

- Git 的内容完整性要优于 SVN：Git 的内容存储使用的是 SHA-1 哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。
  
![img](./img/0D32F290-80B0-4EA4-9836-CA58E22569B3.png)


# Git常用命令
## 一、新建代码库
```shell script
# 在当前目录新建一个Git代码库
[root@localhost ~]# git init

# 新建一个目录，将其初始化为Git代码库
[root@localhost ~]# git init [project-name]

# 下载一个项目和它的整个代码历史
[root@localhost ~]# git clone [url]
```

## 二、配置
Git的设置文件为.gitconfig，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。
```shell script
# 显示当前的Git配置
[root@localhost ~]# git config --list
# 编辑Git配置文件
[root@localhost ~]# git config -e [--global]
# 设置提交代码时的用户信息
[root@localhost ~]# git config [--global] user.name [username]
[root@localhost ~]# git config [--global] user.email [email]

```

## 三、增加/删除文件
``` shell script
# 添加指定文件到暂存区
[root@localhost ~]# git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
[root@localhost ~]# git add [dir]

# 添加当前目录的所有文件到暂存区
[root@localhost ~]# git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
[root@localhost ~]# git add -p

# 删除工作区文件，并且将这次删除放入暂存区
[root@localhost ~]# git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
[root@localhost ~]# git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
[root@localhost ~]# git mv [file-original] [file-renamed]
```

## 四、代码提交
```shell script
# 提交暂存区到仓库区
[root@localhost ~]# git commit -m [message]

# 提交暂存区的指定文件到仓库区
[root@localhost ~]# git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
[root@localhost ~]# git commit -a

# 提交时显示所有diff信息
[root@localhost ~]# git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
[root@localhost ~]# git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
[root@localhost ~]# git commit --amend [file1] [file2] ...
```
## 五、分支
```shell script
# 列出所有本地分支
[root@localhost ~]# git branch

# 列出所有远程分支
[root@localhost ~]# git branch -r

# 列出所有本地分支和远程分支
[root@localhost ~]# git branch -a

# 新建一个分支，但依然停留在当前分支
[root@localhost ~]# git branch [branch-name]

# 新建一个分支，并切换到该分支
[root@localhost ~]# git checkout -b [branch]

# 新建一个分支，指向指定commit
[root@localhost ~]# git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
[root@localhost ~]# git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
[root@localhost ~]# git checkout [branch-name]

# 切换到上一个分支
[root@localhost ~]# git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
[root@localhost ~]# git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
[root@localhost ~]# git merge [branch]

# 选择一个commit，合并进当前分支
[root@localhost ~]# git cherry-pick [commit]

# 删除分支
[root@localhost ~]# git branch -d [branch-name]

# 删除远程分支
[root@localhost ~]# git push origin --delete [branch-name]
[root@localhost ~]# git branch -dr [remote/branch]
```
## 六、标签
```shell script
# 列出所有tag
[root@localhost ~]# git tag

# 新建一个tag在当前commit
[root@localhost ~]# git tag [tag]

# 新建一个tag在指定commit
[root@localhost ~]# git tag [tag] [commit]

# 删除本地tag
[root@localhost ~]# git tag -d [tag]

# 删除远程tag
[root@localhost ~]# git push origin :refs/tags/[tagName]

# 查看tag信息
[root@localhost ~]# git show [tag]

# 提交指定tag
[root@localhost ~]# git push [remote] [tag]

# 提交所有tag
[root@localhost ~]# git push [remote] --tags

# 新建一个分支，指向某个tag
[root@localhost ~]# git checkout -b [branch] [tag]
```
## 七、查看信息
```shell script
# 显示有变更的文件
[root@localhost ~]# git status

# 显示当前分支的版本历史
[root@localhost ~]# git log

# 显示commit历史，以及每次commit发生变更的文件
[root@localhost ~]# git log --stat

# 搜索提交历史，根据关键词
[root@localhost ~]# git log -S [keyword]

# 显示某个commit之后的所有变动，每个commit占据一行
[root@localhost ~]# git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
[root@localhost ~]# git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
[root@localhost ~]# git log --follow [file]
[root@localhost ~]# git whatchanged [file]

# 显示指定文件相关的每一次diff
[root@localhost ~]# git log -p [file]

# 显示过去5次提交
[root@localhost ~]# git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
[root@localhost ~]# git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
[root@localhost ~]# git blame [file]

# 显示暂存区和工作区的差异
[root@localhost ~]# git diff

# 显示暂存区和上一个commit的差异
[root@localhost ~]# git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
[root@localhost ~]# git diff HEAD

# 显示两次提交之间的差异
[root@localhost ~]# git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
[root@localhost ~]# git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
[root@localhost ~]# git show [commit]

# 显示某次提交发生变化的文件
[root@localhost ~]# git show --name-only [commit]

# 显示某次提交时，某个文件的内容
[root@localhost ~]# git show [commit]:[filename]

# 显示当前分支的最近几次提交
[root@localhost ~]# git reflog
```
## 八、远程同步
```shell script
# 下载远程仓库的所有变动
[root@localhost ~]# git fetch [remote]

# 显示所有远程仓库
[root@localhost ~]# git remote -v

# 显示某个远程仓库的信息
[root@localhost ~]# git remote show [remote]

# 增加一个新的远程仓库，并命名
[root@localhost ~]# git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
[root@localhost ~]# git pull [remote] [branch]

# 上传本地指定分支到远程仓库
[root@localhost ~]# git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
[root@localhost ~]# git push [remote] --force

# 推送所有分支到远程仓库
[root@localhost ~]# git push [remote] --all
```
## 九、撤销
```shell script
# 恢复暂存区的指定文件到工作区
[root@localhost ~]# git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
[root@localhost ~]# git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
[root@localhost ~]# git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
[root@localhost ~]# git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
[root@localhost ~]# git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
[root@localhost ~]# git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
[root@localhost ~]# git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
[root@localhost ~]# git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
[root@localhost ~]# git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
[root@localhost ~]# git stash
[root@localhost ~]# git stash pop
```
## 十、其他
```shell script
# 生成一个可供发布的压缩包
[root@localhost ~]# git archive
```

# Git使用流程规范
![bg2015080501](./img/bg2015080501.png)
## 一、创建分支
首先，每次开发新功能，都应该新建一个单独的分支。
```shell script
# 获取主干最新代码
[root@localhost ~]# git checkout master
[root@localhost ~]# git pull

# 新建一个开发分支myfeature
[root@localhost ~]# git checkout -b myfeature
```
## 二、提交分支
分支修改后，就可以提交commit了。
```shell script
[root@localhost ~]# git add --all
[root@localhost ~]# git status
[root@localhost ~]# git commit --verbose
```
git add 命令的all参数，表示保存所有变化（包括新建、修改和删除）。从Git 2.0开始，all是 git add 的默认参数，所以也可以用 git add . 代替。

git status 命令，用来查看发生变动的文件。

git commit 命令的verbose参数，会列出 diff 的结果。
## 三、撰写提交信息
提交commit时，必须给出完整扼要的提交信息，下面是一个范本。
```shell script
Present-tense summary under 50 characters

* More information about commit (under 72 characters).
* More information about commit (under 72 characters).

http://project.management-system.com/ticket/123
```
第一行是不超过50个字的提要，然后空一行，罗列出改动原因、主要变动、以及需要注意的问题。最后，提供对应的网址（比如Bug ticket）。
## 四、与主分支同步
分支的开发过程中，要经常与主干保持同步。
```shell script
[root@localhost ~]# git fetch origin
[root@localhost ~]# git rebase origin/master
```
## 五、提交commit
分支开发完成后，很可能有一堆commit，但是合并到主干的时候，往往希望只有一个（或最多两三个）commit，这样不仅清晰，也容易管理。

那么，怎样才能将多个commit合并呢？这就要用到 git rebase 命令。
```shell script
[root@localhost ~]# git rebase -i origin/master
```
git rebase命令的i参数表示互动（interactive），这时git会打开一个互动界面，进行下一步操作。

下面采用Tute Costa的例子，来解释怎么合并commit。

```shell script
pick 07c5abd Introduce OpenPGP and teach basic usage
pick de9b1eb Fix PostChecker::Post#urls
pick 3e7ee36 Hey kids, stop all the highlighting
pick fa20af3 git interactive rebase, squash, amend

# Rebase 8db7e8b..fa20af3 onto 8db7e8b
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```
上面的互动界面，先列出当前分支最新的4个commit（越下面越新）。每个commit前面有一个操作命令，默认是pick，表示该行commit被选中，要进行rebase操作。

4个commit的下面是一大堆注释，列出可以使用的命令。
> pick：正常选中  
> reword：选中，并且修改提交信息；  
> edit：选中，rebase时会暂停，允许你修改这个commit（参考这里）  
> squash：选中，会将当前commit与上一个commit合并  
> fixup：与squash相同，但不会保存当前commit的提交信息  
> exec：执行其他shell命令  

上面这6个命令当中，squash和fixup可以用来合并commit。先把需要合并的commit前面的动词，改成squash（或者s）。
```shell script
pick 07c5abd Introduce OpenPGP and teach basic usage
s de9b1eb Fix PostChecker::Post#urls
s 3e7ee36 Hey kids, stop all the highlighting
pick fa20af3 git interactive rebase, squash, amend
```
这样一改，执行后，当前分支只会剩下两个commit。第二行和第三行的commit，都会合并到第一行的commit。提交信息会同时包含，这三个commit的提交信息。
```shell script
# This is a combination of 3 commits.
# The first commit's message is:
Introduce OpenPGP and teach basic usage

# This is the 2nd commit message:
Fix PostChecker::Post#urls

# This is the 3rd commit message:
Hey kids, stop all the highlighting
```
如果将第三行的squash命令改成fixup命令。
```shell script
pick 07c5abd Introduce OpenPGP and teach basic usage
s de9b1eb Fix PostChecker::Post#urls
f 3e7ee36 Hey kids, stop all the highlighting
pick fa20af3 git interactive rebase, squash, amend
```
运行结果相同，还是会生成两个commit，第二行和第三行的commit，都合并到第一行的commit。但是，新的提交信息里面，第三行commit的提交信息，会被注释掉。
```shell script
# This is a combination of 3 commits.
# The first commit's message is:
Introduce OpenPGP and teach basic usage

# This is the 2nd commit message:
Fix PostChecker::Post#urls

# This is the 3rd commit message:
# Hey kids, stop all the highlighting
```

另外一种合并commit的简便方法，就是先撤销过去5个commit，然后再建一个新的。
```shell script
[root@localhost ~]# git reset HEAD~5
[root@localhost ~]# git add .
[root@localhost ~]# git commit -am "Here's the bug fix that closes #28"
[root@localhost ~]# git push --force
```
squash和fixup命令，还可以当作命令行参数使用，自动合并commit。

```shell script
[root@localhost ~]# git commit --fixup  
[root@localhost ~]# git rebase -i --autosquash 
```

## 六、推送到远程仓库
合并commit后，就可以推送当前分支到远程仓库了。
```shell script
[root@localhost ~]# git push --force origin myfeature
```
git push命令要加上force参数，因为rebase以后，分支历史改变了，跟远程分支不一定兼容，有可能要强行推送.
## 七、发出Pull Request请求
提交到远程仓库以后，就可以发出 Pull Request 到master分支，然后请求别人进行代码review，确认可以合并到master。

# Git工作流程
Git 作为一个源码管理系统，不可避免涉及到多人协作。

协作必须有一个规范的工作流程，让大家有效地合作，使得项目井井有条地发展下去。"工作流程"在英语里，叫做"workflow"或者"flow"，原意是水流，比喻项目像水流那样，顺畅、自然地向前流动，不会发生冲击、对撞、甚至漩涡。

这里介绍三种广泛使用的工作流程
- Git Flow
- Github Flow
- Gitlab Flow

## 一、功能驱动
本文的三种工作流程，有一个共同点：都采用"[功能驱动式开发](https://en.wikipedia.org/wiki/Feature-driven_development)"（Feature-driven development，简称FDD）。

它指的是，需求是开发的起点，先有需求再有功能分支（feature branch）或者补丁分支（hotfix branch）。完成开发后，该分支就合并到主分支，然后被删除。
## 二、Git Flow
最早诞生、并得到广泛采用的一种工作流程，就是[Git flow](https://nvie.com/posts/a-successful-git-branching-model/)。
### 特点：
它最主要的特点有两个。

![bg2015122302](./img/bg2015122302.png)

首先，项目存在两个长期分支。
- 主分支master
- 开发分支develop

前者用于存放对外发布的版本，任何时候在这个分支拿到的，都是稳定的分布版；后者用于日常开发，存放最新的开发版。

其次，项目存在三种短期分支。

- 功能分支（feature branch）
- 补丁分支（hotfix branch）
- 预发分支（release branch）

一旦完成开发，它们就会被合并进develop或master，然后被删除。
### 评价
Git flow的优点是清晰可控，缺点是相对复杂，需要同时维护两个长期分支。大多数工具都将master当作默认分支，可是开发是在develop分支进行的，这导致经常要切换分支，非常烦人。

更大问题在于，这个模式是基于"版本发布"的，目标是一段时间以后产出一个新版本。但是，很多网站项目是"持续发布"，代码一有变动，就部署一次。这时，master分支和develop分支的差别不大，没必要维护两个长期分支。

## 三、Github Flow
[Github flow](http://scottchacon.com/2011/08/31/github-flow.html) 是Git flow的简化版，专门配合"持续发布"。它是 Github.com 使用的工作流程。

### 流程
它只有一个长期分支，就是master，因此用起来非常简单。

![bg2015122305](./img/bg2015122305.png)

1. 根据需求，从master拉出新分支，不区分功能分支或补丁分支。
2. 新分支开发完成后，或者需要讨论的时候，就向master发起一个pull request（简称PR）。
3. Pull Request既是一个通知，让别人注意到你的请求，又是一种对话机制，大家一起评审和讨论你的代码。对话过程中，你还可以不断提交代码。
4. 你的Pull Request被接受，合并进master，重新部署后，原来你拉出来的那个分支就被删除。（先部署再合并也可。）

### 评价
Github flow 的最大优点就是简单，对于"持续发布"的产品，可以说是最合适的流程。

问题在于它的假设：master分支的更新与产品的发布是一致的。也就是说，master分支的最新代码，默认就是当前的线上代码。

可是，有些时候并非如此，代码合并进入master分支，并不代表它就能立刻发布。比如，苹果商店的APP提交审核以后，等一段时间才能上架。这时，如果还有新的代码提交，master分支就会与刚发布的版本不一致。另一个例子是，有些公司有发布窗口，只有指定时间才能发布，这也会导致线上版本落后于master分支。

上面这种情况，只有master一个主分支就不够用了。通常，你不得不在master分支以外，另外新建一个production分支跟踪线上版本。

## 四、Gitlab Flow
[Gitlab flow](http://doc.gitlab.com/ee/workflow/gitlab_flow.html) 是 Git flow 与 Github flow 的综合。它吸取了两者的优点，既有适应不同开发环境的弹性，又有单一主分支的简单和便利。它是 Gitlab.com 推荐的做法。
### 上游优先
Gitlab flow 的最大原则叫做"上游优先"（upsteam first），即只存在一个主分支master，它是所有其他分支的"上游"。只有上游分支采纳的代码变化，才能应用到其他分支。

[Chromium](https://www.chromium.org/chromium-os/chromiumos-design-docs/upstream-first)项目就是一个例子，它明确规定，上游分支依次为：
1. Linus Torvalds的分支
2. 子系统（比如netdev）的分支
3. 设备厂商（比如三星）的分支

### 持续发布
Gitlab flow 分成两种情况，适应不同的开发流程。

![bg2015122306](./img/bg2015122306.png)

对于"持续发布"的项目，它建议在master分支以外，再建立不同的环境分支。比如，"开发环境"的分支是master，"预发环境"的分支是pre-production，"生产环境"的分支是production。

开发分支是预发分支的"上游"，预发分支又是生产分支的"上游"。代码的变化，必须由"上游"向"下游"发展。比如，生产环境出现了bug，这时就要新建一个功能分支，先把它合并到master，确认没有问题，再cherry-pick到pre-production，这一步也没有问题，才进入production。

只有紧急情况，才允许跳过上游，直接合并到下游分支。

### 版本发布
![bg2015122307.png](./img/bg2015122307.png)

对于"版本发布"的项目，建议的做法是每一个稳定版本，都要从master分支拉出一个分支，比如2-3-stable、2-4-stable等等。

以后，只有修补bug，才允许将代码合并到这些分支，并且此时要更新小版本号。

# Git远程操作解析

Git有很多优势，其中之一就是远程操作非常简便。本文详细介绍5个Git命令，它们的概念和用法，理解了这些内容，你就会完全掌握Git远程操作。

- git clone
- git remote
- git fetch
- git pull
- git push


本文针对初级用户，从最简单的讲起，但是需要读者对Git的基本用法有所了解。同时，本文覆盖了上面5个命令的几乎所有的常用用法，所以对于熟练用户也有参考价值。

## 一、git clone
远程操作的第一步，通常是从远程主机克隆一个版本库，这时就要用到git clone命令。
```shell script
$ git clone <版本库的网址>
```
该命令会在本地主机生成一个目录，与远程主机的版本库同名。如果要指定不同的目录名，可以将目录名作为git clone命令的第二个参数。
```shell script
$ git clone <版本库的网址> <本地目录名>
```
git clone支持多种协议，除了HTTP(s)以外，还支持SSH、Git、本地文件协议等，下面是一些例子。
```shell script
$ git clone http[s]://example.com/path/to/repo.git/
$ git clone ssh://example.com/path/to/repo.git/
$ git clone git://example.com/path/to/repo.git/
$ git clone /opt/git/project.git 
$ git clone file:///opt/git/project.git
$ git clone ftp[s]://example.com/path/to/repo.git/
$ git clone rsync://example.com/path/to/repo.git/
```
SSH协议还有另一种写法。
```shell script
$ git clone [user@]example.com:path/to/repo.git/
```
通常来说，Git协议下载速度最快，SSH协议用于需要用户认证的场合。

## 二、git remote
为了便于管理，Git要求每个远程主机都必须指定一个主机名。git remote命令就用于管理主机名。

不带选项的时候，git remote命令列出所有远程主机。
```shell script
$ git remote
origin
# 使用-v选项，可以参看远程主机的网址。
$ git remote -v
origin  git@github.com:jquery/jquery.git (fetch)
origin  git@github.com:jquery/jquery.git (push)
```
上面命令表示，当前只有一台远程主机，叫做origin，以及它的网址。

克隆版本库的时候，所使用的远程主机自动被Git命名为origin。如果想用其他的主机名，需要用git clone命令的-o选项指定。
```shell script
$ git clone -o jQuery https://github.com/jquery/jquery.git
$ git remote
jQuery
```
上面命令表示，克隆的时候，指定远程主机叫做jQuery。

```shell script
# git remote show命令加上主机名，可以查看该主机的详细信息。
$ git remote show <主机名>
# git remote add命令用于添加远程主机。
$ git remote add <主机名> <网址>
# git remote rm命令用于删除远程主机。
$ git remote rm <主机名>
git remote rename命令用于远程主机的改名。
$ git remote rename <原主机名> <新主机名>
```

## 三、git fetch
一旦远程主机的版本库有了更新（Git术语叫做commit），需要将这些更新取回本地，这时就要用到git fetch命令。
```shell script
$ git fetch <远程主机名>
```
上面命令将某个远程主机的更新，全部取回本地。

git fetch命令通常用来查看其他人的进程，因为它取回的代码对你本地的开发代码没有影响。

默认情况下，git fetch取回所有分支（branch）的更新。如果只想取回特定分支的更新，可以指定分支名。

```shell script
$ git fetch <远程主机名> <分支名>

```
比如，取回origin主机的master分支。
```shell script
$ git fetch origin master
```
所取回的更新，在本地主机上要用"远程主机名/分支名"的形式读取。比如origin主机的master，就要用origin/master读取。

git branch命令的-r选项，可以用来查看远程分支，-a选项查看所有分支。
```shell script
$ git branch -r
origin/master

$ git branch -a
* master
  remotes/origin/master
```
上面命令表示，本地主机的当前分支是master，远程分支是origin/master。

取回远程主机的更新以后，可以在它的基础上，使用git checkout命令创建一个新的分支。
```shell script
$ git checkout -b newBrach origin/master
```
上面命令表示，在origin/master的基础上，创建一个新分支。

此外，也可以使用git merge命令或者git rebase命令，在本地分支上合并远程分支。
```shell script
$ git merge origin/master
# 或者
$ git rebase origin/master
```
上面命令表示在当前分支上，合并origin/master。

## 四、git pull
git pull命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。它的完整格式稍稍有点复杂。
```shell script
$ git pull <远程主机名> <远程分支名>:<本地分支名>
```
比如，取回origin主机的next分支，与本地的master分支合并，需要写成下面这样。
```shell script
$ git pull origin next:master
```
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。
```shell script
$ git pull origin next
```
上面命令表示，取回origin/next分支，再与当前分支合并。实质上，这等同于先做git fetch，再做git merge。
```shell script
$ git fetch origin
$ git merge origin/next
```
在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系（tracking）。比如，在git clone的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的master分支自动"追踪"origin/master分支。

Git也允许手动建立追踪关系。
```shell script
git branch --set-upstream master origin/next
```
上面命令指定master分支追踪origin/next分支。

如果当前分支与远程分支存在追踪关系，git pull就可以省略远程分支名。
```shell script
$ git pull origin
```
上面命令表示，本地的当前分支自动与对应的origin主机"追踪分支"（remote-tracking branch）进行合并。

如果当前分支只有一个追踪分支，连远程主机名都可以省略。
```shell script
$ git pull
```
上面命令表示，当前分支自动与唯一一个追踪分支进行合并。

如果合并需要采用rebase模式，可以使用--rebase选项。
```shell script
$ git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
```
如果远程主机删除了某个分支，默认情况下，git pull [root@localhost ~]# pull不知不觉删除了本地分支。

但是，你可以改变这个行为，加上参数 -p 就会在本地删除远程已经删除的分支。
```shell script
$ git pull -p
# 等同于下面的命令
$ git fetch --prune origin 
$ git fetch -p
```
## 五、git push
git push命令用于将本地分支的更新，推送到远程主机。它的格式与git pull命令相仿。

```shell script
$ git push <远程主机名> <本地分支名>:<远程分支名>
```
注意，分支推送顺序的写法是<来源地>:<目的地>，所以git pull是<远程分支>:<本地分支>，而git push是<本地分支>:<远程分支>。

如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。

```shell script
$ git push origin master
```
上面命令表示，将本地的master分支推送到origin主机的master分支。如果后者不存在，则会被新建。

如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。

```shell script
$ git push origin :master
# 等同于
$ git push origin --delete master
```
上面命令表示删除origin主机的master分支。

如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。

```shell script
$ git push origin
```
上面命令表示，将当前分支推送到origin主机的对应分支。

如果当前分支只有一个追踪分支，那么主机名都可以省略。

```shell script
$ git push
```
如果当前分支与多个主机存在追踪关系，则可以使用-u选项指定一个默认主机，这样后面就可以不加任何参数使用git push。

```shell script
$ git push -u origin master
```
上面命令将本地的master分支推送到origin主机，同时指定origin为默认主机，后面就可以不加任何参数使用git push了。

不带任何参数的git push，默认只推送当前分支，这叫做simple方式。此外，还有一种matching方式，会推送所有有对应的远程分支的本地分支。Git 2.0版本之前，默认采用matching方法，现在改为默认采用simple方式。如果要修改这个设置，可以采用git config命令。

```shell script
$ git config --global push.default matching
# 或者
$ git config --global push.default simple
```
还有一种情况，就是不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机，这时需要使用--all选项。

```shell script
$ git push --all origin
```
上面命令表示，将所有本地分支都推送到origin主机。

如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用--force选项。

```shell script
$ git push --force origin 
```
上面命令使用--force选项，结果导致远程主机上更新的版本被覆盖。除非你很确定要这样做，否则应该尽量避免使用--force选项。

最后，git push不会推送标签（tag），除非使用--tags选项。










