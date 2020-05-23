# translocation

### 染色体间的易位

```bash
awk '/.*Chr01\tGhir_A.*/{a[NR][1]=$19}{a[NR][2]=$0}END{for(q=3;q<=NR;q=q+1){if(a[q][1]!=a[q-1][1]&&a[q][1]!=a[q+1][1]&&a[q+1][1]==a[q-1][1]&&a[q][1]!=Ghir_A01){print a[q][2]}}}' 1.coords |awk '{print $18 "\t" $1 "\t" $2 "\t" $19 "\t" $4 "\t" $5}'|sort -k2 -n >A01_simple_inter_tran.bed	#染色体间的简单倒位
```

```bash
awk '/.*Chr01\tGhir_A.*/{a[NR][1]=$19}{a[NR][2]=$0}END{for(q=1;q<=NR;q=q+1){if(a[q][1]!=a[q+1][1]&&a[q][1]!="Ghir_A01"&&a[q+1][1]!="Ghir_A01"){print a[q][2] "\n" a[q+1][2]}}}' 1.coords|sort -n -k2|uniq|awk '{print $18 "\t" $1 "\t" $2 "\t" $19 "\t" $4 "\t" $5}' |awk 'NR == 1 {next} {print $0}' >A01_complex_inter_tran.bed	#染色体间的复杂易位
```

### 染色体内的易位

```bash
awk '{a[NR][1]=$1}{a[NR][2]=$2}{a[NR][3]=$4}{a[NR][4]=$5}{a[NR][5]=$0}END{for(q=2;q<=NR;q=q+1){x=a[q][3]-a[q-1][4];y=a[q+1][3]-a[q][4];x=x^2;y=y^2;e=sqrt(x);f=sqrt(y);if(e+f>2000000){if(e>1000000&&f<100000){print a[q][5] "\t" q "\t" "1!"}else if(f>1000000&&e<100000){print a[q][5] "\t" q "\t" "2!"}else if(e>1000000&&f>1000000){print a[q][5] "\t" q "\t" "3!"}}}}' chr_chr.coords 
#找出易位区间，并对其进行注释
|awk '{a[NR][1]=$21}{a[NR][2]=$0}{a[NR][3]=$20}END{for(q=1;q<=NR;q=q+1){x=a[q][3]-a[q-1][3];if(a[q][1]=="3!"){print a[q][2]}else if(a[q-1][1]=="1!"&&a[q][1]=="2!"&&a[q+1][1]!="3!"&&x==1){print a[q-1][2] "\n" a[q][2]}}}'	#找出易位区间
|awk '$18~/Chr01/{print $18 "\t" $1 "\t" $2 "\t" $19 "\t" $4 "\t" $5}'|sort -k2 -n >A01_intra_tran.bed
```

