# 如何使用Git和码云Git@OSC


### 1.Git简介
关于Git是什么，阅读博客[Git简介](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001373962845513aefd77a99f4145f0a2c7a7ca057e7570000)
### 2.Git 基础
Git命令很多，常用命令如下图
![](http://image.beekka.com/blog/2014/bg2014061202.jpg)

> - Workspace:工作区
> - Index/Stage :暂存区
> - Repository: 本地仓库
> - Remote：远程仓库

工作区、暂存区和本地仓库，逻辑上是本地计算机。当我们新建一个文件时，文件位于工作区，处于已修改（modified）状态，表明文件已进行了修改，但还没有提交保存；通过命令`git add`  将其添加到暂存区，文件是已暂存（staged）状态，表示把已修改的文件放到下次提交时要保存的清单中；通过命令`git commit`将文件放入本地仓库，文件为已提交（commited）状态，表示该文件已经被安全地保存在本地数据库中，到这一步可以说是成功生成了一个新的版本。
远程仓库用来将本地仓库上传到网络，实现备份、共享和合作。我们选用开源中国的[码云Git@OSC](https://git.oschina.net/)作为代码托管平台。
### 3.安装Git
- 到Git官网https://www.git-scm.com/ 下载Git客户端
- 安装时选择默认即可。
- 安装完成后在桌面的快捷菜单中选择`Git Bash Here` 或者在开始菜单中选择`Git Bash`：
- 在Git Bash中执行命令`git --version`查看版本，证明Git安装成功
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170206222949166-1529291759.jpg)

### 4.Git和码云的关联
**4.1 在码云注册账号**
登录[码云 http://git.oschina.net/](http://git.oschina.net/)注册账号

**4.2 配置Git**
因为Git是分布式版本控制系统，必须在Git中配置本机的用户名和Email地址

- 执行命令`git config --global user.nam "你的用户名"`，告诉git你的名字，这个用户名会出现在提交记录中
- 执行命令`git config --global user.email "你的邮箱"`,告诉git你的邮箱， 这个邮箱也会出现在提交记录中，注意Email尽量保持和你注册码云的Email一致。

**4.3 创建SSH Key**

因为你的数据保存在远程服务器，服务器需要对你的身份进行识别，SSH key可以让你的电脑和码云Git@OSC之间建立安全的加密连接。
运行命令`ssh-keygen -t rsa -C "你的邮箱"`,如果已经有SSH key，会提示是否覆盖。然后会有三次提示输入，直接回车即可。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208143129041-1536083178.png)

在用户目录下找到 `.ssh`目录，里面有一个`id_rsa.pub`文件，保存的就是公钥。
登录码云，在SSH公钥文本框里粘贴`id_rsa.pub`文件的内容：
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208143926776-394084493.png)

执行命令`ssh -T git@git.oschina.net`,若返回`Welcome to Git@OSC`，则证明添加成功。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208144945885-1051669930.png)

**4.4 创建远程仓库**

- 在码云中新建项目
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208145621557-1560987594.png)

- 创建项目，输入项目名，选择项目语言。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208145915932-1930080694.png)

- 复制远程项目仓库的地址：选择HTTPS，可以复制远程项目仓库的HTTPS地址,如`https://git.oschina.net/maoth/java-hebau.git`  ，选择SSH，则复制远程仓库的SSH地址。如`git@git.oschina.net:maoth/java-hebau.git`
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208150715916-94746884.png)

**4.5 克隆远程仓库**
如果从零开发，最好的方式是先创建远程仓库，然后克隆远程仓库。
建立目录，如`E:\java`，进入目录后，右击鼠标选择`Git Bash Here`打开命令窗口，执行命令 `git clone <版本库的地址>`，版本库的地址可以是`HTTPS`地址,也可以是`SSH`地址。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208174006885-1970109345.png)

可以看到，远程仓库的项目已经下载到了本地。下面就可以在本地仓库编写代码了。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208174325838-985314028.png)
### 5.本地Git的使用
**5.1 初始化仓库（Git init）**
如果没有克隆远程仓库，需要创建一个新的Git代码库。
运行`git init`命令初始化仓库，将会创建一个`.git`文件夹，这个文件夹是Git来跟踪管理版本库。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207182613494-2054290099.png)

**5.2 添加文件（Git add）**
在当前文件夹下创建一个HelloWorld.java程序
使用`git status`命令可以查看当前仓库状态
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207184948182-1496324448.png)

提示说明有未跟踪（untracked）的文件，可以使用`git add <file>`加进去，通常我们使用`git add -A`命令，将所有相关文件存放到暂存区，此时git就可以跟踪该文件了。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207190213369-997685325.png)

再次使用`git status`命令可以看到发生的变化，提示`changes to be committed`说明可以进行提交了。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207184953510-1221420882.png)

**5.3 提交（Git commit）**
使用`git commit -m "提交信息"` 命令将暂存区的所有文件提交到本地仓库，提交时要求写上提交信息，注意双引号必须是英文半角的。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207210315604-1549601397.png)

提交后用`git log`查看提交记录
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207210323729-2095206302.png)

**5.4 文件修改和撤销**
对文件进行修改后，通过`git status`查看，显示一个文件进行了修改：
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207222022401-1347988833.png)

可以执行`git diff`查看文件做了哪些修改：
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207224233729-1189793129.png)
红色字体表示是删除的内容，绿色字体表示是添加的内容。

如果想撤销这些修改，执行`git checkout 文件名`
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207225555947-273678186.png)

文件修改后，进行add和commit就行了。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207230129776-1879819288.png)

文件add后尚未commit时，可以通过命令`git reset HEAD 文件名`进行撤销
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207231304119-231736864.png)

文件commit后，用`git log`查看提交记录，现在已经有三个提交的版本了。每个版本都有一行黄色`commit`开头的哈希字串，这是每个提交的唯一ID。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207231107291-2119432092.png)

文件已经提交后，如果想撤销，则执行`git reset --hard 9316bda`(取哈希字串的前7位即可），提示HEAD已经指向9316bda了，用`git log`查看，本地仓库已经回退到第一个版本了。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170207222016463-247928899.png)

**5.5 中文乱码问题**

- 关于`git status`中文乱码问题，可以执行`git config --global core.quotepath false`命令。
- 针对windows平台的乱码问题，一劳永逸的办法当然是采用全英文了。
###6. 远程同步
**6.1 推送本地仓库的更新到远程仓库**

- 文件提交到本地仓库后，可以执行`git push origin master`将本地仓库上传到远程仓库，默认情况下，`origin`指的是本地仓库在远程仓库的版本，`master`指的是本地仓库的master分支。第一次push的时候，可能需要输入在码云注册的用户名和密码。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208181830338-1003625507.png)

- 查看码云项目页面，文件HelloWorld.java已经上传到服务器。
![](http://images2015.cnblogs.com/blog/1028015/201702/1028015-20170208182130557-1134859895.png)

**6.2 抓取远程仓库的更新到本地仓库**
执行`git clone`命令后，自动创建了本地的`master`分支，用于跟踪远程仓库中的`origin/master`分支。当远程仓库的内容更新后，可以通过命令`git pull`或者 `git pull origin master`, 将更新的数据抓取到本地仓库，合并到工作目录的当前分支。
执行`git pull`命令时，本地做的提交和服务器上的提交可能有差异，导致合并冲突，此时，需进行冲突处理。具体参考[如何处理代码冲突](http://git.mydoc.io/?t=154702)

上面只是简单介绍了Git的使用，更为系统的学习可参考下列资料：

- [ProGit（中文版）](http://git.oschina.net/progit)
- [Git教程]( http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
- [常用Git命令清单]( http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
- [Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)
- [猴子都能懂的GIT入门](http://backlogtool.com/git-guide/cn/Git和Github)
