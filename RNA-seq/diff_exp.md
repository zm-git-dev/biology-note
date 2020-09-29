```bash
#!/bin/bash
###
### get homologous gene and  different expression script
###
### Usage:
###   bash get_different_exp.sh -H s -S -i -g -G -o -p -t
###
### Options:
###  -H  Input homologous gene file to read.
###  -s  Input stringtie file(diploid) to read.
###  -S  Input stringtie file(tetraploid) to read.
###  -i  Input rearrangement file to read(two columns)
###  -g  Diploid gene file to read
###  -G  Tetraploid gene file to read
###  -o  Output file to write.
###  -p  Specify output file prefix
###  -t  Rearrangement type
###  -h  Show this message.


help() {
        awk -F'### ' '/^###/ { print $2 }' "$0"
}

if [[ $# == 0 ]] || [[ "$1" == "-h" ]]; then
        help
        exit 1
fi

while getopts H:s:S:i:o:p:t:g:G: opt; do
    case "$opt" in
        H) homologous=$OPTARG ;;
        s) exp_one=$OPTARG ;;
        S) exp_two=$OPTARG ;;
        i) input=$OPTARG ;;
        o) output=$OPTARG ;;
        p) prefix=$OPTARG ;;
        t) type=$OPTARG ;;
        g) gene=$OPTARG ;;
        G) GENE=$OPTARG ;;
    esac
done

#find structural variation associated with homologous genes
for i in 01 02 03 04 05 06 07 08 09 10 11 12 13
do
SVline=`wc -l "$input"/"$prefix"_"$i"_"$type".txt | cut -d " " -f1`
        for q in `seq 1 "$SVline"`
        do
                #提取SVs中所包含的基因
                awk 'NR=="'$q'"{print}' "$input"/"$prefix"_"$i"_"$type".txt |awk '{if($2<$3){print $1 "\t" $2 "\t" $3}else{print $1 "\t" $3 "\t" $2}}'>"$output"/1_1_tmp.txt
                awk 'NR=="'$q'"{print}' "$input"/"$prefix"_"$i"_"$type".txt |awk '{if($5<$6){print $4 "\t" $5 "\t" $6}else{print $4 "\t" $6 "\t" $5}}'>"$output"2_1_tmp.txt
                bedtools intersect  -a "$output"/1_1_tmp.txt -b "$gene" -loj |awk '$7!="."{print $7}' >"$output"/1_2_tmp.txt
                bedtools intersect  -a "$output"/2_1_tmp.txt -b "$GENE" -loj |awk '$7!="."{print $7}' >"$output"/2_2_tmp.txt
                
                #判断SVs间内是否包含基因
                size1=`ls -l "$output"/1_2_tmp.txt |awk '{print $5}'`
                size2=`ls -l "$output"/2_2_tmp.txt |awk '{print $5}'`
                if [ "$size1" == "0" ] || [ "$size2" == "0" ]; then
                rm "$output"/1_1_tmp.txt "$output"2_1_tmp.txt "$output"/1_2_tmp.txt "$output"/2_2_tmp.txt
                echo "$q" >>1
                continue
                fi
                
                #输出所有基因的全排列
                paste "$output"/1_2_tmp.txt "$output"/2_2_tmp.txt |awk '/^\t/{print "None"$0}/.\t./{print $0}/\t$/{print $0"None"}'>"$output"/3_tmp.txt
                awk '{a[NR]=$1;b[NR]=$2}\
                END{for(m=1;m<=NR;m++){\
                        for(n=1;n<=NR;n++){\
                        print a[m] "\t" b[n]}}}' "$output"/3_tmp.txt|\
                awk '$1!="None"&&$2!="None"{print $0}' >"$output"/4_tmp.txt
                
                #找出包含的同源基因
                cat "$output"/4_tmp.txt "$homologous" |sort|uniq -d |awk '{print $0 "\t" "Chr'$i'_'$q'"}'>>"$output"/5_tmp.txt
                rm "$output"/1_1_tmp.txt "$output"2_1_tmp.txt "$output"/1_2_tmp.txt "$output"/2_2_tmp.txt "$output"/3_tmp.txt "$output"/4_tmp.txt 
        done
sort "$output"/5_tmp.txt |uniq >"$output"/"$prefix"_"$i"_"$type"_gene.txt
rm "$output"/5_tmp.txt
done
                
#Calculate the expression quantity difference
for i in 01 02 03 04 05 06 07 08 09 10 11 12 13
do
        cut -f1 "$output"/"$prefix"_"$i"_"$type"_gene.txt |xargs -I {} grep {} "$exp_one" |\
        awk '{print $1 "\t" $9}' >"$output"/tmp1.tmp
        cut -f2 "$output"/"$prefix"_"$i"_"$type"_gene.txt |xargs -I {} grep {} "$exp_two" |\
        awk '{print $1 "\t" $9}' >"$output"/tmp2.tmp
        paste "$output"/tmp1.tmp "$output"/tmp2.tmp |\
        awk '{if($2!=0&&$4!=0){print $0 "\t" (log($4/$2)/log(2))}\
        else if($2==0&&$4!=0){print $0 "\t" "max"}else{print $0 "\t" "min"}}' >"$output"/"$prefix"_"$i"_"$type"_gene_diff_exp.txt
        rm "$output"/tmp1.tmp "$output"/tmp2.tmp
done
```

写了一个脚本实现了以下功能：

+ 从结构变异的区间中寻找对应的同源基因
+ 找到同源基因后利用表达量数据计算出每一对同源基因的表达量