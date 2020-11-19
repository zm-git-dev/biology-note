## 1、鉴定PAV



## 2、鉴定rearrangement

### 2.1、得到-1参数和-g参数得到的坐标文件

```bash
show-coords -r -c -l 1.delta > 1.coords
show-coords -r -c -l g.delta > g.coords
awk '$18~/^Chr/{print $18"\t"$1"\t"$2"\t"$19"\t"$4"\t"$5}' 1.coords >1_six.coords
sh ~/script/re_chromosome.sh -i ./1_six.coords -o ./1_re_six.coords  -c 6 -p A2
awk '$18~/^Chr/{print $18"\t"$1"\t"$2"\t"$19"\t"$4"\t"$5}' g.coords >g_six.coords
sh ~/script/re_chromosome.sh -i ./g_six.coords -o ./g_re_six.coords  -c 6 -p A2
```

### 2.2、将-g参数得到的坐标文件中的不同染色体对应的区间过滤掉，保留的就是等位区域

```bash
awk '{a=substr($1,4,2)}{b=substr($4,7,2)}{if(a==b){print $0}}' g_re_six.coords >allelic.coords
```

### 2.3、将-1参数所得到的区间减去等位区域得到的就是不等位的区域，也就是重排区域

```bash
cat allelic.coords 1_re_six.coords |sort -k1,1 -k2n,2|uniq -u >no_allelic.coords
```

|                          | A2_vs_AD1 | A2_vs_AD2 | D5_vs_AD1 | D5_vs_AD2 |
| :----------------------: | :-------: | :-------: | :-------: | :-------: |
|      Raw_length(MB)      |    431    |    391    |    79     |    75     |
| PacBio_filter_length(MB) |    403    |    363    |    65     |    63     |

### 2.4、依据PacBio数据对重排数据进行纠正，如果三代数据跨过结构变异的断点，则保留，否则舍去。

```bash
awk '$1=="Chr02"{print "Chr03",$2,$3,$4,$5,$6"}$1=="Chr03"{print "Chr02",$2,$3,$4,$5,$6"}$1!="Chr02"&&$1!="Chr03"{print}' ../coords/no_allelic.coords >no_allelic.coords1 #D亚组所需的变换
sh ~/script/rearrangement_filter.sh -i ../coords/no_allelic.coords -o . -d ~/data/PacBio/A2/A2_PacBio -D ~/data/PacBio/AD2/AD2_PacBio -f ~/data/A2/A2.fa -F ~/data/AD2/At_genome.fa -s one
```

### 2.5、首先将不同对应染色体编号不同的区块定义为染色体间的易位；

```bash
awk '{a=substr($1,4,2)}{b=substr($4,7,2)}{if(a!=b){print $0}}' no_allelic.coords>A2_vs_AD1_all_inter_tran.txt
```

### 2.6、依据结构变异侧翼区间的方向和位置人工区分染色体内的易位和倒位

### 2.7、对结构变异进行编号

```bash
awk '{print $1"\t"$2"\t"$3"\t"$1"_absence_"NR}' D5_vs_AD1_all_absence.txt >1
awk '{print $1"\t"$2"\t"$3"\t"$1"_inversion_"NR}' D5_vs_AD1_all_inversion.txt >2
awk '{print $1"\t"$2"\t"$3"\t"$1"_inter_tran_"NR}' D5_vs_AD1_all_inter_tran.txt >3
awk '{print $1"\t"$2"\t"$3"\t"$1"_intra_tran_"NR}' D5_vs_AD1_all_intra_tran.txt >4
awk '{print $1"\t"$2"\t"$3"\t"$1"_complex_"NR}' D5_vs_AD1_all_complex.txt >5
cat 1 2 3 4 5|sort -k1,1 -k2,3n >D5_vs_AD1_all_D5_SV.bed
```

最终得到结构变异数目如下

|                | A2_vs_AD1 | A2_vs_AD2 | D5_vs_AD1 | D5_vs_AD2 |
| -------------- | --------- | --------- | --------- | --------- |
| PAV(absence)   | 31859     | 33373     | 17334     | 17190     |
| inversion      |           |           | 2482      | 3275      |
| inter_tran     |           |           | 7976      | 7638      |
| intra_tran     |           |           | 4178      | 2754      |
| complex_reigon |           |           | 103       | 420       |
| all            |           |           | 32073     | 31277     |

结论：

## 3、BA_SV的鉴定

```
intersectBed -loj -a ../boundary/D5_boundary.bed -b ~/The_Crop_Journal/SVs/D5_vs_AD2/D5_vs_AD2_all_D5_SV.bed |awk '$4!="."{print $4"\t"$5"\t"$6"\t"$7}'|sort -k1,1 -k2,3n|uniq >D5_vs_AD2_BA_D5_SV.bed
```

|                   | absence       | presence      | rearrangement | all    |
| ----------------- | ------------- | ------------- | ------------- | ------ |
| A2  vs AD1_At_BA  | 4776(14.99%)  | 5082(17.44%)  | 7530(15.50%)  | 17388  |
| A2  vs AD1_At_ALL | 31859         | 29143         | 48587         | 109589 |
| A2  vs AD2_At_BA  | 5147(15.42%)  | 3223(12.52%)  | 6787(16.05%)  | 15157  |
| A2  vs AD2_At_ALL | 33373         | 25745         | 42283         | 101401 |
| D5  vs AD1_Dt_BA  | 3056(17.63%)  | 6220(20.28%)  | 2531(17.17%)  | 11807  |
| D5  vs AD1_Dt_ALL | 17334         | 30664         | 14739         | 62737  |
| D5  vs AD2_Dt_BA  | 3018(17.56%)  | 4543(15.52%)  | 2241(15.91%)  | 9802   |
| D5  vs AD2_Dt_ALL | 17190         | 29258         | 14087         | 60535  |
| A2_vs_At_BA       | 9923(15.21%)  | 8305(15.13%)  | 15060(16.57%) |        |
| A2_vs_At_ALL      | 65232         | 54888         | 90870         |        |
| D5_vs_Dt_BA       | 6074(17.59%)  | 10763(17.96%) | 4772(16.55%)  |        |
| D5_vs_Dt_ALL      | 34524         | 59922         | 28826         |        |
| ALL_BA            | 15997(16.04%) | 19068(16.61%) | 19089(15.95%) |        |
| ALL_ALL           | 99756         | 114810        | 119696        |        |

+ D亚组的SV相对于A亚组而言更容易发生在边界内
+ 在棉花多倍化过程中无论是重排还是PAV都更不容易发生在边界中

## 4、基于随机排列的边界

## 5、BA和withinTAD的SVs长度分析



## 5、可视化

BA和总的SV的长度分布

```R
library(ggplot2)
Data=read.table("2",header = T)
a <- ggplot(data=Data,aes(x=value))+
geom_histogram(aes(y=..density..), binwidth = 0.5,fill="#798F2D",colour="#798F2D",alpha=0.5,size=0.15)+
#geom_density(color="#AFCD3E") +
theme_bw() +
theme(axis.line=element_blank(),axis.text.x=element_blank(), axis.ticks = element_blank(),
      axis.text=element_blank(), 
      axis.title.x=element_blank(), plot.title = element_blank(),
      axis.title.y=element_blank(),legend.position="none",
      panel.background=element_blank(),panel.border=element_blank(),panel.grid.major=element_blank(), 
      panel.grid.minor=element_blank(),plot.background=element_blank(),plot.margin=unit(rep(0,4),'lines')) +
labs(x = 'Absnece Length',y = 'Tatal SV',title = "")+
scale_y_continuous(expand = c(0,0),limits = c(0,0.2),breaks = c(0.1,0.2),label=c(5,10))+
scale_x_continuous(expand = c(0,0),limits = c(0,50),breaks = c(0,25,50))

Data1=read.table("3",header = T)
b <- ggplot(data=Data1,aes(x=value))+
geom_histogram(aes(y=..density..), binwidth = 0.5,fill="#AFCD3E",colour="#AFCD3E",alpha=0.5,size=0.15)+
#geom_density(color="#AFCD3E") +
theme_bw() +
theme(axis.line=element_blank(),axis.text.x=element_blank(), axis.ticks = element_blank(),
      axis.text=element_blank(), 
      axis.title.x=element_blank(), plot.title = element_blank(),
      axis.title.y=element_blank(),legend.position="none",
      panel.background=element_blank(),panel.border=element_blank(),panel.grid.major=element_blank(), 
      panel.grid.minor=element_blank(),plot.background=element_blank(),plot.margin=unit(rep(0,4),'lines')) +
labs(x = 'Absnece Length',y = 'AB',title = "")+
scale_y_continuous(expand = c(0,0),limits = c(0,0.2),breaks = c(0.1,0.2),label=c(5,10))+
scale_x_continuous(expand = c(0,0),limits = c(0,50),breaks = c(0,25,50))

jpeg(file = "1.jpg",width =3000,height = 2000,units = "px",res =500) 
print(a)
jpeg(file = "2.jpg",width =3000,height = 2000,units = "px",res =500) 
print(b)
dev.off()
```

