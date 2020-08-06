# TAD的鉴定方法（一）——cool格式的转换

上次我们使用HiC-Pro将HiC数据比对到了参考基因组上，并且得到了bin水平的HiC数据（交互文件），我们这里介绍一下TAD的鉴定方法（使用HiCPeaks和TADLib中的两个程序），大致分为以下两步（我们会分）

+ cool格式的转换
+ TAD的鉴定

注：TAD的相关介绍请看本博客之前的文章：三维基因组之TAD的形成机制以及其特征

<!--more-->

## 1.cool格式的转化——HiCPeaks

HiC-Pro所得到的bin水平的HiC数据是TXT/NPZ bin水平的HiC数据，不能直接进行TAD的鉴定，HiCPeaks中的toCooler是将TXT/NPZ bin水平的HiC数据转化成cool格式，以便后续的分析。

### 1.1 HiCPeaks的安装

HiCPeaks的安装需要使用conda进行，操作也十分的简单

+ 其需要一些python要求 `Python 2.7/3.5+、Multiprocess、Numpy、Scipy、Matplotlib、Pandas、Statsmodels、Scikit-Learn、H5py、Cooler`
+ 还需要`ucsc-fetchchromsizes`

具体安装操作如下

首先设置通道，使上面列出的所有包都可访问(注意，顺序很重要，以确保正确的优先级)

```bash
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

然后就进行依赖的安装

```bash
conda install numpy scipy matplotlib pandas statsmodels scikit-learn h5py multiprocess cooler ucsc-fetchchromsizes
```

最后进行hicpeaks的安装

```bash
python setup.py install
```

### 1.2 HiCPeakst之oCooler的使用

#### 1.2.1 原始数据的处理

首先我们依据bin的编号文件`Ga_1_40000_abs.bed`将交互文件`Ga_1_40000.matrix`进行处理，提取出来研究中关注的两条染色体的交互数据，这里就以染色体内的交互进行说明（以前五号染色体为例）

`Ga_1_40000_abs.bed`文件内容如下，四列分别是：染色体编号、bin起始、bin终止、bin编号

```
Chr01   0       40000   1
Chr01   40000   80000   2
Chr01   80000   120000  3
Chr01   120000  160000  4
Chr01   160000  200000  5
...
```

这里我们需要批量提取所有的染色体起始和终止bin的编号，可以用bash脚本实现，脚本如下

```bash
for i in 1 2 3 4 5 	#提取每个染色体的起始和终止bin编号 
do 
grep "Chr0"$i"" Ga_1_40000_abs.bed|awk 'NR==1{printf "'$i'" "\t" $4 "\t"}END{print $4}'>>Ga.bed
done
```

然后我们就根据染色体的起始和终止编号进行染色体内交互的提取

`Ga_1_40000.matrix`文件如下

```
1       1       75
1       2       112
1       3       93
1       4       33
1       5       18
...
```

交互提取的bash脚本如下

```bash
a=($(cut -f2 Ga.bed))	#bed文件是每个染色体的起始和终止bin编号
b=($(cut -f3 Ga.bed))
mkdir 40K #交互文件要放进这个文件夹里，文件夹得命名为bin的大小

for i in $(seq 1 5)	#生成每个染色体内的交互文件命名为 染色体号_染色体号.txt（染色体号为自然数字1、2、3...），并且让bin编号从0开始（这个很重要）
do
awk -v x=${a[i-1]} -v y=${b[i-1]} '{if(x<=$1&&$1<=y&&$2>=x&&$2<=y){print $0}}' Ga_1_40000.matrix |awk -v x=${a[i-1]} '{print $1-x "\t" $2-x "\t" $3}'  >./40K/"$i"_"$i".txt
done
```

至此，原始文件的处理就结束了。

#### 1.2.2染色体大小文件的获取

我们需要创建一个文档记录交互文件中涉及染色体的大小，内容如下

```
1	123408337
2	115147059
3	139243608
4	122505728
5	93157762
```

染色体大小的获得可以用如下指令

```
samtools faidx /Ga.ref/Ga.fasta 
```

#### 1.2.3配置文件的书写

我们还需要创建一个文件，命名为`datasets`，内容如下

```
res:40000
  ./40K
```

+ 第一行是指定分辨率（bin的大小）
+ 第二行是指定交互文件所在的文件夹（绝对路径、相对路径都可以）

到现在为止我们的准备工作就正式完成啦，我们得到的文件目录结构如下

```
.
├── 40K
│   ├── 1_1.txt
│   ├── 2_2.txt
│   ├── 3_3.txt
│   ├── 4_4.txt
│   └── 5_5.txt
├── datasets
└── Ga.fasta.size
```

#### 1.2.4 程序的运行

命令很简单

```bash
toCooler -O Ga.40000.cool -d datasets --chromsizes-file Ga.fasta.size --nproc 1
# 主要参数解释如下
# -O cooler文件的输出路径及名称
# -d 指定配置文件datasets
# --chromsizes-file 指定染色体大小文件
# --nproc ICE时使用的进程数
# --no-balance 跳过ICE平衡
# --logFile 指定log文件名
```

------

好了至此我们的cool格式转化就完成了，程序成功运行之后就会生成一个二进制cool文件`A2.40000.cool`，这个文件就是我们接下来进行TAD鉴定的输入文件，下一篇我们将会详细介绍使用TADLib去进行TAD的鉴定。

+ 参考 Github https://github.com/XiaoTaoWang/HiCPeaks