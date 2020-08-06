# Git系列（一）——Git的简介及安装

众所周知（跟计算机打交道多的），Git是一个很先进的版本控制系统，但其实不瞒你们知道，它是目前世界上最先进的分布式版本控制系统（没有之一）。所以说，还在等着什么，快跟着小编一起来轻松愉快的学习Git吧。

------

## Git的简介

相信大家在实际工作生活中都会使用word进行长篇大论文章的书写，尤其时当很多人一起进行文档的书写时，一定避免不了的进行文档的修改，这时候你一定会抱怨为什么会有这么多中间版本，接收文件时如果命名重复的话就会出现是否覆盖的选项，这个时候如果Git出现在你的世界，你一定会毫不留情地爱上它的。

Git采用的是分布式控制管理系统（下图），与其相对应的是集中式版本控制系统（上图），分布式控制管理系统没有真正意义上的“中央服务器”，每个人的电脑都有完整的版本库，所以说除非所有的电脑同时坏掉，不然就不用担心数据会丢失这样的数据安全问题。当你和同事同时修改了文件A，那么你们只需要进行文件A修改的交换，就可以看到对方的修改了，但一般都会有一台电脑充当“中央服务器”，这是为了方便交换大家的修改。没有它大家也可以进行正常的工作，只是交换修改不方便而已。

<img src="https://s1.ax1x.com/2020/07/28/aVizqg.png" alt="1.png" style="zoom:50%;" />

<img src="https://s1.ax1x.com/2020/07/28/aVixsS.png" alt="2.png" style="zoom:50%;" />

## Git的安装

Git的安装很简单，这里主要介绍linux和windows上安装Git

### 在linux上安装Git

你可以在系统中输入`git`，看看系统中有没有安装git，

```
$ git
The program 'git' is currently not installed. You can install it by typing:
sudo apt-get install git
```

如果没有安装git，linux会贴心的提示你安装的指令`sudo apt-get install git`

或者也可以通过源码进行安装，源码压缩包[网站](https://mirrors.edge.kernel.org/pub/software/scm/git/)，解压之后，依次输入`./config`，`make`，`sudo make install`这几个命令之后

### 在Windows上安装Git

直接从Git官网上直接下载并按照默认选项安装即可。安装成功之后，在开始菜单Git文件夹中找到Git Bash，打开以后是一个命令行窗口，那就说明安装成功了。

![3.png](https://s1.ax1x.com/2020/07/28/aVivM8.png)

<img src="https://s1.ax1x.com/2020/07/28/aViXxf.png" alt="4.png" style="zoom:67%;" />

安装成功之后，我们需要简单配置一下

```bash
$ git config --global user.name	#Your Name
$ git config --global user.email	#email@example.com
```

`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址，命令如下

```bash
$ git config  user.name
$ git config  user.email
$ git config --list #查看当前配置
```

------

到此为止，我们的Git已经安装并简单配置完毕，下一个教程我们会介绍创建版本库以及简单的上传文件操作，敬请期待！

+ 参考 [廖雪峰的官方网站](https://www.liaoxuefeng.com/)

