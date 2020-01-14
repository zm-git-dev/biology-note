# Identification of inversion

倒位的鉴定要从得到的染色体上的对应的片段坐标开始，至于如何得到这个坐标，请参考 [PAVs](README.md) 的前半部分

------

## inversion的鉴定

得到的坐标文件如下

![lbGTtU.png](https://s2.ax1x.com/2020/01/14/lbGTtU.png)

据生物学知识，我们知道倒位主要是指对应染色体的部分，所以我们就以两个基因组种的一号染色体来说明

首先我们要找出我们在At亚组一号染色体上的i与A2一号染色体对应的部分。并且把相反对应的筛选出来，再用bedtools进行合并，指令如下

```bash
awk '/.*Chr01.*Ghir_A01/{if($5<$4){print "Ghir_A01" "\t" $5 "\t" $4}}' A2At.coord|sort -k2 -n >tmp.bed
```

```bash
bedtools merge -i tmp.bed >At01inversion.bed
```

得到的结果如下图所示

[![lbzWB6.png](https://s2.ax1x.com/2020/01/14/lbzWB6.png)](https://imgchr.com/i/lbzWB6)

## 数据的筛选和统计

```bash
awk '{a=$3-$2}{if(a>100){print $0}}' At01inversion.bed ： 筛选小于100bp的片段
awk '{a=$3-$2}{if(a>100){print a}}' At01inversion.bed |awk '{sum+=$1} END {print sum}'|cat ：统计At一号染色体上inversion总长度
```

------

其他的染色体就可以按照这个步骤进行查询、筛选和统计了