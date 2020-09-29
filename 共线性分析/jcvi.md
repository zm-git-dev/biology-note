交互模式

```
bsub -q interactive  -Is bash
```

官网：https://github.com/tanghaibao/jcvi/wiki/MCscan-(Python-version)

```
python -m jcvi.formats.gff bed --type=gene --key=Name ~/data/D5/D5/D5_genes.gff3 -o D5.bed
bedtools  getfasta -fi ~/data/D5/D5.fa -bed D5.bed -name -fo D5.cds -s
sed -i 's/::.*//g' D5.cds
python -m jcvi.compara.catalog ortholog A2 At --no_strip_names
python -m jcvi.compara.synteny screen --minspan=30 --simple A2.At.anchors A2.At.anchors.new
python -m jcvi.graphics.karyotype seqids layout
```

画图之前要加载模块儿

```bash
module load texlive
```

![](C:\Users\DELL\Desktop\国创\matplotlib-color-1024x1008.png)

```
13种颜色
1 red
2 orange
3 yellow
4 green
5 cyan
6 blue
7 purple
8 fuchsia
9 lightsalmon
10 gold
11 slategrey
12 firebrick
13 deepskyblue
```

