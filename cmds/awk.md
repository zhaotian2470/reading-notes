# 有用命令
## 合并行
合并拥有相同的第一列的所有行，脚本类似
```awk
#!/usr/bin/awk -f
BEGIN {
    FS="	"
    OFS=","
}

{
    # a: key is first column, value is other columns concated by OFS
    # b: key is first column, value is line count
    # o: key is nature number, value is first column
    # mx: max key for o
    a[$1]=($1 in a ? a[$1] OFS substr($0,index($0, FS)+1) : $0);

    if(!($1 in b)){ o[++i]=$1 }; 
    b[$1]++; 

    mx=mx>b[$1]?mx:b[$1] 
}

END {
    for(i=1; i in o; i++)
        print a[o[i]]
}
```
