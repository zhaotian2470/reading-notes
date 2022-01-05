# The Big Picture
Linux系统可以分成三层：
* 硬件（hardware）：CPU, memory, disks, network ports；
* 内核（kernel）：process management, memory management, device drivers, system calls；
* 用户进程（user process）：servers, shell, GUI；

## The Kernel
上下文切换（context swith）时，需要做进程管理(process management)。下面我们简单说明用户态（user mode）进程时间片到了后，上下文切换的过程：
1. CPU（真实硬件）使用定时器中断进程，将进程切换到内核态（kernel mode）；
1. 内核保存进程的CPU/内存信息，用于以后重新启动进程；
1. 内核处理上一个时间片中积累的任务，例如：收集输入/输出，I/O，其它操作的数据；
1. 内核选定需要执行的任务；
1. 内核给新进程准备CPU、内存等资源；
1. 内核告诉CPU新进程的时间片长度；
1. 内核将CPU切换到用户态，并且执行新进程；

上下文切换（context swith）时，需要做内存管理(memory management)，具体包括：
* 当物理内存不够时，系统可以使用硬盘空间补充；
* 内核、用户进程需要使用不同的内存地址空间；
* 每个用户进程需要使用不同的内存地址空间；
* 用户进程不能访问其它用户进程的私有地址空间；
* 用户进程间可以共享内存；
* 用户进程的一些内存是只读内存；
在现代CPU中，进程不会直接访问物理内存(physical memory)，而是通过memory management unit（MMU）访问虚拟内存（virtual memory），从而让每个进程觉得自己拥有所有的内存。

# Basic commands and directory hierarchy
man手册的分类：
* 1: user commands；
* 2: system calls；
* 3: higher-level library documentation；
* 4: device interface and driver information；
* 5: system configuration files；
* 6: games；
* 7: File formats, conventions, and encodings (ASCII, suffixes, and so on)；
* 8: system commands；

linux重要目录：
* /bin: basic Unix commands；
* /dev: device files；
* /etc: configuration directory；
* /home: personal directories；
* /lib: shared libraries；
* /proc: system statistics and process information；
* /sys: device and system interface；
* /sbin: system commands；
* /tmp: temporary files, most distributions clear /tmp whe the machine boots；
* /usr: contain a large directory hierarchy like "/"；
    * /include：header files；
    * /info：GNU info manuals；
    * /local: local directory, contain directory hierarchy like "/" or "/user"；
    * /man: manual pages；
    * /share：shared directory；
* /var: variable subdirectory, such as logging, user tracking, caches；

linux其它目录：
* /boot：boot loader files；
* /media: removable media, such as flash drivers；
* /opt: additional third-party software；

备注：
* shell通配符(shell globbing, wildcasts)规则，和正则表达式（regex）不一样；
* 如果需要寻找命令，使用：man -k <keyword>；
* linux命令的报错风格，是以":"分隔的短句，例如：ls: cannot access /dsafsda: No such file or directory；

# Devices
文件类型包括：
* f：文件；
* d：目录；
* l：符号链接；
* c: 字符设备；
* b：块设备；
* p：管道；
* s：套接字；
设备可能是后边4种类型

设备的处理流程：
1. /proc/devices：包括驱动程序、主设备号的映射管理；
1. 当启动机器/硬件发生变动时，内核维护/sys目录（可能还维护/dev目录），并且发送信息；
1. udevd收到内核信息时，维护/dev目录，并且发送消息；
1. 用户程序udevd消息，并且执行用户操作：例如挂载文件系统；

设备的处理命令：
* udevadm；
* udevd；
* dd；
* lsscis；
* mknod；

# Disks and Filesystems
文件系统概述：
* 每块磁盘可能包括多个分区；
    * BIOS系统常用分区：MBR（master boot record）+ 分区；
	* UEFI系统常用分区：GPT（globally unique identifier partition table）+ 分区；
* linux包括vfs（virtual filesystem），屏蔽下层文件系统（例如ext4, iso9660，fat, hfs+, ），从而提供统一接口。每个文件系统存储在一个分区上，以ext为例：
    * super block(all block groups have the same super block)：boot information；
	* inodes(in block groups): directory information；
	* data(in block groups): file data；

文件系统处理命令：
* 分区命令：parted, gparted, blkid；
* 文件系统命令：mkfs, mount, fsck, mkswap, swapon；

# How the Linux Kernel Boots
linux启动流程：
1. 机器开机时，BIOS/UEFI运行boot loader；
1. boot loader运行内核；
1. 内核初始化设备和驱动程序；
1. 内核挂载root文件系统；
1. 内核启动init进程；
1. init进程启动其它的系统进程；
1. init进程启动用户登录进程；

# How User Space Starts
用户进程启动流程：
1. init；
1. low-level services, such as udevd, syslogd；
1. network；
1. mid- and high-level services, such as cron, printing；
1. login prompts, GUIs, and other high-level applications；

init的类别：
* system V init：老的init；
* systemd：新的init，支持并行启动；
* upstart：老版本debian在用，也在迁移到systemd；

symstemd命令：
* systemctl；
* shutdown；
* init；

# SystEm Configuration: Logging, System Time, Batch, Jobs, and Users
/etc目录：
* 老的配置风格：每个应用程序在/etc目录下放一个文件；
* 新的配置风格：每个应用程序在/etc目录下放一个子目录，例如: /etc/systemd

建议使用NTP同步时间：
* hwclock/real time clock：每台机器维护的硬件时间，断电时由电池供电；
* system clock：内核时间；

执行脚本：
* cront：定期执行；
* at：执行一次；

user id的认证，通过PAM（pluggable authentication modules）来实现：
* real user id: 启动进程的id，整个会话期间不会修改；
* saved user id：当启动saved user id的进程时，保存原来的effective user id；
* effective user id: 验证权限时，大部分使用该id；通过API，可以将real/saved user id设置为effective user id；


# A Closer Look at Processes and Resource utilization
对于崩溃的程序，可以使用如下工具跟踪原因：
* ltrace：跟踪动态库函数调用；
* strace：跟踪系统调用；

对于正在运行的程序，可以使用如下工具获取瞬时信息：
* ps：cpu/memory；
* top：cpu/memory；
* iotop：io；
* lsof: io/network；

对于正在运行的程序，可以使用如下工具获取短期统计信息：
* vmstat：cpu/memory；
* iostat：io；
* netstat：net；
* pidstat：process；

对于长期运行的程序，可以使用如下工具获取长期趋势：
* sar（system activity report）；
* acct（process accounting）；

备注：
* page fault分为两种：minor（内存地址映射不存在，不影响性能），major（内存页不存在，影响性能）；

# Understand Your Network and Configuration
linux通过network configration manager管理网络，对应工具：
* ethtool；
* arp；
* ip：替代命令ifconfig/iw，route；
* dig；
* ping/tracepath；


# Network Applications and Services
对应工具：
* lsof/netstat；
* tcpdump；
* iptables；
* netcat；
* telnet；
* nmap；

# Introduction to Shell Scripts

# Move Files across the Network
拷贝文件的手段：
* scp；
* rsync；

共享文件的手段：
* samba；
* nfs；

# User Environments
使用bash时，分为交互式(interactive)和非交互式（no interactive）。一般来说，只有交互式bash脚本需要运行启动脚本：
* login session运行第一个脚本：.bash_profile, .bash_login, .profile；
* no login：.bashrc；

# A Brief Survey of The Linux Desktop
以X Window System为例，Linux桌面分为两大部分：
* X Server：负责和硬件的交互，例如接收鼠标事件、键盘事件等，并且向屏幕发送绘画事件；X Client只能和X Server交互，不能和硬件直接交互；
* X Client：负责和X Server交互：获取鼠标、键盘等事件，并且发送绘画事件；
    * Window Manager：控制窗口相关的功能，例如窗口的位置、大小等；
	* Toolkits：窗口控件，例如button、menu等，Linux的Toolkits包括GTK+（for GNOME）， QT（for KDE）；
	* Desktop Envrionments：负责桌面程序之间的相互通信（例如通过D-bus），Linux的桌面环境包括GNOME，KDE；
	* X Application：应用程序，例如xclock、chrome等；

X Windows System的其它概念：
* Display Manager：负责启动X Server以及其它相关服务，例如：login box、window manager、file manager等，Linux的Display包括gdm（for GNOME）、kdm(for KDE)；
* Network Transparence：X Client/Server之间的通信，可以通过本地IPC，也可以是网络；

X Windows System相关命令：
* xwininfo；
* xev；
* xinput；
* xset；

D-bus可以分为两种：
* system instance：开机时启动，可以被系统进程使用；
* user instance：每次用户登录时启动一个instance，将要修改为每个用户一个instance；

D-bus相关命令：
* dbus-monitor；

# Development Tools
C 编译工具：
* cpp；
* gcc/cc：默认表示链接，-c表示编译；
* ldd；
* ldconfig；
* make；
* gdb；
* lex/flex：词法分析；
* yacc/bison：语法分析；

编译工具的展望：
* LLVM项目：将编译工作分为前端（将源语言翻译为IR）、中间端（IR优化）、后端（将IR翻译为对应平台的汇编）、链接器（链接程序）等；

# Introduction to Compiling Software from C Source File
将C的源文件编译为软件的通用步骤：
* tar：解压缩，并且注意文件README、INSTALL等；
* configure：生成Makefile；
* make：编译；
* make install：安装；

相关命令：
* patch；
* pkg-config：查看包提供的库；
* dpkg --list-files <package>/rpm -ql <pakcage>：查看软件包安装位置；

# Building on the Basics
