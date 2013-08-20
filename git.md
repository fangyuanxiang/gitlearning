Git简单介绍
===========

### 什么是它

* Linus Torvalds: the stupid content tracker

### 主要特点

* [小而快](http://git-scm.com/about/small-and-fast)
  * 为[Linux](https://git.kernel.org)而生
  * [Android](https://android.googlesource.com)使用
  * [github](https://github.com)专业git托管，公共项目免费
  * [bitbucket](https://bitbucket.org)同样著名，而且提供免费私人项目

* [分布式](http://git-scm.com/about/distributed)
  * 不需要中心服务器(停电？宕机？没影响)

* [GPL v2](http://git-scm.com/about/free-and-open-source)
  * 自由开源，可商业使用

### 下载安装

* [Xcode](https://developer.apple.com/xcode/): 自带git 1.7.12.4, 最新1.8.3.2

* 官方网站[git-scm.com](http://git-scm.com/download)直接安装，可能需要翻墙

### 配置使用

* `$GIT_DIR/config` 项目配置

* `~/.gitconfig` 用户配置

* 推荐配置

		shell> git config --global user.email ""
		shell> git config --global user.name ""
		shell> git config --global color.ui true

   **问题**: 如何为不同的项目配置不同的用户？

### 项目管理

* 新建项目

		shell> touch .gitignore
		shell> git init
		shell> git add .gitignore
		shell> git commit -m "first commit"

* 推送项目

		shell> git remote add origin /path/to/repository
		shell> git push -u origin master

  **提示**：`-u`即设定upstream，设定过后，不需要再重新设定。

* 克隆项目

		shell> git clone /path/to/origin

### 修改提交

* 状态

		shell> git status

* 添加

		shell> git add ... ... ...

* 修改

		shell> $EDITOR ...
		shell> git rm ...
		shell> git mv ...

* 提交

		shell> git commit file ... -m "message"
		或者
		shell> git add
		shell> git commit -m "message"
		或者
		shell> git commit -a -m "message"

	**提示**: 提交时带上-v参数，有惊喜哦

### 查看提交

* 变更

		shell> git diff <branch>...<branch> -- path
		shell> git diff <commit>...<commit> -- path

* 查看

		shell> git show ... -- path

* 日志

		shell> git log [-p] [--author=....] <since>...<until>
		shell> git log --since=<date> --until=<date>

	**问题**: 如果查找本月/上月代码提交行数？

* 个性

		shell> git log -p ...
		shell> git blame ...

	**提示**: `git log -p`侧重文件演化过程，而`git blame`则注重目前文件中的每一行是谁写的。

### 反悔提交

* 前提

  只要没有提交到远程，一切都可以修改。  
  **注意**: 请不要对已经push过的代码进行***任何***修改

* 评论

		shell> git commit --amend <SHA1>

* 文件

		shell> git checkout ....

* 重置

		shell> git reset ...
		shell> git reset --hard ...

* 回滚

  **提示**: 这将提交一个新的提交，可用于已经commit

		shell> git revert ...

* 反悔

  **前提**: 没有运行git gc

		shell> git fsck --lost-found
		shell> git reflog

### 忽略提交

* `$GIT_DIR/.gitignore`
  * 一行一个配置
  * 支持通配符，如`*` (`man 3 fnmatch`)
  * 以`#`开头，表示注释
  * 以`!`开头，表示不要忽略
  * 以`/`结尾，表示忽略目录
  * 以`/`开头，表示匹配路径

  **注意**: 如果一个文件已经被版本控制器管理，则不会被忽略！(可以`git rm [--cached] ...`)

  **问题**: 写一个配置文件，忽略Mac OS X或Windows临时文件，或者vim临时文件，以及非根目录下`.gitignore`

* `$GIT_DIR/info/exclude`

  使用方法同`$GIT_DIR/.gitignore`，不过这个文件不会被提交。

* 其它方法

		shell> git update-index --assume-unchanged ...

  这个方法，表示即使版本控制里有，但是还是想忽略它，任何改变将不会被显示。  
  但是，如果有其他人修改这些文件，可能还是会引起冲突。

		shell> git update-index --no-assume-unchanged ...

  要找出这些文件，可以使用以下命令:

		shell> git ls-files -v

  前面小写的就是忽略的，可以使用额外的grep，即

		shell> git ls-files -v | egrep '^[a-z]'

### 项目路径

* `$GIT_DIR`:

		shell> git rev-parse --git-dir

* 绝对路径

		shell> git rev-parse --show-toplevel

* 相对路径

		shell> git rev-parse --show-cdup

* 当前目录

		shell> git rev-parse --show-prefix

### 远程开发

* 添加上游

		shell> git remote add upstream /path/to/repository

  当clone时，默认会添加一个远程的仓库`origin`

* 获取更新

		shell> git fetch ...

* 合并更新

		shell> git merge ...

* 另类合并

		shell> git rebase ...

  rebase和merge功能类似，但是，它会把本地的每一个提交按顺序试图重新整合，最后是一个线性图谱。  
  同时，rebase会新建一个未命名分支合并，因此，也是安全的。一般来说，rebase会做更多的事。

* 合二为一

		shell> git pull

  此命令等同为`git fetch`加上`git merge`，并且会试图解决依赖。  
  本人建议先`fetch`然后再`merge`，有冲突时会自动解决。

* 解决冲突

  如果无法自动合并，会有如下代码:

		<<<<<<< 自己的
		+++++
		=======
		+
		>>>>>>> 别人的

  这种方式不太友好，我们使用一种新的方式来表示，先改变`merge.conflictstyle`:

		shell> git config merge.conflictstyle diff3

  将会显示为以下形式:

		<<<<<<< 自己的
		+++++
		||||||| 共同的
		+++
		=======
		+
		>>>>>>> 别人的

  由于我们的代码是有一个共同的祖先，在diff3模式下，表示相同祖先不同改变情况。

### 分支开发

* 新建分支

		shell> git checkout -b <branch> ...

  **说明**: 可以是tag, SHA1, branch, ...

* 删除分支

		shell> git branch -d <branch>
		shell> git branch -D <branch>

  大D删除，不管当前的merge状态，默认的小d删除，如果有没有merge的文件，将不能提交。

* 设定上游

		shell> git branch --set-upstream <repository>

* 切换分支

		shell> git checkout ...

  **提示**: 和checkout文件一样，这个会自动判断。  
  **问题**: 如果branch和文件一样，得到的是啥？

* 远程分支

		shell> git push <repository> [<branch>]

		shell> git push <repository> :<branch>
		shell> git push --delete <repository> <branch>

* 清除分支

  有时，远程分支已经不存在的，但是本地分支还在。是的，不会自动清空:

		shell> git remote prune <upstream>

### 区域关系

* 工作区域

  人称`working directory`

* 缓存区域

  人称`staging area`, 亦称`index`

* 仓库区域

  人称`repository`，亦称`HEAD`

		+-----------+
		|  working  |  --------------+
		+-----------+                |
		      |                      |
		      |  git add             |
		      |  git diff            |
		      v                      |
		+-----------+                |  git commit -a
		|  staging  |                |
		+-----------+                |  git diff HEAD
		      |                      |
		      |  git commit          |
		      |  git diff --cached   |
		      v                      |
		+-----------+                |
		|  repos..  |  <-------------+
		+-----------+

### 生成快照

	shell> git archive --prefix=project/ HEAD -o project.zip

  这个生成快照，不带`.git`任何信息，可以用来发布版本。  
  当然，格式方式，支持`zip`和`tar`，使用`--format`指定。

### 校验钩子

  钩子，人称`hooks`。默认情况下，在`$GIT_DIR/hooks`下有一些钩子示例，把.sample去除可以直接使用。  
  比如我们要在提交之前，先自我较验代码，如果不通过就直接失败，则可以定义`$GIT_DIR/hooks/pre-commit`。  
  更多用法，请直接:

	shell> man githooks

### 如何学习

* `man git`，查看手册

* 对于命令，可以使用`man git-<command>`，如`man git-checkout`

* 更多的话，`内事不决问百度，外事不决问谷歌`

### 其它内容

* 标签

  人称`tag`，支持以下行为:

  * 添加标签

			shell> git tag <name>
			shell> git tag <name> [<commit>]

  * 删除标签

			shell> git tag -d <name>

  * 其它标签

			shell> man git-tag

* 二级模块

		shell> man git-submodule

* 其它软件

  * subversion

			shell> man git-svn

  * cvs

			shell> man git-cvsimport
			shell> man git-cvsexportcommit
			shell> man git-cvsserver

  * perforce

			shell> man git-p4

