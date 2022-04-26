# 常用命令
tcpdump -i bond1 -XX port 9121  # 指定端口数据

# 常用参数
* -i bond1：指定接口bond1；
* -X：使用hex/ASCII两种方式，打印包头，包体；
* -XX：使用hex/ASCII两种方式，打印数据链路头，包头，包体；

# 常用过滤条件
* port 9121：端口9121；
