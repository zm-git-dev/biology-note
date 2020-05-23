# duplication

```bash
delta-filter -q ../all.delta > q.delta	#用-q参数寻找每个query在reference上的最佳位置,允许多条query在reference上重叠
show-coords -r -c -l q.delta > q.coords	#
awk '{a[NR][1]=$1}{a[NR][2]=$2}{a[NR][3]=$4}{a[NR][4]=$5}{a[NR][5]=$19}{a[NR][6]=$0}END{for(q=1;q<=NR;q=q+1){x=a[q][1]-a[q+1][1];y=a[q][4]-a[q+1][3];z=a[q][2]-a[q+1][2];if(x>-1000&&x<1000&&y>-10000&&y<10000&&z>-1000&&z<1000&&a[q][5]==a[q+1][5]){print a[q][6] "\n" a[q+1][6]}}}' q.coords|awk '{a=substr($18,4,2)}{b=substr($19,7,2)}$1~/^[0-9]/{if(a==b){print $0}}'|awk '{print $18 "\t" $1 "\t" $2 "\t" $19 "\t" $4 "\t" $5}' >dup.bed
```

