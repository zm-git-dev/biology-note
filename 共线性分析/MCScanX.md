---
title: MCScanX共线性分析
comments: true
author: yxlong
date: 2020-03-24 16:55:33

---

## MCScanX的简介

MCScanX：Multiple Collinearity Scan toolkit是一种算法，扫描多个基因组或亚基因组，以识别假定的同源染色体区域，然后使用基因定位这些区域。它的速度很快，操作简单，便于使用，是我们在进行基因共线性分析时的一个利器。

今天我们就来学习一下如何使用这个利器来帮助我们分析生物学问题

<!--more-->

## MCScanX的下载与安装

下载软件

```bash
wget http://chibba.pgml.uga.edu/mcscan2/MCScanX.zip
```

解压

```bash
unzip MCScanX.zip
```

到安装目录之后，给

- msa.h
- dissect_multiple_alignment.h
- detect_collinear_tandem_arrays.h

这三个文件前面添加上`\#include <unistd.h>`，原因是软件不支持64位操作系统，然后make一下就安装完成了。

## 原始数据的准备

我们这里使用陆地棉TM-1的基因组数据作为原始数据，去探究棉花两个亚基因组之间的共线性。

首先下载数据

```bash
wget http://cotton.hzau.edu.cn/EN/data/download/Ghirsutum_genome_HAU_v1.1.tar.gz
```

然后解压

```bash
tar -zxvf Ghirsutum_genome_HAU_v1.1.tar.gz
```

我们需要`Ghirsutum_gene_model.gff3`、`Ghirsutum_gene_peptide.fasta`这两组数据，我们来看看两组数据长什么样

### gff文件

下面是`Ghirsutum_gene_model.gff3`文件

```
Ghir_A01        Ghir_EVM        gene    80323913        80324566        .       +       .       ID=Ghir_A01G013980;Name=Ghir_A01G013980
Ghir_A01        Ghir_EVM        mRNA    80323913        80324566        .       +       .       ID=Ghir_A01G013980.1;Parent=Ghir_A01G013980
Ghir_A01        Ghir_EVM        CDS     80324198        80324566        .       +       0       ID=cds.Ghir_A01G013980.1;Parent=Ghir_A01G013980.1

```

软件需要的gff文件格式为

```
sp#	 gene	starting_position	ending_position
```

所以我们可以使用linux指令中的awk去处理我们的文件，指令如下

```bash
awk -F "\t" '{a=substr($9,4,15)}$0~/gene/{print $1 "\t" a".1" "\t" $4 "\t" $5}' Ghirsutum_gene_model.gff3 >At-Dt.gff
```

因为我们的gff文件中的基因名最后是没有.1的，但是我们使用了每一个基因的第一个蛋白质序列，所以两个输入文件中的基因名不同，我们可以将gff文件中的基因名后面加上.1

### 蛋白质序列文件

然后就是`Ghirsutum_gene_peptide.fasta`文件

```
>Ghir_A01G000010.1
MLKQDGTLCSFSPCMEQVQRSCETLRSDFIEIYGPLKYCSACMKSVNGKWITRRSMMGIPLHALHTRGGRLQVKQVWGTM
QVLRESWLGHLLKLEGILDI
>Ghir_A01G000020.1
MNKIPYPDLTVDLRDKTITLVARLILLLIHAQHSMLRQSSSEIYDLGRKLGNSSFLGAWPDSIAGRWG
```

每个基因可以预测出多个蛋白质序列，我们这里同统一采用.1结尾的序列，并且我们需要比对的是A亚组和D亚组，所以我们就要把A亚组和D亚组的蛋白质序列数据分开，指令如下

```bash
awk 'NR==1&&$1~/^>/{print $0}NR>=2&&$1~/^>/{print "\n"$0}$1~/^[^>]/{printf $0}' Ghirsutum_gene_peptide.fasta |grep '^>Ghir_A' -A 1|grep '\.1$' -A 1 |sed '/--/d' >At.fasta
awk 'NR==1&&$1~/^>/{print $0}NR>=2&&$1~/^>/{print "\n"$0}$1~/^[^>]/{printf $0}' Ghirsutum_gene_peptide.fasta |grep '^>Ghir_D' -A 1|grep '\.1$' -A 1 |sed '/--/d' >Dt.fasta
```

### blast结果文件

#### 建库

```
makeblastdb -in At.fasta -dbtype prot -parse_seqids -out Atdb
```

这里选择两个亚基因组中的任意一个建库即可

#### 比对

```
blastp -query Dt.fasta -db Atdb -out At-Dt.blast -evalue 1e-10 -num_threads 30 -outfmt 6 -num_alignments 5 
```

参数解释如下

```
-query 被比对的物种的蛋白质文件
-db 上一步建的库
-out 输出结果
-evalue 设置输出结果的e-value值，值越小相似度越高。
-num_threads 比对所用的线程数
-outfmt 输出的格式
-num_alignments 输出比对上的序列的最大值条目数
```

这一步是限速步骤，会花费一些时间

运行结束后会得到一个名为`At-Dt.blast`的文件，这是我们软件需要的输入文件之一。

至此为止我们就得到了两个软件的输入文件`At-Dt.blast``At-Dt.gff`

注意事项

+ 如果比较多个基因组之间的共线性，我们要将这些物种的gff文件cat在一起

+ 两个输入文件里边的基因名必须一致，一定要仔细检查

## 软件的运行

将上一步生成的At-Dt.blast和At-Dt.gff放到新建的At-Dt文件夹内，然后进入文件夹里输入指令

```
MCScanX ./At-Dt
```

经过短暂的时间后，就会生成一个文件`At-Dt.collinearity`和一个文件夹`At-Dt.html`

At-Dt.collinearity里记录了共线性信息,可以清楚的看到共线性基因的数目比例以及具体的比对信息

## 下游分析

这里主要讲可视化分析，这里有四种图供我们选择

### dot_plotter

首先我们需要修改`MCScanX/downstream_analyses`里边的`dot.ctl`这个control文件，如下

```
800	//dimension (in pixels) of x axis
800	//dimension (in pixels) of y axis
Ghir_A01,Ghir_A02,Ghir_A03,Ghir_A04,Ghir_A05,Ghir_A06,Ghir_A07,Ghir_A08,Ghir_A09,Ghir_A10,Ghir_A11,Ghir_A12,Ghir_A13	//chromosomes in x axis
Ghir_D01,Ghir_D02,Ghir_D03,Ghir_D04,Ghir_D05,Ghir_D06,Ghir_D07,Ghir_D08,Ghir_D09,Ghir_D10,Ghir_D11,Ghir_D12,Ghir_D13	//chromosomes in y axis
```

主要修改的部分就是染色体名字，想画哪条染色体就把那一条染色体写进去就可以了，染色体名字要和gff文件中的染色体名字相同，然后运行代码

```
java dot_plotter 
-g At-Dt.gff 
-s At-Dt.collinearity 
-c dot.ctl 
-o At-Dtdot.PNG
```

+ -g后面是gff文件
+ -s是得到的后缀为collinearity的结果文件
+ -c是我们需要修改的control文件
+ -o是输出的文件名称

各个文件的路径要指定明确，之后会得到图片如下

<img src="https://s1.ax1x.com/2020/03/24/8qbv6K.png" alt="At-Dtdot.PNG" style="zoom: 50%;" />

如果有使用Xshell运行上述代码时提示需要下载Xmanager的小伙伴，可以参考主页文章[04vscode与服务端R交互]([https://biocottonhub.github.io/codeHub/2020/03/22/04vscode%E4%B8%8E%E6%9C%8D%E5%8A%A1%E7%AB%AFR%E4%BA%A4%E4%BA%92/](https://biocottonhub.github.io/codeHub/2020/03/22/04vscode与服务端R交互/))

### dual_synteny_plotter

dual_synteny.ctl文件

```
600     //plot width (in pixels)
800     //plot height (in pixels)
Ghir_A01      //chromosomes in the left column
Ghir_D01       //chromosomes in the right column
```

指令如下

```
java dual_synteny_plotter 
-g At-Dt.gff 
-s At-Dt.collinearity 
-c dual_synteny.ctl 
-o At-Dtdual.PNG
```

<img src="https://s1.ax1x.com/2020/03/24/8LPmu9.png" alt="At-Dtdual.PNG" style="zoom:50%;" />

### circle_plotter

circle.ctl文件

```
800	//plot width and height (in pixels)	
Ghir_A01,Ghir_A02,Ghir_A03,Ghir_D01,Ghir_D02,Ghir_D03	//chromosomes in the circle
```

指令如下

```
java circle_plotter 
-g At-Dt.gff 
-s At-Dt.collinearity 
-c circle.ctl 
-o At-Dtcircle.PNG
```

<img src="https://s1.ax1x.com/2020/03/24/8LPnBR.png" alt="At-Dtcircle.PNG" style="zoom:50%;" />

### bar_plotter

bar.ctl文件

```
800     //dimension (in pixels) of x axis
800     //dimension (in pixels) of y axis
Ghir_A01,Ghir_A02,Ghir_A03      //reference chromosomes
Ghir_D01,Ghir_D02,Ghir_D03      //target chromosomes
```

指令如下

```
java bar_plotter 
-g At-Dt.gff 
-s At-Dt.collinearity 
-c bar.ctl 
-o At-Dtbar.PNG
```

<img src="https://s1.ax1x.com/2020/03/24/8LPZjJ.png" alt="At-Dtbar.PNG" style="zoom:50%;" />

至此我们就基本掌握MCScanX的用法，还有一些其他的下游分析，可以参考[官网](http://chibba.pgml.uga.edu/mcscan2/)

如果想画出更美观的图片，可以参考我们的后续文章，会教大家如何处理MCScanX的结果文件然后使用circos画出更好看的圈图