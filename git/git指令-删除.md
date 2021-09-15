#git指令-删除
添加一个新文件test.txt到Git并且提交：

	$ git add test.txt
	$ git commit -m "add test.txt"
	[master e793d0b] add test.txt
	 1 file changed, 0 insertions(+), 0 deletions(-)
	 create mode 100644 test.txt
一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用rm命令删了：`$ rm test.txt`

这个时候，Git知道你删除了文件，因此，工作区和版本库就不一致了，git status命令会立刻告诉你哪些文件被删除了：![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190809233159274-2124028381.png)
现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit：![](https://img2018.cnblogs.com/blog/1080016/201908/1080016-20190809233251064-168239818.png)
ps:先手动删除文件，然后使用git rm <file>和git add<file>效果是一样的。

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
    $ git checkout -- test.txt

git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

从来没有被添加到版本库就被删除的文件，是无法恢复的！

命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。