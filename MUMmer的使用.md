# MUMmer的使用

## 原始数据的处理

准备两个物种的染色体水平的基因组数据：fasta格式

如图所示

![](https://s2.ax1x.com/2020/01/14/lbG7hF.png)

## MUMmer的运行

创建一个文件夹，在这个文件夹下运行指令如下

```bash
nucmer --mum -c 90 -l 40 -p A2-At  ../A2_genome.fasta ../At_genome.fasta
```

其中 A2基因组为reference At基因组为query file 

```bash
--mum, --mumreference(默认), --maxmatch
--mincluster/-c: 用于聚类的匹配最低长度，默认65
--minmatch/-l: 单个匹配最小长度
-p：后加生成文件的前缀
```

运行速度较慢 得到文件的后缀是 .delta

## 坐标文件的获得

在之前的文件夹下运行如下指令

```bash
show-coords ./A2-At.delta
```

主要参数解释如下

```bash
-q：	Sort output lines by query
-r：	Sort output lines by reference
-l：	Include sequence length columns in the output
-T：	Switch output to tab-delimited format
```

得到的文件大致如下

![](https://s2.ax1x.com/2020/01/14/lbGTtU.png)

其中，1、2列是reference的对应染色体的起始与终止，3、4列是query对应的染色体的起始与终止、5、6列分别是前四列对应的片段长度、6、7列是对应的染色体长度、8、9列是对应的染色体编号