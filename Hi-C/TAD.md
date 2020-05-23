# cool格式的转化

转换成cool格式

此处写了一个提取染色体内交互的脚本

```bash
#!/bin/bash
a=($(cut -f2 Graimondii.bed))	#bed文件是每个染色体的起始和终止bin编号
b=($(cut -f3 Graimondii.bed))
for i in $(seq 1 13)
do
awk -v x=${a[i-1]} -v y=${b[i-1]} '{if(x<=$1&&$1<=y&&$2>=x&&$2<=y){print $0}}' Graimondii_1_40000.matrix>"$i"_"$i".txt
done

```

```
toCooler -O A01.50000.cool -d datasets --chromsizes-file Graimondii.chromsizes --nproc 1
```

```bash
#!/bin/bash
a=($(cut -f2 Graimondii.bed))	#bed文件是每个染色体的起始和终止bin编号
b=($(cut -f3 Graimondii.bed))
mkdir 40K
for i in $(seq 1 13)
do
awk -v x=${a[i-1]} -v y=${b[i-1]} '{if(x<=$1&&$1<=y&&$2>=x&&$2<=y){print $0}}' Graimondii_1_40000.matrix |awk -v x=${a[i-1]} '{print $1-x+1 "\t" $2-x+1 "\t" $3}'  >./40K/"$i"_"$i".txt
done

for i in $(seq 1 9)
do 
mv ./40K/"$i"_"$i".txt ./D50"$i"/40K
done

for i in 10 11 12 13
do
mv ./40K/"$i"_"$i".txt ./D5"$i"/40K
done

for i in 01 02 03 04 05 06 07 08 09 10 11 12 13
do 
mkdir -p D5"$i"/40K	#递归创建目录
cp Graimondii.fasta.size ./D5"$i"	#复制染色体大小文件
echo "res:40000
  ./40K
" > ./D5"$i"/datasets	#创建toCooler的配置文件
echo "res:40000
  rep1:/public/home/yxlong/subject/HiC/D5/TAD/D5$i/D5$i.40000.cool::/40000
" > ./D5"$i"/meta_file	#创建hitad的配置文件

cd ./D5"$i" 
    toCooler -O D5"$i".40000.cool -d datasets --chromsizes-file Graimondii.fasta.size --no-balance	#执行toCooler 
hitad -O test.txt -d meta_file --logFile hitad.log -W RAW	#执行hitad
cd ..

done


```

https://github.com/XiaoTaoWang/HiCPeaks#overview

鉴定TAD

```
hitad -O test.txt -d meta_file --logFile hitad.log
```

https://xiaotaowang.github.io/TADLib/hitad.html

