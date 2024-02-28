# 有用命令
## 计算占用内存
pmap -q -x 1 | egrep -v "\[ (anon)|(stack) \]" | awk '/[0-9]/{print $3}' | egrep '^[0-9]*$' | awk '{s+=$1} END {print s}'
