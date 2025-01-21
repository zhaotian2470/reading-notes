# Language Mechanics On Stacks And Pointers
## Introduction
    概念：
  * Frame Boundaries：Go函数调用时，参数按值传递（by value）,因此被调用函数（callee function）的改动不影响调用函数（caller function）;
  * Pointer Types：指针类型都属于unnamed types，用来共享值（share a value）

# Language Mechanics On Escape Analysis
## Introduction
    概念：
* Escape analysis：编译器决定把变量保存在哪儿的逻辑，例如stack，heap；
* Heap：变量保存到heap中，可以跨函数共享，但是需要付出额外代价（例如GC等）；

## Practice
    操作命令：
* go build添加参数"-m"，可以看到逃逸分析的日志（"-m -m"可以看到更多）
```
go build -gcflags "-m -m"
```

# Language Mechanics On Memory Profiling
## Introduction
    操作命令：
* 性能测试时添加参数"-benchmem"，可以显示逃逸内存分配情况
```
go test -run none -bench AlgorithmOne -benchtime 3s -benchmem
```
* 性能测试时添加参数"-memprofile"，可以生成pprof文件
```
go test -run none -bench AlgorithmOne -benchtime 3s -benchmem -memprofile mem.out
```
* go tool pprof可以分析pprof文件的逃逸内存分配情况（"-alloc_space" 表示查看分配情况，缺省"-inuse_space"表示查看使用情况）
```
go tool pprof -alloc_space memcpu.test mem.out
```

# Design Philosophy On Data And Semantics
## Design Philosophies
* Mental Models：设计程序时，需要有一个Mental Models，否则程序不好理解；
* Data Oriented Design：设计程序时，需要以数据为中心；类型表示一个数据，并且保持数据的完整性；类型的方法表示数据的能力；多态表示程序操作不同数据时，对应的行为也不同；
* Prototype First Approach：设计程序时，先做原型，再重构代码；

## Semantic Guidelines
    在设计数据时，首先确定语义是值（value）或者指针（pointer），然后所有地方保持一致：
* 函数：参数，返回值；
* 方法：接收者；
* 少数例外：例如Unmarshaling总使用指针语义；

    语义选择建议：
* Built-In Types： value semantics；
* Reference Types： value semantics；
* User Defined Types：建议和factory function保持一致；

# Reference
* [Language Mechanics On Stacks And Pointers](https://www.ardanlabs.com/blog/2017/05/language-mechanics-on-stacks-and-pointers.html)
* [Language Mechanics On Escape Analysis](https://www.ardanlabs.com/blog/2017/05/language-mechanics-on-escape-analysis.html)
* [Language Mechanics On Memory Profiling](https://www.ardanlabs.com/blog/2017/06/language-mechanics-on-memory-profiling.html)
* [Design Philosophy On Data And Semantics](https://www.ardanlabs.com/blog/2017/06/design-philosophy-on-data-and-semantics.html)
