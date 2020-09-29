## 两个文本按条件合并

```bash
$ cat 1.txt 2.txt 
0011AAA 200.00 20050321
0012BBB 300.00 20050621
0013DDD 400.00 20050622
0014FFF 500.00 20050401
I0011  11111    hhh
I0012  22222    kkk
I0014  55555    ppp
I0017  66666    ttt

$ awk  'NR==FNR{a[substr($1,2,5)]=$0}NR>FNR&&a[b=substr($1,1,4)]{print $0, a[b]}' 2.txt 1.txt #比较 1.txt的1-4字符 和 2.txt的2-5 字符，如果相同，将2.txt 的全部列 与 1.txt 合并
0011AAA 200.00 20050321 I0011  11111    hhh
0012BBB 300.00 20050621 I0012  22222    kkk
0014FFF 500.00 20050401 I0014  55555    ppp
#FNR表示当前所属的行数，处理一个新的文件时就会从0开始
```

## 不排序删除重复行

```bash
$ echo -e '1 2 3\n1 2 3\n1 2 4\n1 2 3\n1 2 5' | awk '!x[$0]++'
1 2 3
1 2 4
1 2 5
#awk 的基本执行流程是，对文件的每一行，做一个指定的逻辑判断，如果逻辑判断成立，则执行指定的命令；如果逻辑判断不成立，则直接跳过这一行。这里写的 awk 命令是!x[$0]++，意思是，首先创建一个 map 叫x，然后用当前行的全文$0作为 map 的 key，到 map 中查找相应的 value，如果没找到，则整个表达式的值为真，可以执行之后的语句；如果找到了，则表达式的值为假，跳过这一行。由于表达式之后有++，因此如果某个 key 找不到对应的 value，该++操作会先把对应的 value 设成 0，然后再自增成 1，这样下次再遇到重复的行的时候，对应的 key 就能找到一个非 0 的 value 了。
```

## next合并多行为一行

```bash
$ cat data
web01[192.168.2.100]
httpd            ok
tomcat               ok
sendmail               ok
web02[192.168.2.101]
httpd            ok
postfix               ok
web03[192.168.2.102]
mysqld            ok
httpd               ok
$ awk '/^web/{T=$0;next;} {print T":\t"$0;}' data
web01[192.168.2.100]:   httpd            ok
web01[192.168.2.100]:   tomcat               ok
web01[192.168.2.100]:   sendmail               ok
web02[192.168.2.101]:   httpd            ok
web02[192.168.2.101]:   postfix               ok
web03[192.168.2.102]:   mysqld            ok
web03[192.168.2.102]:   httpd               ok
#行首匹配到web时将这一整行赋值给T存储起来，并读入下一行，最后将T和下一行一起print；next 读入下一输入行并从(Main Input中的)第一个规则开始执行脚本。
```

## getline合并文件

```bash
$ cat 1.txt 2.txt 
0011AAA 200.00 20050321
0012BBB 300.00 20050621
0013DDD 400.00 20050622
0014FFF 500.00 20050401
I0011  11111    hhh
I0012  22222    kkk
I0014  55555    ppp
I0017  66666    ttt
$ awk '{printf $0 "\t"; getline < "2.txt" ;print $0}' 1.txt 
0011AAA 200.00 20050321	I0011  11111    hhh
0012BBB 300.00 20050621	I0012  22222    kkk
0013DDD 400.00 20050622	I0014  55555    ppp
0014FFF 500.00 20050401	I0017  66666    ttt
# 当其左右无重定向符 | 或 < 时，getline作用于当前文件，读入当前文件的第一行给其后跟的变量var 或$0（无变量）；应该注意到，由于awk在处理getline之前已经读入一行，所以getline得到的返回结果是隔行的。
# 当其左右有重定向符 | 或 < 时，getline则作用于定向输入文件，由于该文件是刚打开，并没有被awk读入一行，只是getline读入，那么getline返回的是该文件的第一行，而不是隔行。
```



```
awk '$1~/^Chr/&&$3=="gene"{a=substr($9,4,14);print $1 "\t" $4 "\t" $5 "\t" a}' A2_gene.gff3 |sort -k1,1 -k2n,2|uniq>A2_only_gene.bed #提取基因
awk '$0~/Alignment/{split($0,a," ");b=substr(a[6],3)}$1~/^[^#]/{print $0"\t"b}' A2_AD1/A2_AD1.collinearity |awk -F "\t" '$2~/Garb/&&$3~/Ghir/{split($1,a,"-");print a[1]"\t"$2"\t"$3"\t"$5}' |sort -t $'\t' -k2,2 -k4,4nr|awk -F "\t" '{print $1,$3,$4,$2}' OFS="\t"|uniq -f3|awk -F "\t" '{print $1,$3,$4,$2}' OFS="\t"|uniq -f3|awk '{a=substr($3,1,14);b=substr($4,1,15);print a "\t" b}' #寻找同源基因
```

