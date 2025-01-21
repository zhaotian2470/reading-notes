# Concept
    概念解释：
* Physical Memory：计算机里面的物理内存；
* Virtual Memory：在操作系统管理下的虚拟地址空间里，通过MMU按照Page映射到物理存储（物理内存/硬盘缓存）；
* Page：虚拟内存映射到物理内存的基本单位，例如8K；
* Memory Allocator：内存分配器，通过系统调用（例如brk）请求内存，从而满足分配内存的请求；

# TCMalloc
    TCMalloc（Thread Cache Malloc）的设计目标：
* memory fragment：减少内存碎片；
* thread cache：提升并发请求性能；

    TCMalloc的设计思路：
* class size：将请求内存的大小调整为固定的几类，在不浪费很多内存的前提下，减少碎片化；
* mspan：由连续的page组成，可以按照class size区分，也可以按照页面数区分；
* mcache：线程本地内存，包括所有class size的mspan，不需要加锁就能访问；为了方便GC，每个class size的mspan又分为scan（包含指针）/noscan（没有指针）；
* mcentral：包括所有class size的mspan；为了方便分配内存，每个class size的mspan又分为empty（没有free object，或者被mcache分配）/nonempty（有free object）；
* mheap：一个全局的mheap管理整个堆，除了包括mcentral之外，还包括按照页面数区分的空闲/非空闲mspan；
* arena：虚拟地址段单位，例如64M；

    TCMalloc的分配路径：
* 分配起点：小对象（例如<=32K）是mcache，大对象（例如>32K）是mheap；
* mcache缺少内存时，从mcentral获取；
* mcentral缺少内存时，从mheap获取；
* mheap缺少内存时，通过系统调用（例如brk）从系统获取；
