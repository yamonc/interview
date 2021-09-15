#git指令-管理修改
git管理的不是文件，而是修改
eg：对readme.txt文件进行修改一行
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190809231026600-1858964846.png)
在最后追加一句`git has to tracked`
然后添加，并且查看状态
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190809231133617-1128226804.png)


cat +文件名称 表示查看文件的内容

![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190809231259550-576522740.png)
最后提交成功
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190809231355250-1985520513.png)
成功后查看状态
		
		$ git status
		On branch master
		Changes not staged for commit:
		  (use "git add <file>..." to update what will be committed)
		  (use "git checkout -- <file>..." to discard changes in working directory)
		
			modified:   readme.txt
		
		no changes added to commit (use "git add" and/or "git commit -a")

用git diff HEAD -- readme.txt命令可以查看工作区和版本库里面最新版本的区别：



总结

两次修改后可以采取的指令
那怎么提交第二次修改呢？你可以继续git add再git commit，也可以别着急提交第一次修改，先git add第二次修改，再git commit，就相当于把两次修改合并后一块提交了：

第一次修改 -> git add -> 第二次修改 -> git add -> git commit