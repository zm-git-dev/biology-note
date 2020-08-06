# Git系列（五）——撤销修改

在Git中“后悔药”可是有好多种的，今天我们就来看看另外的几种“后悔药”

------

在我们的使用Git的过程中，可能会有三种情况出现：1、文件修改之后，突然发现自己的修改没有意义，想把修改删去 2、`git add`之后，突然发现自己的修改没有意义 3、`git commit`之后，突然发现自己的修改没有意义，这时候大家可能最想做的就是把修改给撤销了，这节课，我们就来学习一下这三种情况下如何撤销我们的修改。

## 撤销工作区的修改

假设在学习Git的过程中我们想放弃了，所以我们在我们的`tmp.txt`文件之后加上了一行：

```
I like Git
Because it is useful
I will use it
I will give up
```

一觉醒来，我们突然又觉得自己不能前功尽弃，自己一定要坚持下去，我们用`git status`一看：

```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   tmp.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```

Git也在提示你使用`git checkout -- <file>...`去撤销工作区的修改，我们来试一试：

```
$ git checkout -- tmp.txt
```

再来看看`tmp.txt`文件：

```
$ cat tmp.txt 
I like Git
Because it is useful
I will use it
```

OK，工作区的修改确实消失了。

其实，`git checkout -- <file>...`这个指令就是让这个文件回到最近一次`git commit`或`git add`时的状态。如果修改在缓存区就让它回到工作区，如果在工作区就直接删除修改了，很像是word里边的撤销按钮。

## 撤销缓存区的修改

我们再次回到刚刚的场景，假设，我们跟刚才一样的修改了`tmp.txt`文件，这次我们用`git add`之后，我们该如何撤销呢，我们先来用`git status`看看状态：

```
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#	modified:   tmp.txt
#
```

同样的，Git告诉我们可以使用`git reset HEAD <file>...`将暂存区的修改撤销掉（unstage），重新放回工作区：

```
$ git reset HEAD tmp.txt
Unstaged changes after reset:
M	tmp.txt
```

这个`git reset`既可以版本回退，也可以把暂存区的修改退回到工作区，`HEAD`指的是最新版本，然后我们再看一下状态：

```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   tmp.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```

确实，修改回到了工作区，这时我们再用第一个撤销工作区的指令：`git checkout -- tmp.txt`，用了之后，我们再来看一下：

```
$ git status
# On branch master
nothing to commit, working directory clean
```

OK，修改成功被我们撤销了。

## 撤销版本库的修改

如果成功的`git commit`之后，修改就上传到了版本库，这是我们如果想要撤销版本库的修改，请看上一个教程，这里就不再赘述了。

------

OK，本次教程就到这里结束了，下一节课，我们来讲一下如何删除文件。