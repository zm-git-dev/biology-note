# Git系列（四）——版本回退

我们已经学会了创建和修改文件，那也就是说我们已经生成了很多个版本，这样我们也就可以来学习版本的回退了。

------

## 工作区和暂存区

首先，我们来了解工作区和暂存区这两个概念：

+ 工作区就是我们能看到的目录，比如`learngit`这个目录，就是我们的工作区；
+ 暂存区就是我们通过`git add`这个指令，将修改提交到的地方，图中的`stage`；
+ 除此之外，`git commit`是把这些修改提交到我们自动创建的第一个分支`master`，还有一个指向`master`的指针`HEAD`（这两个概念我们之后再讲）

[![1.png](https://s1.ax1x.com/2020/08/02/ats6sJ.png)](https://imgchr.com/i/ats6sJ)

这样我们就可以理解为什么Git上传或者修改文件都需要两步，而且每次`commit`都是将暂存区的所有修改一起提交到分支，所以说，如果我们有很多个修改，一定要用`git add`指令全部添加到暂存区之后，再一起上传到分支。

我们来举一个例子：我们再在`learngit`目录下创建一个文件README.txt，内容如下：

```
I am learning git
```

并且把我们之前的`tmp.txt`文件再加一行变成：

```
I like Git
Because it is useful
I will use it
```

然后，我们使用`git status`来看看我们的修改：

```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   tmp.txt
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	README.txt
no changes added to commit (use "git add" and/or "git commit -a")
```

很明显，git定位到了我们的修改，我们可以看到`README.txt`的状态是`Untracked files`，这是因为我们还没有将这个文件上传，现在，我们使用`git add`对两个文件进行添加后，我们再用`git status`来看一下：

```
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#	new file:   README.txt
#	modified:   tmp.txt
#
```

在这个时候，这两个修改都在暂存区里，git就会提示我们使用`git commit`来进行上传，然后我们就用`git commit`将修改进行上传，这时候我们再使用`git status`查看一下：

```
$ git statua
# On branch master
nothing to commit, working directory clean
```

这时候，Git就告诉我们，整个世界都是干净的了!

我们可以两次`add`后再进行`commit`，但是我们不能`add`--`commit`--`add`，后者的第二次`add`的内容就不会被上传到分支。总之，我们在操作git时，最后一步一定是`commit`。

## 版本回退

版本回退这个功能就意味着，我们可以有无限的犯错机会，就像我们打单机游戏最后的boss之前，我们都会先存档，这样我们就算失败了也可以无限次的从头再来，git的版本回退功能也是一样的道理。

我们的工作目录`learngit`已经经历了很多次修改，也产生了很多个版本，我们想要进行版本回退，我们首先需要知道都有那些版本，我们可以用`git log`这个指令

```
$ git log
commit 5b8c051f43c21483c100aafdf0362899d2168fb9
Author: yxlong <1570447120@qq.com>
Date:   Sat Aug 1 16:12:25 2020 +0800

    add a new file and a new line : I will use it

commit 2c2ad624b48cc59d345206e038de9c3b9e0140bc
Author: yxlong <1570447120@qq.com>
Date:   Tue Jul 28 21:34:54 2020 +0800

    add Because it is useful

commit 90b59be891df1420bc9d243f6da6681cdde4fb60
Author: yxlong <1570447120@qq.com>
Date:   Tue Jul 28 21:23:12 2020 +0800

    write a tmp file
```

我们可以清晰的看到有三次提交，如果觉得信息太多看的眼花缭乱的话，我们可以加上`--pretty=oneline`参数：

```
$ git log --pretty=oneline
5b8c051f43c21483c100aafdf0362899d2168fb9 add a new file and a new line : I will use it
2c2ad624b48cc59d345206e038de9c3b9e0140bc add Because it is useful
90b59be891df1420bc9d243f6da6681cdde4fb60 write a tmp file
```

前面的这一大串数字是`commit id`（版本号），是每个版本的唯一标识。

现在我们就坐上时光穿梭机把`tmp.txt`回退到上一个版本吧，首先我们需要知道，`HEAD`表示当前版本，也就是最新的`5b8c051...`，上一个版本就是`HEAD^`，上上个版本就是`HEAD^^`，100个版本之前就是`HEAD~100`。

我们如果想回退到上一个版本，我们就需要使用`git reset`命令：

```
$ git reset --hard HEAD^
HEAD is now at 2c2ad62 add Because it is useful
```

`--hard`参数的含义我们之后再讲，这里直接用就可以了，我们来看一下`tmp.txt`的内容：

```
$ cat tmp.txt
I like Git
Because it is useful
```

果然，被还原了，而且最新添加的`README.txt`文件也消失了。

现在我们来看一下我们现有的版本信息：

```
$ git log
commit 2c2ad624b48cc59d345206e038de9c3b9e0140bc
Author: yxlong <1570447120@qq.com>
Date:   Tue Jul 28 21:34:54 2020 +0800

    add Because it is useful

commit 90b59be891df1420bc9d243f6da6681cdde4fb60
Author: yxlong <1570447120@qq.com>
Date:   Tue Jul 28 21:23:12 2020 +0800

    write a tmp file
```

Oh my God!最后的那个版本消失了，我们真的穿越了，那可以传回去嘛？答案是肯定的，我们只需要向上翻一番记录找到最新版本的`commit id`，然后使用如下指令：

```
$ git reset --hard 5b8c0
HEAD is now at 5b8c051 add a new file and a new line : I will use it
```

这样就变回去了，最后那个`commit id`不用写全，写前几位就可以，Git会自动为你寻找的。

我们来看一下`tmp.txt`的内容：

```
$ cat tmp.txt 
I like Git
Because it is useful
I will use it
```

嗯，没错，它又回来了。

这时候，可能就会有朋友问，如果我忘记了`commit id`怎么办呢？放心，Git很强大的，他会提前想到所有的情况的，`git reflog`这个命令用来记录你的每一次命令，如下：

```
$ git reflog
5b8c051 HEAD@{0}: reset: moving to 5b8c0
2c2ad62 HEAD@{1}: reset: moving to HEAD^
5b8c051 HEAD@{2}: commit: add a new file and a new line : I will use it
2c2ad62 HEAD@{3}: commit: add Because it is useful
90b59be HEAD@{4}: commit (initial): write a tmp file
```

Oh Nice! 我又可以知道我的`commit id`了。

------

本次教程就到此结束了，本文知识点较多，大家要多复习呦。