# 工具
## java
一些有用的命令：
* java -XX:+UnlockDiagnosticVMOptions -XX:NativeMemoryTracking=summary -XX:+PrintNMTStatistics -version # Thread Memory Consumption on Java 8

## jmap
一些有用的命令：
* jmap -dump:live,format=b,file=dump.hprof 1 # dump heap

## jstack
* jstack -l 1 # show java stack, if you want show thread status, use: jstack 1 | egrep "java.lang.Thread.State" | sort | uniq -c

## jstat
* jstat -gc 1 # show gc status for pid 1

# 有用链接
* [Java (JVM) Memory Model - Memory Management in Java](https://www.digitalocean.com/community/tutorials/java-jvm-memory-model-memory-management-in-java)
