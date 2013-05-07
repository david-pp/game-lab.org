---
layout: post
title: POSIX多线程程序设计
category : Linux开发
tags : [多线程, pthread]
date: 2009-02-05 21:27:00 +0800
---

### 目录 

1. [摘要](#abstract)  
2. [译者序](#david)
3. [Pthreads 概述](#pthread-intro)  
	3.1 [什么是线程?](#3.1)  
	3.2 [什么是Pthreads?](#3.2)  
	3.3 [为什么使用Pthreads?](#3.3)   
	3.4 [使用线程设计程序](#3.4)   
4. [Pthreads API编译多线程程序](#4)  
5. [线程管理](#5)  
	5.1 [创建和终止线程](#5.1)  
	5.2 [向线程传递参数](#5.2)  
	5.3 [连接（Joining）和分离（Detaching）线程](#5.3)  
	5.4 [栈管理](#5.4)   
	5.5 [其它函数](#5.5)   
6. [互斥量（Mutex Variables）](#6)   
	6.1 [互斥量概述](#6.1)   
	6.2 [创建和销毁互斥量](#6.2)  
	6.3 [锁定（Locking）和解锁（Unlocking）互斥量](#6.3)  
7. [条件变量（Condition Variable）](#7)  
	7.1 [条件变量概述](#7.1)  
	7.2 [创建和销毁条件变量](#7.2)  
	7.3 [等待（Waiting）和发送信号（Signaling）](#7.3)  
8. [没有覆盖的主题](#8)
9. [Pthread 库API参考](#9)  
10. [参考资料](#10)

---------------------------------------------------

### <a id="abstract"></a> 1. 摘要

在多处理器共享内存的架构中（如：对称多处理系统SMP），线程可以用于实现程序的并行性。历史上硬件销售商实现了各种私有版本的多线程库，使得软件开发者不得不关心它的移植性。对于UNIX系统，IEEE POSIX 1003.1标准定义了一个C语言多线程编程接口。依附于该标准的实现被称为POSIX theads 或 Pthreads。 

该教程介绍了Pthreads的概念、动机和设计思想。内容包含了Pthreads API主要的三大类函数：线程管理（Thread Managment）、互斥量（Mutex Variables）和条件变量（Condition Variables）。向刚开始学习Pthreads的程序员提供了演示例程。 

适于：刚开始学习使用线程实现并行程序设计；对于C并行程序设计有基本了解。不熟悉并行程序设计的可以参考EC3500: Introduction To Parallel Computing。

---------------------------------------------------

### <a id="david"></a> 2. 译者序

三天时间，终于在工作期间，抽空把上一篇POSIX threads programing翻译完了。由于水平有限，翻译质量差强人意，若有不合理或错误之处，请您之处，在此深表感谢！有疑问点此查看原文。在参考部分提及的几本关于Pthreads库的大作及该文章原文和译文可在下面的连接下载：

* 本篇及其英文原文:  <http://download.csdn.net/source/992256>
* 多线程编程指南:  <http://download.csdn.net/source/992248>
* Programing with POSIX thread(强烈推荐): <http://download.csdn.net/source/992239>
* Pthread Primer(强烈推荐): <http://download.csdn.net/source/992213>

---------------------------------------------------

### <a id="pthread-intro"></a> 3. Pthreads概述 

#### <a id="3.1"></a> 3.1 什么是线程? 
 
技术上，线程可以定义为：可以被操作系统调度的独立的指令流。但是这是什么意思呢？ 

对于软件开发者，在主程序中运行的“函数过程”可以很好的描述线程的概念。 

进一步，想象下主程序（a.out）包含了许多函数，操作系统可以调度这些函数，使之同时或者（和）独立的执行。这就描述了“多线程”程序。 
怎样完成的呢？ 
 
在理解线程之前，应先对UNIX进程（process）有所了解。进程被操作系统创建，需要相当多的“额外开销”。进程包含了程序的资源和执行状态信息。如下： 

* 进程ID，进程group ID，用户ID和group ID 
* 环境 
* 工作目录  
* 程序指令 
* 寄存器 
* 栈 
* 堆 
* 文件描述符 
* 信号操作（Signal actions） 
* 共享库 
* 进程间通信工具（如：消息队列，管道，信号量或共享内存） 

![进程](/assets/img/2009-02-05-process.gif)

线程使用并存在于进程资源中，还可以被操作系统调用并独立地运行，这主要是因为线程仅仅复制必要的资源以使自己得以存在并执行。 

独立的控制流得以实现是因为线程维持着自己的： 

* 堆栈指针 
* 寄存器 
* 调度属性（如：策略或优先级） 
* 待定的和阻塞的信号集合（Set of pending and blocked signals） 
* 线程专用数据（TSD：Thread Specific Data.） 

因此，在UNIX环境下线程： 

* 存在于进程，使用进程资源 
* 拥有自己独立的控制流，只要父进程存在并且操作系统支持 
* 只复制必可以使得独立调度的必要资源 
* 可以和其他线程独立（或非独立的）地共享进程资源 
* 当父进程结束时结束，或者相关类似的 
* 是“轻型的”，因为大部分额外开销已经在进程创建时完成了 

因为在同一个进程中的线程共享资源： 

* 一个线程对系统资源（如关闭一个文件）的改变对所有其它线程是可以见的 
* 两个同样值的指针指向相同的数据 
* 读写同一个内存位置是可能的，因此需要成员显式地使用同步 

---------------------------------------------------

#### <a id="3.2"></a> 3.2 什么是 Pthreads? 

历史上，硬件销售商实现了私有版本的多线程库。这些实现在本质上各自不同，使得程序员难于开发可移植的应用程序。 

为了使用线程所提供的强大优点，需要一个标准的程序接口。对于UNIX系统，IEEE POSIX 1003.1c（1995）标准制订了这一标准接口。依赖于该标准的实现就称为POSIX threads 或者Pthreads。现在多数硬件销售商也提供Pthreads，附加于私有的API。 

Pthreads 被定义为一些C语言类型和函数调用，用pthread.h头（包含）文件和线程库实现。这个库可以是其它库的一部分，如libc。 


---------------------------------------------------

#### <a id="3.3"></a> 3.3 为什么使用 Pthreads? 

使用Pthreads的主要动机是提高潜在程序的性能。 

当与创建和管理进程的花费相比，线程可以使用操作系统较少的开销，管理线程需要较少的系统资源。 

例如，下表比较了fork()函数和pthread_create()函数所用的时间。计时反应了50,000个进程/线程的创建，使用时间工具实现，单位是秒，没有优化标志。 

备注：不要期待系统和用户时间加起来就是真实时间，因为这些SMP系统有多个CPU同时工作。这些都是近似值。 

<table class="table table-striped">
	<thead>
		<tr>
			<th rowspan="2">平台</th>
			<th colspan="3">fork()</th>
			<th colspan="3">pthread_create()</th>
		</tr>
		<tr>
			<th>real</th>
			<th>user</th>
			<th>sys</th>
			<th>real</th>
			<th>user</th>
			<th>sys</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>AMD 2.4 GHz Opteron (8cpus/node) </td>
			<td>41.07</td> 
			<td>60.08</td> 
			<td>9.01</td>
			<td>0.66</td>
			<td>0.19</td>
			<td>0.43</td>
		</tr>
		<tr>
			<td>IBM 1.9 GHz POWER5 p5-575 (8cpus/node) </td>
			<td>64.24</td> 
			<td>30.78</td> 
			<td>27.68</td>
			<td>1.75</td>
			<td>0.69</td>
			<td>1.10</td>
		</tr>
		<tr>
			<td>IBM 1.5 GHz POWER4 (8cpus/node) </td>
			<td>104.05</td> 
			<td>48.64</td> 
			<td>47.21</td>
			<td>2.01</td>
			<td>1.00</td>
			<td>1.52</td>
		</tr>
		<tr>
			<td>INTEL 2.4 GHz Xeon (2 cpus/node) </td>
			<td>54.95</td> 
			<td>1.54</td> 
			<td>20.78</td>
			<td>1.64</td>
			<td>0.67</td>
			<td>0.90</td>
		</tr>
		<tr>
			<td>INTEL 1.4 GHz Itanium2 (4 cpus/node)  </td>
			<td>54.54</td> 
			<td>1.07</td> 
			<td>22.22</td>
			<td>2.03</td>
			<td>1.26</td>
			<td>0.67</td>
		</tr>
	</tbody>
</table>

在同一个进程中的所有线程共享同样的地址空间。较于进程间的通信，在许多情况下线程间的通信效率比较高，且易于使用。 

较于没有使用线程的程序，使用线程的应用程序有潜在的性能增益和实际的优点： 

* CPU使用I/O交叠工作：例如，一个程序可能有一个需要较长时间的I/O操作，当一个线程等待I/O系统调用完成时，CPU可以被其它线程使用。 
* 优先/实时调度：比较重要的任务可以被调度，替换或者中断较低优先级的任务。 
* 异步事件处理：频率和持续时间不确定的任务可以交错。例如，web服务器可以同时为前一个请求传输数据和管理新请求。 

考虑在SMP架构上使用Pthreads的主要动机是获的最优的性能。特别的，如果一个程序使用MPI在节点通信，使用Pthreads可以使得节点数据传输得到显著提高。 

例如： 

* MPI库经常用共享内存实现节点任务通信，这至少需要一次内存复制操作（进程到进程）。 
* Pthreads没有中间的内存复制，因为线程和一个进程共享同样的地址空间。没有数据传输。变成cache-to-CPU或memory-to-CPU的带宽（最坏情况），速度是相当的快。 
* 比较如下： 

<table class="table table-striped">
	<thead>
		<tr>
			<th>Platform</th>
			<th>MPI Shared Memory Bandwidth(GB/sec) </th>
			<th>Pthreads Worst Case Memory-to-CPU Bandwidth (GB/sec) </th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>AMD 2.4 GHz Opteron </td>
			<td>1.2</td>
			<td>5.3</td>
		</tr>
		<tr>
			<td>IBM 1.9 GHz POWER5 p5-575</td>
			<td>4.1</td>
			<td>16</td>
		</tr>
		<tr>
			<td>IBM 1.5 GHz POWER4 </td>
			<td>2.1</td>
			<td>4</td>
		</tr>
		<tr>
			<td>Intel 1.4 GHz Xeon </td>
			<td>0.3</td>
			<td>4.3</td>
		</tr>
		<tr>
			<td>Intel 1.4 GHz Itanium 2</td>
			<td>1.8</td>
			<td>6.4</td>
		</tr>
	</tbody>
</table>

---------------------------------------------------

#### <a id="3.4"></a> 3.4 使用线程设计程序 

##### 并行编程:  

在现代多CPU机器上，pthread非常适于并行编程。可以用于并行程序设计的，也可以用于pthread程序设计。 

并行程序要考虑许多，如下： 

* 用什么并行程序设计模型？ 
* 问题划分 
* 加载平衡（Load balancing） 
* 通信 
* 数据依赖 
* 同步和竞争条件 
* 内存问题 
* I/O问题 
* 程序复杂度 
* 程序员的努力/花费/时间 
* ...  

包含这些主题超出本教程的范围，有兴趣的读者可以快速浏览下“Introduction to Parallel Computing”教程。 

大体上，为了使用Pthreads的优点，必须将任务组织程离散的，独立的，可以并发执行的。例如，如果routine1和routine2可以互换，相互交叉和（或者）重叠，他们就可以线程化。 
 
拥有下述特性的程序可以使用pthreads： 

* 工作可以被多个任务同时执行，或者数据可以同时被多个任务操作。 
* 阻塞与潜在的长时间I/O等待。 
* 在某些地方使用很多CPU循环而其他地方没有。 
* 对异步事件必须响应。 
* 一些工作比其他的重要（优先级中断）。 

Pthreads 也可以用于串行程序，模拟并行执行。很好例子就是经典的web浏览器，对于多数人，运行于单CPU的桌面/膝上机器，许多东西可以同时“显示”出来。 

使用线程编程的几种常见模型： 

* **管理者/工作者（Manager/worker）**：一个单线程，作为管理器将工作分配给其它线程（工作者），典型的，管理器处理所有输入和分配工作给其它任务。至少两种形式的manager/worker模型比较常用：静态worker池和动态worker池。 

* **管道（Pipeline）**：任务可以被划分为一系列子操作，每一个被串行处理，但是不同的线程并发处理。汽车装配线可以很好的描述这个模型。 

* **Peer**: 和manager/worker模型相似，但是主线程在创建了其它线程后，自己也参与工作。 


##### 共享内存模型（Shared Memory Model）:  

所有线程可以访问全局，共享内存 

线程也有自己私有的数据 

程序员负责对全局共享数据的同步存取（保护） 
 
##### 线程安全（Thread-safeness）:  

线程安全：简短的说，指程序可以同时执行多个线程却不会“破坏“共享数据或者产生“竞争”条件的能力。 

例如：假设你的程序创建了几个线程，每一个调用相同的库函数： 

* 这个库函数存取/修改了一个全局结构或内存中的位置。 
* 当每个线程调用这个函数时，可能同时去修改这个全局结构活内存位置。 
* 如果函数没有使用同步机制去阻止数据破坏，这时，就不是线程安全的了。 
 
如果你不是100%确定外部库函数是线程安全的，自己负责所可能引发的问题。 

建议：小心使用库或者对象，当不能明确确定是否是线程安全的。若有疑虑，假设其不是线程安全的直到得以证明。可以通过不断地使用不确定的函数找出问题所在。 


---------------------------------------------------

### <a id="4"></a> 4. 编译多线程程序 

下表列出了一些编译使用了pthreads库程序的命令： 

<table class="table table-striped">
	<thead>
		<tr>
			<th>Compiler/Platform</th>
			<th>Compiler Command</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td rowspan="3">IBM AIX</td>
			<td>xlc_r / cc_r </td>
			<td>C (ANSI  /  non-ANSI) </td>
		</tr>
		<tr>
			<td>xlC_r </td>
			<td>C++</td>
		</tr>
		<tr>
			<td>xlf_r -qnosave, xlf90_r -qnosave </td>
			<td>Fortran - using IBM's Pthreads API (non-portable) </td>
		</tr>
		<tr>
			<td rowspan="2">INTEL Linux </td>
			<td>icc -pthread</td>
			<td>C</td>
		</tr>
		<tr>
			<td>icpc -pthread </td>
			<td>C++</td>
		</tr>
		<tr>
			<td rowspan="2">PathScale Linux  </td>
			<td>pathcc -pthread</td>
			<td>C</td>
		</tr>
		<tr>
			<td>pathCC -pthread </td>
			<td>C++</td>
		</tr>
		<tr>
			<td rowspan="2">PGI Linux </td>
			<td>pgcc -lpthread</td>
			<td>C</td>
		</tr>
		<tr>
			<td>pgCC -lpthread </td>
			<td>C++</td>
		</tr>
		<tr>
			<td rowspan="2">GNU Linux, AIX </td>
			<td>gcc -pthread</td>
			<td>C</td>
		</tr>
		<tr>
			<td>g++ -pthread </td>
			<td>C++</td>
		</tr>
	</tbody>
</table>

---------------------------------------------------

#### <a id="3.1"></a> 3.1


