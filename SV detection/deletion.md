# deletion

## A2

先把初始结果文件过滤，得到允许重排的最佳匹配区间

```bash
delta-filter -i 90 -l 100  -1 -r -q ../A2At.delta  > 1.delta
show-coords -r -c -l 1.delta > 1.coords
```

用-1参数得到允许重拍的的坐标文件筛选出没有比对上的长度大于100的片段

```bash
awk '/.*Chr01.*Ghir/{print $0}' 1.coords |awk '{a[NR][1]=$1;a[NR][2]=$2}END{for(i=1;i<=NR-1;i++){print "Chr01""\t"a[i][2]"\t"a[i+1][1]}}'|awk '{a=$3-$2}{if(a>=100){print $0 "\t" a}}' >A01rawdel.bed
```

将结果提取成fasta文件并用blastn进行全局搜索

```bash
bedtools getfasta -fi ../../../A2_genome.fasta -bed A01rawdel.bed -fo A01raw.fasta	#提取序列

makeblastdb -in ../../../../At_genome.fasta -dbtype nucl  -title At -parse_seqids -out At	#建库

bsub -J 1 -n 30 -q normal -R span[hosts=1] -o out.txt "blastn -query ./A01raw.fasta -db ./db/At -evalue 1e-5 -num_threads 30 -outfmt 6 -num_alignments 5 -out A01.blast"	#比对
```

将coverage >50% and identity >90%.的片找出来并且过滤掉，之后得到的就是可信度较高的在多倍化过程中完全消失的片段

```bash
awk -F '[\t:-]' '{b=$3-$2}{c=$6/b}{if(c>0.5&&$5>90){print $1 "\t" $2 "\t" $3}}' A01.blast|sort|uniq >guolv	#找出需要过滤掉的片段

cat guolv A01rawdel.bed|sort -n -k2|uniq -u >A01del.bed	#过滤片段
```

## At

得到At一号染色体匹配上的区段，进而得到没有匹配到的区段，合并后提取序列

```bash
awk '{if($19=="Ghir_A01"){if($4<$5){print $19 "\t" $4 "\t" $5}else{print $19 "\t" $5 "\t" $4}}}' 1.coords|sort -n -k2 >A01.tmp
bedtools merge -i  A01.tmp |awk '{a[NR][1]=$2;a[NR][2]=$3}END{for(i=1;i<=NR-1;i++){print "Ghir_A01""\t"a[i][2]"\t"a[i+1][1]}}' >A01rawdel.bed
bedtools getfasta -fi /public/home/yxlong/data/AD1/At_genome.fa -bed A01rawdel.bed -fo A01raw.fasta	#提取序列
```

blast比对

```bash
bsub -J 1 -n 30 -q smp -R span[hosts=1] -o out.txt "blastn -query ./A"$i"raw.fasta -db /public/home/yxlong/subject/SV/database/A2/A2 -evalue 1e-5 -num_threads 10 -outfmt 6 -num_alignments 5 -out A"$i".blast"
```

将coverage >50% and identity >90%.的片找出来并且过滤掉，之后得到的就是可信度较高的在多倍化过程中完全消失的片段

```bash
awk -F '[\t:-]' '{b=$3-$2}{c=$6/b}{if(c>0.5&&$5>90){print $1 "\t" $2 "\t" $3}}' A01.blast|sort|uniq >A01.tmp
cat A01.tmp A01rawdel.bed|sort -n -k2|uniq -u >A01del.bed
```

