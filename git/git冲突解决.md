#git-分支管理-解决冲突
	
	1. 创建git分支：git checkout -b feature1
	2. 修改readme.txt最后一行，改为：
			Creating a new branch is quick AND simple.
	3. 在feature1分支上提交：
		git add readme.txt

		$ git commit -m "AND simple"
		[feature1 14096d0] AND simple
		 1 file changed, 1 insertion(+), 1 deletion(-)
	4. 切换到master分支
		git checkout master
		$ git checkout master
		Switched to branch 'master'
		Your branch is ahead of 'origin/master' by 1 commit.
		  (use "git push" to publish your local commits)
	5. 在master分支上把readme.txt文件的最后一行改为：

			Creating a new branch is quick & simple.
	6. 在master分支上提交：
	    $ git add readme.txt 
		$ git commit -m "& simple"
		[master 5dc6824] & simple
		 1 file changed, 1 insertion(+), 1 deletion(-)
	7. 现在，master分支和feature1分支各自都分别有新的提交，变成了这样：

![](https://img2018.cnblogs.com/blog/1080016/201909/1080016-20190904180554090-45107160.png)
	这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，我们试试看：
	
	$ git merge feature1
	Auto-merging readme.txt
	CONFLICT (content): Merge conflict in readme.txt
	Automatic merge failed; fix conflicts and then commit the result.
	果然冲突了！Git告诉我们，readme.txt文件存在冲突，必须手动解决冲突后再提交。git status也可以告诉我们冲突的文件：
	
	$ git status
	On branch master
	Your branch is ahead of 'origin/master' by 2 commits.
	  (use "git push" to publish your local commits)
	
	You have unmerged paths.
	  (fix conflicts and run "git commit")
	  (use "git merge --abort" to abort the merge)
	
	Unmerged paths:
	  (use "git add <file>..." to mark resolution)
	
		both modified:   readme.txt
	
	no changes added to commit (use "git add" and/or "git commit -a")
	我们可以直接查看readme.txt的内容：
	
	Git is a distributed version control system.
	Git is free software distributed under the GPL.
	Git has a mutable index called stage.
	Git tracks changes of files.
	<<<<<<< HEAD
	Creating a new branch is quick & simple.
	=======
	Creating a new branch is quick AND simple.
	>>>>>>> feature1
	Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，我们修改如下后保存：
	
	Creating a new branch is quick and simple.
	再提交：
	
	$ git add readme.txt 
	$ git commit -m "conflict fixed"
	[master cf810e4] conflict fixed
	现在，master分支和feature1分支变成了下图所示：
![](https://img2018.cnblogs.com/blog/1080016/201909/1080016-20190904180704501-1347887715.png)
	删除分支：git branch -d feature1

		英文状态下按Q可以退出git log