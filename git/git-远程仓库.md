git指令-添加远程仓库

1. 首先在GitHub上创建属于你自己的远程仓库：例如我创建的远程仓库`mybatis`用于我最近保存的mybatis代码
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190811230113645-575699594.png)
目前，在GitHub上的这个learngit仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。<br>

2. 把内容推送到远程仓库：前提是你已经add文件并且将暂存区的文件commit到本地仓库之后才可以使用该指令`git push -u origin master`<br>
该指令的大概意思是：把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。<br>
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
3. 到现在为止，已经完成从零到一的git操作。所以以后如果这些文件或者这么说，本地仓库有了新的添加之后，使用git status查看操作后，重新add并且commit到本地仓库后，不用再重复上述步骤。直接`git push origin master`把本地master分支的最新修改推送至GitHub。


ps：

![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190816225959444-1817965113.png)
在创建远程仓库的时候就有两个选项，一个是ssh一个是https，使用https的时候更安全，具体是使用的时候每次提交需要输入用户名和密码才可以提交上去，但是ssh由于是本地存储秘钥，直接诶略过这步。

当你第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：

	The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
	RSA key fingerprint is xx.xx.xx.xx.xx.
	Are you sure you want to continue connecting (yes/no)?
这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。

Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

	Warning: Permanently added 'github.com' (RSA) to the list of known hosts.

##summary：
要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；

关联后，使用命令git push -u origin master第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！
