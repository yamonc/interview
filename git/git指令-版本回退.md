#git指令-版本回退
##回顾：
	1. 修改文件
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190806213643122-987978816.png)
		
	2. 添加到暂存区并提交
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190806213810261-256990425.png)
回顾对readme共三次修改：

	1. 版本1：wrote a readme file
			Git is a version control system.
			Git is free software.
	2. 版本2：add distributed
			Git is a distributed version control system.
			Git is free software.
	3. 版本3：append GPL
			modify some words in there；
	关于这些历史记录，版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用git log命令查看：
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190806214143753-1534384931.png)

	git log命令显示从最近到最远的提交日志，我们可以看到3次提交，
	最近的一次是append GPL，上一次是add distributed，最早的一
	次是wrote a readme file。

	如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--
	pretty=oneline参数：
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190807215054681-155214417.png)

	35c67bf55ee2044606114d77b3821e5d92945175是commit id（版本号）并且为十六进制的数
    4. 回退到上个版本
		如果想回退到上个版本也就是 add distributed 则git必须知道当前版本是哪个，在git中，使用head来表示当前版本，也就是最新提交的	85bbf3b631777af6da983ef93cd0253a7aaf368a
		上一个版本就是head^ 
		上上个版本head^^
		……
		往上100可以写head~100
		现在回退上个版本使用的命令 git reset
		git reset --hard head^  回退上一个版本。没错你没看错，是两个-，如果写成一个报错：
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190807215749898-2125687589.png)	
	
		正确后为
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190807215820181-1643103264.png)
		使用git命令查看readme.txt内容：cat readme.txt
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190807215922187-950700654.png)
		使用git log 查看日志，可以发现append gpl版本丢失。如果想找到，只要窗口没有关闭，可以查看commit的版本号是多少然后指定git回退的版本
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190807220228445-1521710793.png)
		具体文件的结构如图
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190807220302233-1556466439.png)
		git reflog用来记录你的每一次命令

---------
*summary：*
	1. HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
	2. 用git log可以查看提交历史，以便确定要回退到哪个版本
	3. 用git reflog查看命令历史，以便确定要回到未来的哪个版本