# OS Scheduler
## Summary
* 程序（Program）运行时创建进程（Process），每个进程有初始线程（Thread），随后可以创建更多的线程；
* 操作系统的调度器（OS Scheduler）在调度时，以线程为基本单位，并且需要考虑多重因素，例如处理器（Processor）和核心（Core）数量、CPU caches和NUMA；

## Detail
* 机器中，既有所有处理器（Processor）公用的内存（memory），也有每个处理器专用的cache，甚至是每个核心（Core）cache，所有的存储需要同步从而保证数据的一致性；
* 线程的状态包括：等待（waiting，可能是因为硬件、操作系统的系统调用、同步调用），可执行（runnable），执行（executing）；
* 工作类型包括：CPU-Bound，IO-Bound；
* 执行线程时，PC（program counter）寄存器（也叫做IP寄存器，instruction pointer）保存下一条需要执行的指令。在下面的stack trace中，`+0x39`和`0+x72`都是PC寄存器的值，分别表示针对函数`example`/`main`的距离（offset）， 
```
goroutine 1 [running]:
   main.example(0xc000042748, 0x2, 0x4, 0x106abae, 0x5, 0xa)
       stack_trace/example1/example1.go:13 +0x39                 <- LOOK HERE
   main.main()
       stack_trace/example1/example1.go:8 +0x72                  <- LOOK HERE
```
* 上下文切换（context switching）：当前主流操作系统（包括Windows，Linux，Mac），都使用抢占式调度（preemptive scheduler），每次调度需要一次上下文切换（从当前线程用户态进入内核态，再切换到另外一个线程的用户态，涉及到寄存器的保存，CPU cache的失效，虚拟地址空间失效，需要占用12k-18k指令）；

## Conclusion
* 传统语言中，程序员需要控制线程数（太多的话上下文切换占用时间，太少的话CPU没有充分利用）。一般可以使用线程池（thread pools），并且凭借经验硬编码线程数量，例如对于IO-Bound工作类型，每个core配置3个线程；

# Go Scheduler
## Introduction
    基本概念：
* P（Processor）：对应着硬件线程数，例如1CPU 4Core并且有Hyper-Threading（1Core包括两个硬件线程）的机器，P为8；
* M（Machine）：对应着操作系统线程数，需要在Core上执行；
* G（Goroutine）：对应着用户线程数，需要在M上执行；
* LRQ(Local Run Queue)：本地执行队列，每个P上的G组成；
* GRQ(Global Run Queue)：全局执行队列，没有P的G组成；


  自己的推测：
* G和P的绑定：正在执行的G，需要和P绑定；可以执行的G，或者和P绑定，或者在GRQ中；
* G和M的绑定：正在执行的G，需要和M绑定；等待状态的G，或者和M绑定（例如同步I/O调用），或者在其他地方（例如异步网络调用的G在Net Poller）；
* M和P的绑定：正在执行G的M，需要和P绑定；没有执行G的M，不需要和P绑定；

## Detail
  Go Scheduler调度方式：
* Cooperating Scheduler：低版本Go采用的调度方式，会导致tight loop程序不能中断；
* Preemptive Scheduler：高版本Go采用的调度方式；


  Goroutine的状态：
* Waiting：等待状态，例如操作系统的系统调用，同步调用等；
* Runnable：可运行状态，等待调度；
* Executing：执行状态；

  Context Switching：
* context switching的来源包括：go创建新线程；gc；系统调用；同步调用；
* context switching发生在用户代码，不需要切换到内核态代码，因此性能较高：例如操作系统的上下文切换需要执行12k指令，go的上下文切换至需要2.4k指令；

## Scenario
### 异步系统调用（以网络操作为例）：
1. G1发起网络操作；
2. G1从当前的M中移除，放到Net Poller上，并且另外一个线程G2在M上执行；
3. G1的网络操作完毕后，重新放到M对应P的LRQ；


### 同步系统调用（以I/O操作为例）：
1. G1发起I/O操作；
2. G1、M1一起从当前的P中移除，P绑定新M2并且运行G2；
3. G1的I/O操作完毕后，重新放到P的LRQ，M1被空置；


### 工作窃取：
* P可能从GRQ中窃取G，放到本地LRQ；
* 如果P运行完毕，会从其它P的LRQ、GRQ窃取G，还可能查看网络情况；

## Conclusion
    go scheduler将IO-Bound任务转换为CPU-Bound任务，因此执行任务的操作系统线程（OS Threads）数，只需要和虚拟核心（virtual cores）数相同就行。

# Concurrency
## Introduction
    基本概念：
* Concurrency：并发，就是说不能保证指令的顺序；
* Parallelism：并行，就是说多个指令同时进行；

## Conclusion
    场景分析：
* CPU-Bound任务，只有在Parallelism时才能提升：例如将很多数字相加；
* IO-Bound任务，Concurrency就可以提升：例如读取文件；
* 一些任务，似乎只适合顺序执行：例如排序；

# Reference
* [Scheduling In Go : Part I - OS Scheduler](https://www.ardanlabs.com/blog/2018/08/scheduling-in-go-part1.html)
* [Scheduling In Go : Part II - Go Scheduler](https://www.ardanlabs.com/blog/2018/08/scheduling-in-go-part2.html)
* [Scheduling In Go : Part III - Concurrency](https://www.ardanlabs.com/blog/2018/12/scheduling-in-go-part3.html)

