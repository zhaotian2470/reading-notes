# Semantics
## Introduction
    GC（Garbage Collector）相关概念：
* GC负责heap上内存的分配、回收：GC不需要处理stack上内存；
* Go的GC使用tri-color mark and sweep collector

## Collector Behavior
    GC过程如下：
* Mark setup：STW（Stop The World）；
* Marking：concurrent，可能会启动一些MA（Mark Assist）协程，帮助GC；
* Mark termination：STW（Stop The World）；
* Sweep：concurrent，协程在分配内存时擦除不需要的内存；
    上述每个过程，都会降低程序的执行速度。

## GC Trace
    运行go程序时，添加环境变量"GODEBUG=gctrace=1"或者"GODEBUG=gctrace=1,gcpacertrace=1"就可以将gc情况显示到stderr。下面是对一行信息的分析：
```
gc 1405 @6.068s 11%: 0.058+1.2+0.083 ms clock, 0.70+2.5/1.5/0+0.99 ms cpu, 7->11->6 MB, 10 MB goal, 12 P

// General
gc 1404     : The 1404 GC run since the program started
@6.068s     : Six seconds since the program started
11%         : Eleven percent of the available CPU so far has been spent in GC

// Wall-Clock
0.058ms     : STW        : Mark Start       - Write Barrier on
1.2ms       : Concurrent : Marking
0.083ms     : STW        : Mark Termination - Write Barrier off and clean up

// CPU Time
0.70ms      : STW        : Mark Start
2.5ms       : Concurrent : Mark - Assist Time (GC performed in line with allocation)
1.5ms       : Concurrent : Mark - Background GC time
0ms         : Concurrent : Mark - Idle GC time
0.99ms      : STW        : Mark Term

// Memory
7MB         : Heap memory in-use before the Marking started
11MB        : Heap memory in-use after the Marking finished
6MB         : Heap memory marked as live after the Marking finished
10MB        : Collection goal for heap memory in-use after Marking finished

// Threads
12P         : Number of logical processors or threads used to run Goroutines
```

## Pacing
    环境变量GOGC，可以控制GC的频率（pacing）：缺省值一般为 `100`，表示当堆使用量相对于上一次 GC 后的大小增加 100% 时触发下一次 GC。

## Conclusion
    为了提升程序速度，不应该减少GC的频率（pacing），而应该少使用堆。

# GC Traces
## Running The Application
    运行时添加环境变量，控制GC的显示：
* GOGC=off：不进行gc；
* GODEBUG=gctrace=1：显示gc信息；
* GODEBUG=gctrace=1,gcpacertrace=1：显示更多gc信息；

    运行时展示pprof：
* 添加如下代码，可以将pprof信息绑定到缺省Web服务器的/debug/pprof/allocs：
```
import net/http/pprof
```
* 使用命令查看pprof：
```
go tool pprof http://<ip>:<port>/debug/pprof/allocs
```
* 在pprof中使用命令分析：
```
top 6 -cum # 显示6个分配堆内存最多的函数
list rssSearch # 显示函数分配堆内存的情况
```

    如果想给Web Server做压测，可以使用命令：hey

## Conclusion
    如果想提升go程序性能，需要减少堆内存的使用；

# GC Pacing
## Concurrent Example Code
* 添加如下代码，可以将trace信息输出到stdout：
```
import "runtime/trace"

func main() {
     trace.Start(os.Stdout)
     defer trace.Stop()
```
* 使用下面的命令，可以分析trace情况：
```
go tool trace t.out
```
输出类似：
![](https://www.ardanlabs.com/images/goinggo/103_figure2.png?v2)

## Conclusion
    虽然go的协程代价很少，但是也不要无限制开，需要注意控制数量。

# Reference
[Semantics](https://www.ardanlabs.com/blog/2018/12/garbage-collection-in-go-part1-semantics.html)
[Traces](https://www.ardanlabs.com/blog/2019/05/garbage-collection-in-go-part2-gctraces.html)
[GC Pacing](https://www.ardanlabs.com/blog/2019/07/garbage-collection-in-go-part3-gcpacing.html)
