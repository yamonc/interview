#git指令
	1. 
		$ git config --global user.name "Your Name"
		$ git config --global user.email "email@example.com"
	注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。
	2. 初始化本地仓库：
		通过git init命令把这个目录变成Git可以管理的仓库：
		这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190801224337990-1172104779.png)

![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190801224254895-761601538.png)

	3. 把一个文件放到Git仓库只需要两步：
		1. 第一步，用命令git add告诉Git，把文件添加到仓库：
			* $ git add readme.txt
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190801224850919-1401483403.png)

		2. 第二步，用命令git commit告诉Git，把文件提交到仓库：
			* $ git commit -m "wrote a readme file"	
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190801225012304-1878541260.png)

			*简单解释一下git commit命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。
			*git commit命令执行成功后会告诉你，1 file changed：1个文件被改动（我们新添加的readme.txt文件）；2 insertions：插入了两行内容（readme.txt有两行内容）。
			使用git add可以添加多个文件，都一次性提交上去。
		例如：
		$ git add file1.txt
		$ git add file2.txt file3.txt
		$ git commit -m "add 3 files."
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190801225521478-2136765787.png)
	4. 修改内容的后续
	
之前：![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190805215334880-777479391.png)
修改内容后：
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190805215351207-1487426721.png)
然后点击保存，![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190805215427952-1728231770.png)
右击gitbashhere
输入指令：`git status`
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190805215554053-245579438.png)
`git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，readme.txt被修改过了，但还没有准备提交的修改

	虽然Git告诉我们readme.txt被修改了，但如果能看看具体修改了什么内容，自然是很好的。比如你休假两周从国外回来，第一天上班时，已经记不清上次怎么修改的readme.txt，所以，需要用git diff这个命令看看
	*使用git diff指令来查看具体修改的内容
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190805215830799-1406618765.png)

	然后提交到仓库：
		*git add 添加文件
		*在执行第二步git commit之前，我们再运行git status看看当前仓库的状态：
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190805220214265-2018904224.png)
		git status告诉我们，将要被提交的修改包括readme.txt，下一步，就可以放心地提交了：
		*git commit 提交到仓库 
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190805220315241-117548003.png)
  ---》版本回退