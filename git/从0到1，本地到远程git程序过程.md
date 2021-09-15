# 从0到1，本地到远程git程序过程

1. 切记一定要在需要提交代码的文件夹下`git init`，既是你使用了什么
tortoisegit什么工具，或者你在idea环境下已经add了，但是仍然需要你在当前文件夹下右击`git bash here`，前提是你不需要idea来给你提交到仓库或者，idea的仓库需要更换，不适用了。
2. 当`git init` 后，可以发现文件夹下出现了`.git`的隐藏文件，然后可以`git status`查看当前文件夹的内容：
 ![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190816230857144-297811369.png)
3. 查看完成后，如果需要提交单个或者多个文件时候使用`git add 文件1，文件2，。。。`但是如果需要提交的是全部的文件，直接`git add .`提交全部文件，如图：
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190816231100171-2028205781.png)
4. 提交之后，应该commit到本地仓库：git commit -m 备注信息，注意需要用引号引起来。如图：
 ![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190816231220885-154467499.png)
5. 添加到本地仓库后，需要和远程仓库进行关联：建议使用ssh来进行提交
 git remote add origin 你的ssh名称，直接copy过来即可。
 ![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190816231437557-2052103013.png)

6. 关联远程仓库后，使用push到远程仓库：这个只是第一次的时候需要添加u的信息

    git push -u orgin master
![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190816231534444-1204943083.png)
7. 如果后续有文件需要提交到该远程库中，可以 直接使用指令：

	git push origin master

-----
## Summary
​	git init：初始化仓库
​	git add README.md：添加文件
​	git commit -m "first commit" 添加文件到本地仓库
​	git remote add origin git@github.com:mlh1421/baidu-mission.git ： 关联远程仓库
​	git push -u origin master 第一次提交到远程仓库的文件
​	git push origin master:第二次+n提交到远程仓库的文件

