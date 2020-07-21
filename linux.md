#### Top命令字段

us：用户态使用的cpu时间比
sy：系统态使用的cpu时间比
ni：用做nice加权的进程分配的用户态cpu时间比
id：空闲的cpu时间比
wa：cpu等待磁盘写入完成时间
hi：硬中断消耗时间
si：软中断消耗时间
st：虚拟机偷取时间



查看负载均衡

`1.42, 1.58, 1.44 1/331 10135`  

前三个数字是1、5、15分钟内的平均进程数，分子是正在运行的进程数，分母是进程总数，另一个是最近运行的进程ID号

**“有多少核心即为有多少负荷”法则： 在多核处理中，你的系统均值不应该高于处理器核心的总数
量。**两颗四核的处理器 等于 四个双核处理器 等于 八个单处理器。所以，它应该有八个处理器内核

#### 进程调度算法

目的：高资源利用率、高吞吐量、用户满意等原则

进程调度所采用的算法是与整个系统的设计目标相一致的：

1）.批处理系统：增加系统吞吐量和提高系统资源利用率。

2）.分时系统：保证每个分时用户能容忍的响应时间。

3）.实时系统：保证对随机发生的外部事件做出实时响应。

1) 非抢占式优先权算法

在这种方式下，系统一旦把处理机分配给就绪队列中优先权最高的进程后，**该进程便一直执行下去，直至完成；或因发生某事件使该进程放弃处理机时，系统方可再将处理机重新分配给另一优先权最高的进程**。**这种调度算法主要用于批处理系统中**；也可用于某些对实时性要求不严的实时系统中。

**让进程运行直到结束或阻塞的调度方式   容易实现   适合专用系统，不适合通用系统**   

2) 抢占式优先权调度算法

在这种方式下，系统同样是把处理机分配给优先权最高的进程，使之执行。**但在其执行期间，只要又出现了另一个其优先权更高的进程，进程调度程序就立即停止当前进程(原优先权最高的进程)的执行**，重新将处理机分配给新到的优先权最高的进程。因此，在采用这种调度算法时，是每当系统中出现一个新的就绪进程i 时，就将其优先权Pi与正在执行的进程j 的优先权Pj进行比较。如果Pi≤Pj，原进程Pj便继续执行；但如果是Pi>Pj，则立即停止Pj的执行，做进程切换，使i 进程投入执行。显然，**这种抢占式的优先权调度算法能更好地满足紧迫作业的要求，故而常用于要求比较严格的实时系统中，以及对性能要求较高的批处理和分时系统中**。

**允许将逻辑上可继续运行的在运行过程暂停的调度方式   可防止单一进程长时间独占CPU ，  系统开销大（降低途径：硬件实现进程切换，或扩充主存以贮存大部分程序）**

##### 先来先服务（FCFS)

按照作业提交或进程变为就绪状态的先后次序，分派CPU； 当前作业或进程占用CPU，直到执行完或阻塞，才出让CPU（非抢占方式）。 在作业或进程唤醒后（如I/O完成），并不立即恢复执行，通常等到当前作业或进程出让CPU。

##### 高优先权优先调度算法

为了照顾紧迫型作业，使之在进入系统后便获得优先处理，引入了最高优先权优先（FPF）调度算法。此算法常被用于批处理系统中，作为作业调度算法，也作为多种操作系统中的进程调度算法，还可用于实时系统中。当把该算法用于作业调度时，系统将从后备队列中选择若干个优先权最高的作业装入内存。当用于进程调度时，该算法是把处理机分配给就绪队列中优先权最高的进程。这时，有可进一步把该算法分为：**非抢占式优先权算法、抢占式优先权调度算法**。区别在于：非抢占式优先权算法下，系统一旦把处理机分配给就绪队列中优先权最高的进程后会一直执行到完成。而抢占式则在分配后进程执行过程中如果又出现了更高优先权的进程后，进程调度程序会立即停止当前进程，重新将处理机分配给新到的优先权最高的进程

##### 基于时间片的轮转调度算法

在早期的时间片轮换法中，系统将所有的就绪进程按先来先服务的原则排成一个队列，每次调度时，把CPU分配给队首进程，并令其执行一个时间片。时间片的大小从几ms到几百ms。当执行的时间片用完时，有一个计数器发出时钟中断请求，调度程序便据此信号来停止该进程的执行，并将它送往就绪队列的末尾；然后再把处理机分配给就绪队列中新的队首进程，同时也让他执行一个时间片。**这样就可以保证就绪队列中的所有进程在一给定时间内均能获得一时间片的处理机执行时间。换言之，系统能在给定时间内响应所有用户的请求**。

##### 多级反馈队列调度算法

多级反馈队列算法是时间轮转算法和优先级算法的综合和发展，为提高系统吞吐量和缩短平均周转时间而照顾短进程。为获得较好的I/O设备利用率和缩短响应时间而照顾I/O型进程。不必估计进程的执行时间，动态调节。

**应设置多个就绪队列**，并为各个队列赋予不同的优先级，第1级队列的优先级最高，第2级队列次之，其余队列的优先级逐次降低。

**赋予各个队列中进程执行时间片的大小也各不相同**，在优先级越高的队列中，每个进程的运行时间片就越小。例如，第2级队列的时间片要比第1级队列的时间片长一倍，……第i+1级队列的时间片要比第i级队列的时间片长一倍。

**当一个新进程进入内存后，首先将它放入第1级队列的末尾，按FCFS原则排队等待调度。当轮到该进程执行时，如它能在该时间片内完成，便可准备撤离系统；如果它在一个时间片结束时尚未完成，调度程序便将该进程转入第2级队列的末尾，再同样地按FCFS原则等待调度执行；**如果它在第2级队列中运行一个时间片后仍未完成，再以同样的方法放入第3级队列……如此下去，当一个长进程从第1级队列依次降到第n 级队列后，在第 n 级队列中便釆用时间片轮转的方式运行。

**仅当第1级队列为空时，调度程序才调度第2级队列中的进程运行**；仅当第1 ~(i-1)级队列均为空时，才会调度第i级队列中的进程运行。如果处理机正在执行第i级队列中的某进程时，**又有新进程进入优先级较高的队列（第 1 ~(i-1)中的任何一个队列），则此时新进程将抢占正在运行进程的处理机，即由调度程序把正在运行的进程放回到第i级队列的末尾，把处理机分配给新到的更高优先级的进程**。

- 终端型作业用户：短作业优先
- 短批处理作业用户：周转时间较短
- 长批处理作业用户：经过前面几个队列得到部分执行，不会长期得不到处理

##### 短作业优先（SJF）调度算法

短作业优先(SJF)调度算法是从后备队列中选择一个或若干个估计运行时间最短的作业，将它们调入内存运行。该算法对长作业不利，SJF调度算法中长作业的周转时间会增加。更严重的是，如果有一长作业进入系统的后备队列，由于调度程序总是优先调度那些 (即使是后进来的）短作业，将导致长作业长期不被调度（“饥饿”现象，注意区分“死锁”。后者是系统环形等待，前者是调度策略问题）



#### 空间

为了保证用户进程不能直接操作内核（kernel），保证内核的安全，操心系统将虚拟空间划分为两部分，一部分为内核空间，一部分为用户空间。针对linux操作系统而言，**将最高的1G字节**（从虚拟地址0xC0000000到0xFFFFFFFF），供内核使用，称为内核空间，**而将较低的3G字节**（从虚拟地址0x00000000到0xBFFFFFFF），供各个进程使用，称为用户空间

#### 多路IO复用

全部的[Io](https://segmentfault.com/a/1190000003063859),讲的比较好

操作系统在处理io的时候，主要有两个阶段：①等到数据传到kernel内核space②kernel内核区域将数据复制到user space（理解为进程或者线程的缓冲区）

select/epoll的优势并不是对于单个连接能处理得更快，而是在于能处理更多的连接

select，poll，epoll都是IO多路复用的机制。I/O多路复用就通过一种机制，可以监视多个描述符，一旦某个描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。**但select，poll，epoll本质上都是同步I/O，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。**

##### select

<img src="https://img2018.cnblogs.com/blog/1644918/201907/1644918-20190711210209296-720545504.png" alt="img" style="zoom: 50%;" />

单个进程就可以同时处理多个网络连接的io请求（同时阻塞多个io操作）。基本原理就是程序呼叫select，然后整个程序就阻塞了，这时候，kernel就会轮询检查所有select负责的fd，当找到一个client中的数据准备好了，select就会返回，这个时候程序就会系统调用，将数据从kernel复制到进程缓冲区。

select的几大缺点：

（1）每次调用select，都需要把fd集合从用户态拷贝到内核态，这个开销在fd很多时会很大

（2）同时每次调用select都需要在内核遍历传递进来的所有fd，这个开销在fd很多时也很大

（3）select支持的文件描述符数量太小了，默认是1024

##### poll

poll是链式的，没有最大连接数的限制; poll有一个特点是水平触发，也就是通知程序fd就绪后，这次没有被处理，那么下次poll的时候会再次通知同个fd已经就绪

仍然需要遍历所有监听的文件描述符，可能有大部分是没有事件响应的

每次调用都要把要监听的文件描述符信息从用户空间拷贝到内核空间，也同样要把进程绑定到相应设备的等待队列中

##### epoll

int epoll_create(int size);创建一个epoll的句柄，size用来告诉内核这个监听的数目一共有多大

 int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)先注册要监听的事件类型

 int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout)等待epfd上的io事件，最多返回maxevents个事件

由于事件的注册和监听分离，只有文件描述符产生变动的情况下才会进行从内核到用户空间的内存拷贝以及绑定到等待队列上。而且，当有事件响应之后，响应的事件会以回调的方式把相应的文件描述符插入到一个队列上，因此**内核只需要遍历所有发生响应的文件描述符**，**而不需要遍历所有文件描述符**。

#### 进程和线程

<img src="/Users/kangkang/Library/Application Support/typora-user-images/image-20200619160744923.png" alt="image-20200619160744923" style="zoom:50%;" />

进程只能作为资源拥有的基本单位，这些资源包括内存地址空间，文件描述符等等, 一个进程中的多个线程共享这些资源

<img src="/Users/kangkang/Library/Application Support/typora-user-images/image-20200625111916280.png" alt="image-20200625111916280" style="zoom: 33%;" />

线程成为了CPU调度的基本单位

ps和top默认只能打印进程级别的信息，看不到线程的tid

进程（20992）中创建threads，threads的pid都是进程的pid（20992）, 各自又用自己的pthread id（很长，在系统级别没有意义）号以及lwp(tid，比如两个线程的tid分别是20993，20994)

```bash
PID   TID   LWP  TGID  PGRP   SID TPGID COMMAND
20992 20992 20992 20992 20992 30481 20992 ./threadTest
20992 20993 20993 20992 20992 30481 20992 ./threadTest
20992 20994 20994 20992 20992 30481 20992 ./threadTest
```

- **pid**: 进程ID。
- **lwp**: 线程ID。在用户态的命令(比如ps)中常用的显示方式。
- **tid**: 线程ID，等于lwp。tid在系统提供的接口函数中更常用，比如syscall(SYS_gettid)和syscall(__NR_gettid)。
- **tgid**: 线程组ID，也就是线程组leader的进程ID，等于pid。
- ------分割线------
- **pgid**: 进程组ID，也就是进程组leader的进程ID。
- **pthread id**: pthread库提供的ID，生效范围不在系统级别，可以忽略。
- **sid**: session ID for the session leader。
- **tpgid**: tty process group ID for the process group leader。



#### fork

fork调用的一个奇妙之处就是它仅仅被调用一次，却能够返回两次，它可能有三种不同的返回值：
  1）在父进程中，fork返回新创建子进程的进程ID；
  2）在子进程中，fork返回0；
  3）如果出现错误，fork返回一个负值；

#### 进程之间的通信方式

https://www.cnblogs.com/zgq0/p/8780893.html

##### 管道，通常指无名管道

它是**半双工的**（即数据只能在一个方向上流动），具有固定的读端和写端。

它只能用于具有亲缘关系的进程之间的通信（也是父子进程或者兄弟进程之间）。

**它可以看成是一种特殊的文件**，对于它的读写也可以使用普通的read、write 等函数。但是它不是普通的文件，**并不属于其他任何文件系统，并且只存在于内存中**

当一个管道建立时，它会创建两个文件描述符：`fd[0]`为读而打开，`fd[1]`为写而打开

使用pid_t而不使用int只是为了可移植性好一些. 因为在不同的平台上有可能int,long

int pipe(int filedes[2]); 返回0成功，-1失败

##### FIFO，也称为命名管道，它是一种文件类型

1. FIFO可以在无关的进程之间交换数据，与无名管道不同。
2. FIFO有路径名与之相关联，它以一种**特殊设备文件形式****存在于文件系统中**。

**默认是阻塞的**

在数据读出时，FIFO管道中同时清除数据，并且“先进先出”。

多个客户端可以向服务器写

##### 消息队列：

```c++
// 消息结构
  struct msg_form {
     long mtype;
     char mtext[256];
 };
```

消息结构第一个一定是long,后面的无所谓

```c++
 #include <sys/msg.h>
 // 创建或打开消息队列：成功返回队列ID，失败返回-1
 int msgget(key_t key, int flag);
 // 添加消息：成功返回0，失败返回-1
 int msgsnd(int msqid, const void *ptr, size_t size, int flag);
 // 读取消息：成功返回消息数据的长度，失败返回-1
 int msgrcv(int msqid, void *ptr, size_t size, long type,int flag);
 // 控制消息队列：成功返回0，失败返回-1
 int msgctl(int msqid, int cmd, struct msqid_ds *buf);
```

flag 用于控制当前消息队列满或队列消息到达系统范围的限制时将要发生的事情。

0：msgrcv() 调用阻塞直到接收消息成功为止

IPC_NOWAIT: 调用进程会立即返回。若没有收到消息则立即返回 -1。

##### 信号量：

信号量（semaphore）与已经介绍过的 IPC 结构不同，它**是一个计数器。信号量用于实现进程间的互斥与同步，而不是用于存储进程间通信数据**

1. 信号量用于进程间同步，**若要在进程间传递数据需要结合共享内存**。
2. 信号量基于操作系统的 PV 操作，程序对信号量的操作都是**原子操作**。
3. 每次对信号量的 PV 操作不仅限于对信号量值加 1 或减 1，而且可以**加减任意正整数**。
4. 支持**信号量组**。

p操作：信号量为0挂起等待，否则信号量-1并获取资源

v操作：如果有进程正在挂起等待，则唤醒它们，否则释放资源并将信号量值+1

##### 共享内存：

指两个或多个进程共享一个给定的存储区

1. 共享内存是最快的一种 IPC，因为进程是直接对内存进行存取。
2. 因为多个进程可以同时操作，所以需要进行同步。
3. 信号量+共享内存通常结合在一起使用，信号量用来同步对共享内存的访问。



1.管道：速度慢，容量有限，只有父子进程能通讯   

2.FIFO：任何进程间都能通讯，但速度慢   

3.消息队列：容量受到系统限制，且要注意第一次读的时候，要考虑上一次没有读完数据的问题   

4.信号量：不能传递复杂消息，只能用来同步   

5.共享内存区：能够很容易控制容量，速度快，但要保持同步，比如一个进程在写的时候，另一个进程要注意读写的问题，相当于线程中的线程安全，当然，共享内存区同样可以用作线程间通讯，不过没这个必要，线程间本来就已经共享了同一进程内的一块内存

#### tty

软件仿真终端，它们是运行在内核态的

```bash
$ tty
/dev/ttys000
```

<img src="https://img2018.cnblogs.com/blog/952033/201909/952033-20190904181756676-1523246503.png" alt="img"  />

ttys000是**仿真终端在文件系统中的表示**，程序通过对这些文件的读写实现对仿真终端的读写

-  一些连接了键盘和显示器的系统中，我们可以接触到运行在内核态的软件仿真终端(tty1-tty6)

- 通过 SSH 等方式建立的连接中使用的都是伪终端（返回/dev/pts/1，1是例子）

- 伪终端是运行在用户态的软件仿真终端

伪终端在内核中分为两部分，分别是 master side 和 在 TTY 驱动中实现的 slave side。注意上图中的 xterm，这是一个运行在用户态的终端仿真程序

如果是通过 PuTTY 等终端仿真程序通过 SSH 的方式远程连接 Linux，那么终端仿真程序通过 SSH 与 PTY master side 交换数据

#### Open

https://blog.csdn.net/xqhrs232/article/details/52857709

主标志之间互斥，可用|连接副标志

主标志：只读，只写，读写

#### 常用命令

https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ps.html
