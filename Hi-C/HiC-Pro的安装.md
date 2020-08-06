# HiC-Pro安装与使用

HiC-Pro可以用来处理Hi-C数据，从原始的fastq文件(配对端Illumina数据)到标准化的交互图谱。简单的来说就是将Hi-C数据比对到拼装好的参考基因组上，并形成交互文件去存储Hi-C数据。下面我们就来介绍一下HiC-Pro的安装与使用。

<!--more-->

## HiC-Pro的安装

------

### HiC-Pro所需要的这些依赖

[![依赖.png](https://s1.ax1x.com/2020/07/11/UldOCd.png)](https://imgchr.com/i/UldOCd)

### **1.使用conda安装依赖**

python版本为`2.7`

```bash
conda create -n hicpro python=2.7	#强烈推荐安装一个新的环境
conda install -y samtools bowtie2 R
conda install -y pysam bx-python numpy scipy
```

### **2.安装对应的R包**

在当前conda环境下运行R

也可以绝对路径运行R

```bash
R
install.packages(c('ggplot2','RColorBrewer'))
```

### 3.配置confir-install.txt安装文件

`PREFIX`软件安装路径，会在该路径创建一个`HiC-Pro_2.11.1`目录

`R_PATH`指定conda环境下的R

`PYTHON_PATH`指定conda环境下的python

`CLUSTER_SYSCLUSTER_SYS`集群调度系统为`TORQUE,SGE,SLURM,LSF`四个中的一种

```bash
PREFIX = /public/home/yxlong/yxlong/app/	#HiC-Pro所在目录
BOWTIE2_PATH = /public/home/yxlong/miniconda3/bin/bowtie2	#bowtie2所在目录
SAMTOOLS_PATH = /public/home/yxlong/miniconda3/bin/samtools	#samtools所在的目录
R_PATH = /public/home/yxlong/miniconda3/envs/hicpro/bin/R	#R包所在目录
PYTHON_PATH = /public/home/yxlong/miniconda3/envs/hicpro/bin/python	#python所在目录
CLUSTER_SYS = LSF	#TORQUE,SGE,SLURM,LSF四个中的一种
```

这些路径可以which+对应的依赖（如: which bowtie2）获得

### 4.安装

配置文件修改完成之后运行下列指令即可完成安装

```bash
make configure
make install
```



## HiC-Pro的使用

------

### 1.文件的处理

#### 1.1文件夹的创建

建议创建两个文件夹命名为`sample.reads`和`sample.ref`（sample为自己的物种名字即可，下面以亚洲棉Ga为例）

#### 1.2`Ga.reads`的的目录结构

目录结构如下（依据自己的研究修改物种名即可，Ga_1、Ga_2...为不同的生物学重复）

![reads.png](https://s1.ax1x.com/2020/07/11/UldbUe.png)

#### 1.3`Ga.ref`的相关文件

![ref.png](https://s1.ax1x.com/2020/07/11/Uldq4H.png)

Ga.fasta文件是一个link文件，链接的是拼装好的参考基因组

建立索引 （索引的命名为物种名）

```bash
/PATH/TO/bowtie2-build --threads 20 /Ga.ref/Ga.fasta /Ga.ref/Ga 
```

#### 1.4酶切信息文件的获得

采用HiC-Pro自带的digest_genome.py程序酶切位点信息文件

```bash
/PATH/TO/HiC-Pro_2.11.1/bin/utils/digest_genome.py -r hindiii -o Ga.HindIII.txt /Ga.ref/Ga.fasta
#-r指定酶的名称或者序列（如下图）
#-o指定酶切信息文件的输出目录
#最后的fasta文件是组装的参考基因组
```

![酶切.png](https://s1.ax1x.com/2020/07/11/UldjgI.png)

#### 1.5基因组中序列大小文件

序列大小文件格式如下 contig1[TAB]contig1_length

![size.png](https://s1.ax1x.com/2020/07/11/UldX8A.png)

可以用如下指令获得

```bash
samtools faidx /Ga.ref/Ga.fasta && awk '{print $1"\t"$2}' /PATH/TO/Ga.fasta.fai > Ga.fasta.size #注意生成的.fai在.fasta文件所在的目录下面
```

至此，相关文件的准备就结束了。

### 2.配置文件的书写

首先将配置文件复制一份到当前目录下

```
cp /PATH/TO/HiC-Pro_2.11.1/config-hicpro.txt .
```

然后，进行修改（以下为通常需要修改的参数，其他的参数的修改请参考官网）

```bash
# N_CPU，例如N_CPU = 24
# JOB_NAME，例如JOB_NAME = Ga
# JOB_WALLTIME，例如JOB_WALLTIME = 11:00:00
# JOB_QUEUE，例如JOB_QUEUE = normal
# JOB_MAIL，例如JOB_MAIL = 1570447120@qq.com
# BOWTIE2_IDX_PATH填写用bowtie2对reference建立索引的目录，注意是目录，而不是索引路径!如/PATH/to/Ga.ref
# REFERENCE_GENOME，例如Ga 注意：这里REFERENCE_GENOME一定要和bowtie2建立的索引对应上,名字为物种名
# GENOME_SIZE，即生成的文件Ga.fasta.size（最好使用绝对路径）
# GENOME_FRAGMENT，用digest_genome.py程序生成的文件Ga.HindIII.txt
# LIGATION_SITE，酶的序列，例如HindIII，则为AAGCTAGCTT；如果是MboI则序列为GATCGATC。
# MIN_FRAG_SIZE，例如MIN_FRAG_SIZE = 100
# MAX_FRAG_SIZE，例如MAX_FRAG_SIZE = 160000
# MIN_INSERT_SIZE，例如MIN_INSERT_SIZE = 200
# MAX_INSERT_SIZE，例如MAX_INSERT_SIZE = 600
# GET_PROCESS_SAM，例如GET_PROCESS_SAM = 1
```

### 3.运行HiC-Pro

HiC-Pro的使用很简单，主要就是三个参数，指令如下（单线程运行）

```bash
/PATH/TO/HiC-Pro_2.11.1/bin/HiC-Pro --input Ga.reads --output hicpro_output --conf config-hicpro.txt 
# --input reads所在的文件
# --output 输出的文件夹
# --conf 配置文件
```

如果想多线程运行，要加上-p参数，输入指令后会在输出文件中生成两个文件`HiCPro_step1.sh`、`HiCPro_step2.sh`。先运行step1，step1执行完成后，再执行step2即可。