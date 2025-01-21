# Introduction
    诊断Go程序的手段包括：
* Profiling：展示程序整体性能，例如CPU，内存；
* Tracing：展示一个请求/函数的情况，例如延迟；
* Debugging：展示程序运行状态，用于查看程序是否正确等；
* Runtime statistics and events：展示统计和事件，用于程序监控；
    各种诊断手段可能相互影响，因此建议每次使用一种手段。

# Profiling

## Concept
    Go Profiling包括如下方面：
* CPU；
* Heap；
* ThreadCreate：OS线程的创建；
* Goroutine；
* Block：同步原语（例如timer，channel）上的等待；
* Mutex：锁竞争；

    Profiling的手段：
* Go Profiling: 代码（[net/http/pprof](https://go.dev/pkg/net/http/pprof/)或者[runtime/pprof](https://go.dev/pkg/runtime/pprof)）go test产生，go tool pprof展示;
* OS Profiling：Linux的[perf tools](https://perf.wiki.kernel.org/index.php/Tutorial)，macOS的[Instruments](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/)

## Examples
    Profiling数据的产生：
* 对于go test命令，添加参数：cpuprofile、 -memprofile ；
* 对于go程序，添加如下代码将会在http endpoint "/debug/pprof/"展示profiling数据；
```
import _ "net/http/pprof"
```
* 对于go程序，添加如下代码导出pprof文件（cpu profile按照一定频率采样（例如100hz），每个样本只保留部分调用函数（例如100个））：
```
var cpuprofile = flag.String("cpuprofile", "", "write cpu profile to file")
var memprofile = flag.String("memprofile", "", "write memory profile to this file")

func main() {
    flag.Parse()
    if *cpuprofile != "" {
        f, err := os.Create(*cpuprofile)
        if err != nil {
            log.Fatal(err)
        }
        pprof.StartCPUProfile(f)
        defer pprof.StopCPUProfile()
    }
    if *memprofile != "" {
        f, err := os.Create(*memprofile)
        if err != nil {
            log.Fatal(err)
        }
        pprof.WriteHeapProfile(f)
        f.Close()
        return
    }
    ...
```

    Profiling数据的分析：
* 入口命令（有很多参数，例如--nodefraction表示隐藏占比小的节点）：
```
go tool pprof <binary> <prof file>
go tool pprof <format> <binary> <prof file>
go tool pprof -http <ip>:<port> <binary> <prof file> # 可以看到火焰图

go tool pprof http://localhost:6060/debug/pprof/profile   # 30-second CPU profile
go tool pprof http://localhost:6060/debug/pprof/heap      # heap profile
go tool pprof http://localhost:6060/debug/pprof/block     # goroutine blocking profile
```
* top：展示占用资源最多的函数，默认是flat，接收参数-cum；
* list：展示每行代码资源占用情况；
* web：展示svg图片；

    Profiling转换为火焰图的操作命令：
```
go tool pprof -raw -output=cpu.txt havlak1 havlak1.prof
~/go/src/github.com/brendangregg/FlameGraph/stackcollapse-go.pl cpu.txt | ~/go/src/github.com/brendangregg/FlameGraph/flamegraph.pl > tmp.svg
```

## Refrence
* https://go.dev/blog/pprof

# Tracing
* 单个节点：golang提供包"golang.org/x/net/trace"，用于分析所有函数调用链的延迟；
* 多个节点：使用context.Context和http协议头交互，在多个节点之间传递信息；

# Debugging
    调试golang时，有两种调试器：
* Delve：golang调试器，推荐使用；
* GDB：能够调试golang，但是不理想；

## Delve
    编译golang时，建议采用以下参数：
```
go build -gcflags=all="-N -l" # 关闭优化
go build -gcflags="-dwarflocationlists=true" # 协助调试优化后的程序
```

如果希望用delve调试core dump，参考 [Go Wiki: CoreDumpDebugging](https://go.dev/wiki/CoreDumpDebugging)
* delve可以跨平台调试core dump：如果保留Linux平台下的二进制文件、core文件，就可以再macOS上使用delve调试；
* macOS上使用delve上调试时，dump出来的core特别大（例如5G），因此不建议使用；

# Runtime statistics and events
## Stats and States
-   [runtime.ReadMemStats](https://go.dev/pkg/runtime/#ReadMemStats)  堆内存分配和GC相关数据；
-   [debug.ReadGCStats](https://go.dev/pkg/runtime/debug/#ReadGCStats)  GC相关数据；
-   [debug.Stack](https://go.dev/pkg/runtime/debug/#Stack)  栈相关的数据；
-   [debug.WriteHeapDump](https://go.dev/pkg/runtime/debug/#WriteHeapDump)  heap dump；
-   [runtime.NumGoroutine](https://go.dev/pkg/runtime#NumGoroutine)  协程相关的数据；

## Execution tracer
* 产生数据：
```
```
* 分析数据：
```
go tool trace
```

## GODEBUG
GODEBUG环境变量配置：
-   GODEBUG=gctrace=1：显示GC；
-   GODEBUG=inittrace=1：显示包初始化时的执行时间、内存情况；
-   GODEBUG=schedtrace=X：显示调度事件；
-   GODEBUG=cpu.all=off 关闭所有的优化指令；
-   GODEBUG=cpu.extension=off 关闭特定的优化指令，其中extension是指令的小写，例如sse41、avx；

## Prometheus
如果需要配置prometheus，参考 [Prometheus Monitoring with Golang](https://medium.com/devbulls/prometheus-monitoring-with-golang-c0ec035a6e37)

# Reference
* [diagnostics](https://go.dev/doc/diagnostics)
