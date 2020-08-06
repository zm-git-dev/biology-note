# Git系列（三）——文件的修改及提交

文件的创建以及上传我们已经搞定了，现在我们需要对我们的文档进行修改，这些该如何进行呢，我么这就来学习一下。

------

## 文件的修改及查看

我们先给我们的`tmp.txt`文件加上一句话

```
I like Git
Because it is useful
```

保存之后，我们就可以使用`git status`命令查看修改结果：

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

结果很多，但其实就一句话最有用，就是`#	modified:   tmp.txt`它告诉我们`tmp.txt`文件被修改过了，最后一句话告诉我们修改还没有进行提交。

但是如果第一天的修改，我们第二天忘记了，不用慌，我们可以使用`git diff`指令查看具体的修改内容：

```
$ git diff
diff --git a/tmp.txt b/tmp.txt
index ad2ec82..1137052 100644
--- a/tmp.txt
+++ b/tmp.txt
@@ -1 +1,2 @@
 I like Git
+Because it is useful
```

我们就可以从结果看出，我们加了一行`Because it is useful`

## 文件修改的查看

还是之前的两步：

```
$ git add tmp.txt
```

之后，我们可以再使用`git status`来查看当前仓库的状态

```
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#	modified:   tmp.txt
#
```

git告诉我们，被提交的修改中包括`tmp.txt`文件，这时候我们就可以安心的提交了：

```
$ git commit -m "add Because it is useful"
[master 2c2ad62] add Because it is useful
 1 file changed, 1 insertion(+)
```

成功之后，我们可以再来看当前仓库的状态

```
$ git status
# On branch master
nothing to commit, working directory clean
```

------

git告诉我们，当前目录是干净的，没有文件被修改过，到此，我们就进行了文件的修改工作，这其实还没有展现出git的强大之处，下次我们会学习版本的回溯，准备好“时光穿梭机”一起感受git的强大之处吧！

+ 参考 [廖雪峰的官方网站](https://www.liaoxuefeng.com/)