# Git系列（六）——删除文件

Git中删除文件可不太一样哦，我们一起来学习一下

------

在Git中如果你想要删除文件，比平时我们删除文件要麻烦那么一点点，毕竟Git有两个地方：工作区、版本库，平时我们删除文件的方式，只是把工作区的文件给删除了，这时候版本库和工作区不一致，Git会自动追踪到这个修改，然后告诉你该怎么做，我们来举个例子：

如果大家按照之前的教程来学习的话，我们的`learngit`文件夹中还有一个`README.txt`文件，这时候，我们使用linux指令rm把它删掉之后，来看一下Git的状态

```
$ rm README.txt

$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add/rm <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	deleted:    README.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```

Git告诉我们使用`git add/rm <file>..`把修改提交到暂存区，然后除此之外我们还需要使用`git commit`将修改提交到版本库中：

```
$ git rm README.txt
rm 'README.txt'

$ git commit -m "remove README.txt"
[master 0f43972] remove README.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 README.txt
```

这样我们的文件就成功地删除了。

+ `git rm` 和 先`rm`再`git rm`这两种情况是一样的结果
+ 如果删错了文件，请参考（四）（五）教程恢复

------

Git的基本操作其实我们已经学的差不多了，下一个阶段我们就来学习一下神奇的远程仓库和神奇的Github。