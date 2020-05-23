```bash
intersectBed -loj -a window.bed -b gene.bed >gene_circos.bed

|awk '$5!="."{a[$4][1]+=$7-$6+1;a[$4][2]=$1;a[$4][3]=$2;a[$4][4]=$3;}$5=="."{a[$4][1]+=0;a[$4][2]=$1;a[$4][3]=$2;a[$4][4]=$3;}END{for(i in a){printf a[i][2]"\t"a[i][3]"\t"a[i][4]"\t"a[i][1]"\n"}}' 	#统计每个bin的覆盖度
```

