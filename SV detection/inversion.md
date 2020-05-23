# inversion

```bash
awk '$1~/^[0-9]/{a=substr($18,4,2)}{b=substr($19,7,2)}{if($5<$4&&a==b){print $0}}' 1.coords	#找出对应染色体的假定的倒位区间
| xargs -I {} grep {} -A 1 -B 1 ./1.coords	#找出假定倒位区间侧翼的区间
| awk '{if($4>$5){print $0 "\t" "|" "\t" "1!"}else{print $0 "\t" "|" "\t" "0!"}}'	#在最后一列对正负区间进行注释
| awk '{a[NR][1]=$21}{a[NR][2]=$0}END{for(q=1;q<=NR;q=q+1){if(a[q][1]=="0!"){print a[q][2] "\n" a[q][2]}else{print a[q][2]}}}' >tmp.coord	#将所有的不倒位的区域重复写一遍

grep -w -A 1 '1!' tmp.coord|awk '/1!$/{print $0}'|sort -k18 |uniq |awk '/.*Chr01\tGhir_A01.*/{print $0}'|sort -n -k1	#关键步骤，找出一号染色体的倒位的区域！
|awk '{a[NR][1]=$1}{a[NR][2]=$2}{a[NR][3]=$4}{a[NR][4]=$5}{a[NR][5]=$0}END{for(q=1;q<=NR;q=q+1){b=a[q][2]-a[q+1][1];c=a[q][4]-a[q+1][3];if(b>-1000&&b<1000&&c>-1000&&c<1000){print $18 "\t" a[q][1] "\t" a[q+1][2] "\t" $19 "\t" a[q][3] "\t" a[q+1][4] ;q=q+1}else{print $18 "\t" a[q][1] "\t" a[q][2] "\t" $19 "\t" a[q][3] "\t" a[q][4]}}}'
|awk '{a[NR][1]=$2}{a[NR][2]=$3}{a[NR][3]=$5}{a[NR][4]=$6}{a[NR][5]=$0}END{for(q=1;q<=NR;q=q+1){b=a[q][2]-a[q+1][1];c=a[q][4]-a[q+1][3];if(b>-1000&&b<1000&&c>-1000&&c<1000){$1 "\t" a[q][1] "\t" a[q+1][2] "\t" $4 "\t" a[q][3] "\t" a[q+1][4] ;q=q+1}else{print a[q][5]}}}'
|awk '{a[NR][1]=$2}{a[NR][2]=$3}{a[NR][3]=$5}{a[NR][4]=$6}{a[NR][5]=$0}END{for(q=1;q<=NR;q=q+1){b=a[q][2]-a[q+1][1];c=a[q][4]-a[q+1][3];if(b>-1000&&b<1000&&c>-1000&&c<1000){$1 "\t" a[q][1] "\t" a[q+1][2] "\t" $4 "\t" a[q][3] "\t" a[q+1][4] ;q=q+1}else{print a[q][5]}}}'
|awk '{a[NR][1]=$2}{a[NR][2]=$3}{a[NR][3]=$5}{a[NR][4]=$6}{a[NR][5]=$0}END{for(q=1;q<=NR;q=q+1){b=a[q][2]-a[q+1][1];c=a[q][4]-a[q+1][3];if(b>-1000&&b<1000&&c>-1000&&c<1000){$1 "\t" a[q][1] "\t" a[q+1][2] "\t" $4 "\t" a[q][3] "\t" a[q+1][4] ;q=q+1}else{print a[q][5]}}}'|	#将gap<1000bp的进行合并
```
