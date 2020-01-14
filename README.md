# Identification of PAVs（present/absence variations)

------

## 原始数据的处理

准备两个物种的染色体水平的基因组数据：fasta格式

如图所示

![lbG7hF.png](https://s2.ax1x.com/2020/01/14/lbG7hF.png)

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

![lbGTtU.png](https://s2.ax1x.com/2020/01/14/lbGTtU.png)

其中，1、2列是reference的对应染色体的起始与终止，3、4列是query对应的染色体的起始与终止、5、6列分别是前四列对应的片段长度、6、7列是对应的染色体长度、8、9列是对应的染色体编号

**注意**3、4列起始终止可能是倒过来的，这很正常，而且这很有用！

## PAVs的寻找和筛选

PAVs主要是分为两个方面，下面我一个一个进行阐述

### A2到At过程中，A2基因组上absence的片段

首先，这个的寻找需要我们在运行MUMmer过程中，将At作为参考基因组去运行，最后得到坐标的位置的时候我们要以A2基因组的顺序进行排列，得到的坐标文件如图所示

[![lbJvvj.png](https://s2.ax1x.com/2020/01/14/lbJvvj.png)](https://imgchr.com/i/lbJvvj)

我们需要每一条染色体的去寻找，下面我们就以A2上的一号染色体为例去说明

首先我们先找出我们需要的一号染色体对比上的片段对应的坐标，并且把坐标统一变成正向的，运行如下指令

```bash
awk '/.*Ghir_A.*Chr01/{print "Chr01" "\t" $4 "\t" $5}' At_A2.coords |awk '{if($3<$2){print $1 "\t" $3 "\t" $2} else {print $0}}' >tmp.bed
```

然后我们用bedtools对这些有gap的区间进行合并（取并集）

```bash
sort -k2 -n tmp.bed >tmpsort.bed ：对得到的区间以区间的左端进行排序
bedtools merge -i  tmpsort.bed >match.bed : 将区间进行合并
```

得到的文件如下图所示

[![lb0D7d.png](https://s2.ax1x.com/2020/01/14/lb0D7d.png)](https://imgchr.com/i/lb0D7d)

最后我们就可以用awk指令将**第n个区间的终止当作第n个区间的起始，第n+1个区间的起始当作第n个区间的终止**（这句话要好好理解下），就得到了A2在进化过程中PAVs的片段了，指令如下：

```bash
awk '{a[NR][1]=$1;a[NR][2]=$2;a[NR][3]=$3;}END{for(i=1;i<=NR-1;i++){print a[i][1]"\t"a[i][3]"\t"a[i+1][2]}}' match.bed >PAVs.bed
```

结果如图所示

[![lbDQo9.png](https://s2.ax1x.com/2020/01/14/lbDQo9.png)](https://imgchr.com/i/lbDQo9)

后续我们可以进行筛选统计

```bash
awk '{a=$3-$2}{if(a>100){print $0}}' PAVs.bed ： 筛选小于100bp的片段
awk '{a=$3-$2}{if(a>100){print a}}' PAVs.bed |awk '{sum+=$1} END {print sum}'|cat ：统计A2一号染色体上PAVs总长度
```

### A2到At过程中，At基因组上present的片段

这个的分析和之前的一样，只是要在一开始运行MUMmer的时候把A2当作参考基因组，按照之前的步骤进行之后，就可以得到At一号染色体present的片段了。