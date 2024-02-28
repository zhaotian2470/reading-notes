# 工具
## java
一些有用的命令：
* java -XX:+UnlockDiagnosticVMOptions -XX:NativeMemoryTracking=summary -XX:+PrintNMTStatistics -version # Thread Memory Consumption on Java 8

## jmap
一些有用的命令：
* jmap -dump:live,format=b,file=dump.hprof 1 # dump heap

# 有用链接
* [Java (JVM) Memory Model - Memory Management in Java](https://www.digitalocean.com/community/tutorials/java-jvm-memory-model-memory-management-in-java)
