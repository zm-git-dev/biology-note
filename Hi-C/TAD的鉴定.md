# TAD的鉴定方法（二）——TAD的鉴定

上一篇文章我们详细介绍了cool格式的转化，格式的转化是为了更好地进行数据的分析，这次，我们就来详细学习使用TADLib进行TAD的鉴定。

<!--more-->

## 2.TAD的鉴定——TADLib的使用

### 2.1 TADLib的安装

TADLib的安装可以使用conda进行，操作也十分的简单

+ 其需要一些python要求 `Python 3.5+、Numpy、Scipy、Matplotlib、Scikit-Learn、H5py、Cooler、pomegranate 0.10.0+、networkx 1.x (not compatible with networkx 2.0 yet)`

具体安装操作如下

首先设置通道，使上面列出的所有包都可访问(注意，顺序很重要，以确保正确的优先级)

```bash
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

然后就进行依赖的安装

```bash
conda install setuptools numpy scipy matplotlib scikit-learn cooler pomegranate=0.10.0 networkx=1
```

最后进行TADLib的安装

```bash
pip install TADLib
```

### 1.2 TADLib之Hierarchical TAD的使用

#### 1.2.1 初始文件的准备

初始输入文件为cool格式的交互文件，cool文件的获取推荐使用HiC-Pro+HiCPeaks来获得，之前的文章都有介绍。

#### 1.2.2 配置文件的书写

我们需要一个配置文件去指定分辨率、重复标签和cool URI信息，命名为`meta_file`

文件规范格式如下

```
res:40000
  rep1:/HiC/A2/TAD/rep1/A2.40000.cool::/40000
  rep2:/HiC/A2/TAD/rep2/A2.40000.cool::/40000
```

+ 第一行指定分辨率
+ 第二行及以后是指定不同的生物学重复对应的cool URI

#### 1.2.3 程序的运行

```bash
hitad -O test.txt -d meta_file --logFile hitad.log
# -O 指定输出文件名称
# -d 指定配置文件
# --logFile 指定log文件名称
# --maxsize 指定最后的domain大小
# -p 指定线程数
# -W WEIGHT_COL 使用标准化后的矩阵文件（默认）
# -W RAW 使用非标准化的矩阵文件
```

### 1.3 结果文件的解读

软件运行结束后，会生成一个后缀为.txt的文本文件，文本中记录了不同的domain的信息，如下所示

```
1       0       1200000 2
1       0       1680000 1
1       0       3960000 0
...
```

+ 第一列为染色体编号
+ 第二列为domain的起始位置
+ 第三列为domain的终止位置
+ 第四列为domain的类型，0代表TAD、1代表sub-TAD、2代表subsequent domain

------

至此，我们就从原始的HiC数据经过HiC-Pro+HiCPeaks+TADLib成功鉴定出了TAD。

参考：https://xiaotaowang.github.io/TADLib/hitad.html