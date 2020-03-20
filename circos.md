# circos作图基础

## circos的简介

众所周知，生物信息学是一个以庞大的数据量为基础的学科，这也就是使得结果的可视化变得有些困难，随着生物信息的发展，其辅助分析工具也迅速增多，Martin Krzywinski等人开发的Circos就在一定程度上解决了可视化困难这个问题。Circos是一个可视化数据和信息的软件包。它以圆形布局可视化数据——这使得Circos非常适合探索对象或位置之间的关系。它是创建高质量信息图表和插图的理想工具，具有丰富的分层数据和令人愉快的对称性。你可以根据你的受众调整其焦点和细节并且可以很好地控制图中的每个元素，此外，其具备高度自动化的特点，是由纯文本配置文件控制，十分的方便快捷。

## circos的安装

首先在[这里](http://circos.ca/software/download/circos/)下载到最新的Circos软件包，这里主要介绍linux版本，Circos本身是基于Perl的，所以不需要编译安装。解压下载的软件包到你想要存放的位置，并将circos/bin加入环境变量即可。它依赖较多的Perl模块，可以用`circos -module`查看相关Perl模块的安装情况。如果全部安装完毕，结果如图所示

![](C:\Users\DELL\Desktop\公众号\1.PNG)

如果安装模块儿出现问题，别着急，之后会有文章详细介绍模块儿的安装哦

## 样本的运行测试

Circos包里有自己的demo，可通过`circos -conf yourpath/circos/example/etc/circos.conf`来运行，如果你成功的在命令行当前目录下生成png和svg两个图形文件。如下图所示，那么恭喜你已经完成了第一步并且得到了一个十分漂亮的图。

![](C:\Users\DELL\Desktop\公众号\circos.png)

## 数据文件格式

circos主要有四个模块儿需要输入数据文件，包括karyotype以及highlight、plot、link，其格式都相对简单，编辑起来也不麻烦。

我们来看karyotype这个数据文件，这个文件记载了染色体的相关信息，其基本数据格式如下

```
chr - ID LABEL START END COLOR	
chr - D5Chr01 D501 0 55868233 rgb1
chr - D5Chr02 D502 0 62769430 rgb2
chr - D5Chr03 D503 0 45765648 rgb3
chr - D5Chr04 D504 0 62178258 rgb4
...
```

这里第一列和第二列一般都是固定的

ID是染色体的名称，一定是你课题中使用的染色体名称

LABEL是在图中显示的染色体名称

START、END尽量覆盖全长，其他的控制文件可以控制画出来的长度

最后一列的color可以随心所欲的自己控制

## 配置文件的书写

配置文件至少需要五个部分，

```
# Karyotypes
karyotype	= data/karyotype.human.txt,data/karyotype.mouse.txt,data/karyotype.rat.txt

# Ideograms
<ideogram>

<spacing>
default		= 0.002r
</spacing>

thickness	= 25p
fill		= yes
radius		= 0.80r

</ideogram>

# Image
<image>
<<include etc/image.conf>>
</image>

# RGB/HSV color definitions, color lists, location of fonts, fill patterns.
<<include etc/colors_fonts_patterns.conf>>

# Debugging, I/O and other system parameters
<<include etc/housekeeping.conf>>
```

首先我们对circos环图中最基本的元素进行设置，包括karyotype、ideogram、ticks等模块。

### Karyotype

