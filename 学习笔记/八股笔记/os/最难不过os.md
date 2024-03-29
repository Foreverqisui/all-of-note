[TOC]

# 最难不过os:heart_eyes_cat:

> 一篇关于os的笔记，分为三大篇，希望有所帮助
> 
> * 入门篇
> 
> * 进阶篇
> 
> * 不会篇

## 一、冯诺依曼模型

### 基本结构

* #### 内存：程序和数据的存储位置
  
  > 特点：线性的存储区域
  > 
  > 最小的存储单位：字节；内存地址从0开始编号，自增排列，保证内存**读写任何一个数据的速度都是相同的**

* #### 总线：cpu和内存及其他设备间的通信
  
  > 地址总线：用于指定cpu操作的内存地址
  > 
  > 数据总线：用于读写内存的数据
  > 
  > 控制总线：用于发送和接受信号

* #### 输入 输出设备
  
  > 键盘、鼠标等

* #### :moneybag:cpu：32位和64位，成为cpu的带宽
  
  > 32位cpu一次可以计算4字节
  > 
  > 64位cpu一次可以计算8字节
  
  * ##### 内部组件
    
    * ###### 寄存器：存储计算时的数据
      
      > 通用寄存器：用来存储运算的数据
      > 
      > 程序计数寄存器：用来存储下一条指令的**内存地址**
      > 
      > 指令寄存器：存放指令本身
    
    * 控制单元：负责控制cpu的工作
    
    * 逻辑运算单元：负责计算

热:man_technologist:爱:man_technologist:学:man_technologist:习:man_technologist:每:man_technologist:一:man_technologist:天:man_technologist:

## 二、存储器的层次结构:minidisc:

> 寄存器、CPU cache、内存、外存

### 1、寄存器

:money_mouth_face::dollar::dollar::dollar::dollar::dollar::dollar::dollar::dollar::dollar::dollar::dollar:

运行速度最快，价格昂贵

### 2、CPU cache

:dollar::dollar::dollar::dollar::dollar::dollar:

> 使用SRAM（静态随机存储器），有电就可以保存数据，断电则消失
> 
> L1和L2是每个CPU核心独有的，L3是多个CPU核心共享的

* #### L1 cache：分为指令缓存和数据缓存

* #### L2 cache：二级缓存

* #### L3 cache：三级缓存

### 3、内存

:dollar::dollar::dollar::dollar:

> 使用DRAM（动态随机存取存储器）
> 
> 通过电容存储数据，需要定时更新电容，才能保存数据。（有可能漏电）

### 4、外存

:dollar:

> SSD硬盘
> 
> HDD硬盘

### 5、对应速度转化关系

> L1 cache = 100*内存 = HHD*10000000
> 
> L1 cache =  150000 SSD
> 
> SSD = 70*HHD
> 
> 所以机械硬盘慢的要死，没钱也要肝SSD

认:man_technologist:真:man_technologist:学:man_technologist:习:man_technologist:每:man_technologist:一:man_technologist:天:man_technologist:

## 三、走进ICU:bomb:

> 咳咳，从此刻开始就要走进ICU了，哦不CPU，这是一个伤脑筋的位置
> 
> 主要说三部分：CPU执行和指令、缓存一致性、MESI协议和中断

### 1、走进ICU第一步--CPU是怎么执行的

> 主要是以下几个小步骤：
> 
> * CPU读取程序计数器 获得 **指令的内存地址**
> 
> * 控制单元操作地址总线 指定 **刚刚获得的地址**
> 
> * 内存准备数据
> 
> * 数据总线传输 **指令数据** 到CPU
> 
> * CPU将指令数据存入 **指令寄存器**
> 
> * CPU分析指令，确定指令类型和参数
> 
> * CPU执行完毕 程序计数器自增，指向下一条指令

好了，以上就是CPU的运行过程可以看出一个

#### **指令周期**

> **Fetch（取的指令）** **Deccode（指令译码）** **Store（数据回写）** **Execution（执行指令）**，这一个循环了

#### 指令的执行速度

> 时钟周期：每一次脉冲信号高低电平的转换 GHZ
> 
> CPI：每条指令的平均时钟周期数
> 
> 程序的CPU执行时间=指令数×PI×钟周期时间

想要程序跑的快，优化

> 指令数
> 
> CPI
> 
> 时钟周期时间

那么CPU是如何和内存建立映射的呢

#### 直接映射策略

> 由于CPU从内存中读取数据是一小块一小块的，在cache中叫做Cache Line，在内存中成为BLOCK（内存块）
> 
> 直接映射：就是通过**取模运算**把Block的地址始终映射到CPU Line（缓存块）的地址
> 
> 缺点：取模预算会产生多个内存对应同一个CPU Line的问题

于是会在CPU Line中存储三个变量

> * Tag（组标记）：用来记录CPU Line中存储数据对应的内存块，区分不同的内存块
>
> * Data（数据）：从内存加载过来的实际存放数据
>
> * Valid bit（有效位）：用来标记对应的CPU Line中的数据是否是有效位的，如果为0，则直接访问内存，重新加载数据
>

因此一个内存的访问地址包括：组标记、CPU Line索引、偏移量

CPU Cache里的数据结构：索引+有效位+组标记+数据块组成

#### 如何写出让CPU执行快的代码

#### 提高缓存命中率

> 顺序操作：有规律的条件分支
> 
> 多核cpu：线程绑定CPU的某一个核心

### 2、缓存一致性

#### Cache中的数据写回内存

##### 写直达

> 把数据同时写入内存和Cache中
> 
> 优点：操作简单
> 
> 缺点：写操作花费大量时间，影响性能

##### 写回

> 写操作时，新数据仅写入Cache Block中，只有修改过的Cache Block**被替换**时，才会把数据写入内存。并将其他CPU缓存置为invalidated
> 
> 优点：减少写回频率，提高性能
> 
> 缺点：多核操作获取数据时会产生不一致

#### 解决缓存不一致的问题

##### 写传播

> 某个CPU Cache更新数据时，必须传播到其它核心

##### 事务的串行化

> 当某个CPU在更新数据时，就需要同步到其它核心
> 
> 实现：1. CPU核心对于Cache中数据的操作，需要同步给其它CPU核心
> 
>             2. 引入锁，只有拿到锁才会进行对应的数据更新

#### 总线嗅探

> 用来实现写传播
> 
> 作用：CPU需要每时每刻监听总线上的一切活动
> 
> 缺点：不管其它核心Cache是否缓存相同的数据，都需要发出广播事件，**加重总线负载**，不能保证事务串行化

#### MESI协议

> 作用：实现事务串行化，降低总线带宽，弥补总线嗅探的不足
> 
> modified（已修改）：状态表明是脏标记，表明Cache Block已更新，但未写入内存
> 
> invalidated（已失效）：表明Cache Block已经失效，不可读取该状态的数据
> 
> exclusive（独占）：表明是干净状态，没有其它核心有该数据
> 
> shared（共享）：表示相同数据存在多个核心里，不能直接修改

#### 伪共享问题

> 多个线程同时读写同一个Cache Line的不同变量时，而导致CPU Cache失效的现象
> 
> 避免方法：Cache Line大小字节对齐 字节填充等方式

### 3、中断

> 为了解决中断处理程序执行过长和中断丢失问题，将中断过程分为两个阶段，分为上半部和下半部分

#### 软中断

> 也就是下半部分
> 
> 由内核触发，主要是负责上半部未完成的工作
> 
> 特点：延迟执行，通常都是耗时比较长的事情

#### 硬中断

> 主要是负责耗时短的工作
> 
> 特点：快速执行

:older_man:已经秃头了，然而呢，:older_man:大概呢，:older_man:仅仅是一个前序，:older_man:还没正式开始，而且还省略了好多东西:older_man:

## 四、进程管理子系统:mortar_board:

> 这个地方的重要性，不言而喻，嗯对.....
> 
> 对执行程序进行生命周期和资源管理

### 进程:space_invader:

> 运行中的程序

#### 1、进程的状态

> 创建态、就绪态、运行态、阻塞态、结束态

#### 2、挂起态

> 描述进程没有占用实际物理内存空间的情况
> 
> 阻塞挂起态：进程在外存，等待某个事件出现
> 
> 就绪挂起态：进程在外存，只要进入内存就立刻执行

#### 3、进程控制块（PCB）

> 是进程的唯一标识

##### 进程描述信息

> 进程标识符：标识各个进程且唯一
> 
> 用户标识符：进程归属的用户，共享和保护

##### 进程控制和管理

> 进程当前状态：new running blocked
> 
> 进程优先级：进程抢占CPU时的优先级

##### 资源分配清单

> 内存地址和虚拟地址的空间信息
> 
> 打开文件列表和使用的I/O设备信息

##### CPU信息

> CPU中各个寄存器的值
> 
> 进程切换时CPU的状态信息

#### 4、进程的上下文切换

> 一个进程切换到另一个进程运行

##### CPU上下文:man_teacher:

> 独立的地址空间
> 
> 依赖程序计数器和CPU寄存器的环境
> 
> 包含虚拟内存、栈、全局变量等用户空间资源（线程没有）
> 
> 海报框内核堆栈、寄存器等内核空间资源

#### 5、进程间的通信:man_teacher:

##### 匿名管道

> 单向数据传输
> 
> **管道本质其实是内核中维护的一块内存缓冲区**
> 
> 优点：简单，容易得知管道里的数据已经被另一个读取
> 
> 缺点：通信效率低，不适合频繁的交换数据
> 
>             只能用于具有亲缘关系的进程间的通信

##### 命名管道（FIFO）

> 命名管道**提供了一个路径名与之关联**以FIFO 的文件形式存在于文件系统中,并且其打开方式与打开一个普通文件是一样的，这样即使与 FIFO 的创建进程不存在亲缘关系的进程,只要可以访问该路径,就能够彼此通过 FIFO 相互通信
> 
> 优点：能用于亲缘关系的进程间通信，可以与不相关的进程交换数据

##### 消息队列

> 保存在内核的消息链表
> 
> 优点：可以频繁的交换数据
> 
> 缺点
> 
> * 通信不及时
> 
> * 传输数据大小有限制（内核对每个消息体有最大长度限制）
> 
> * 用户态与内核态之间数据拷贝的开销

##### 共享内存

> 拿出一块虚拟内存地址空间，映射到物理内存中，无需内核介入
> 
> 优点：解决了用户态和内核态之间的消息拷贝的开销
> 
> 缺点：多个进程同时修改一个共享内存，可能会发生冲突

##### 信号量

> 实现进程间的互斥和同步
> 
> 两种原子操作
> 
> * P操作：把信号量减1，如果小于0，**表明资源已经被占用需要等待**
> 
> * V操作：把信号量加1，如果小于等于0，**表明有阻塞进程**，将其唤醒
> 
> 两种初始状态
> 
> * 同步信号量：信号量初始为0，保证A先于B执行（生产者A 消费者B）
> 
> * 互斥信号量：信号量初始为1，保证共享内存任何时刻只有一个进程访问

##### 内存映射（Memory-mapped I/O）

> 是将磁盘文件的数据映射到内存,用户通过修改内存就能修改磁盘文件

##### 信号

> 对于异常情况的工作模式，需要信号通知进程
> 
> 是事件发生时对进程的通知机制,有时也称之为软件中断,它是在软件层次上对中断机制的一种模拟,是一种异步通信的方式。信号可以导致一个正在运行的进程被另一个正在运行的异步进程中断,转而处理某一个突发事件

##### Socket

> 跨网络与不同主机上的进程之间通信

### 线程:face_with_thermometer:

> 进程中的一条执行流程
> 
> 优点
> 
> * 创建时间快：不涉及资源管理而是共享
> 
> * 终止时间快：需要释放的资源很少
> 
> * 同进程内，线程切换快：线程具有相同的地址空间（虚拟内存共享），意味着拥有同一个页表，切换时不需要切换页表，减少开销
> 
> * 线程间共享内存和文件资源：交互效率高，数据传递不需要经过内核

#### 1、线程的上下文切换:man_teacher:

> 线程有自己的堆栈和局部变量,无独立的地址空间
> 
> 虚拟内存资源保持不变（内存共享），只切换线程私有数据、寄存器等不共享数据

#### 2、线程的实现

##### 用户线程

> 在用户空间实现的线程，不由**内核管理**的线程，由**用户态的线程库**来完成维护线程的管理
> 
> 优点：无需内核和用户态的切换，速度快
> 
> 缺点
> 
> * 一个线程阻塞，所有线程都不能执行
> 
> * 用户态线程没法中断当前运行的线程
> 
> * 每个线程得到时间片较少，执行较慢

##### 内核线程

> 由os管理，在**内核**中实现
> 
> 优点
> 
> * 一个线程阻塞不会影响其他线程
> 
> * 分配给线程的进程获得更多的CPU运行时间
> 
> 缺点
> 
> * 系统开销大
> 
> * 由内核维护进程和线程的上下文信息

##### 轻量级线程（LWP）

> 是内核支持的用户线程，一个进程可有一个或多个LWP，每个LWP和内核线程一对一映射

##### 多线程同步

###### 特点:man_teacher:

> * 线程是程序执行的最小单元，一个进程可以拥有多个线程
> 
> * 各个线程之间共享程序的内存空间（代码段、数据段和堆空间）和系统分配的资源（CPU，I/O，打开的文件），但是各个线程拥有自己的栈空间 
> 
> * 多线程优点：减少程序响应时间；提高CPU利用率；创建和切换开销小；数据共享效率高；简化程序结构

> 互斥：两个操作不能在同一时间执行
> 
> 同步：前面的不结束，后面的就不能开始

为了解决在临界区互斥问题，于是有了锁

#### 3、线程的状态

> NEW、RUNNABLE、BLOCKED、WTING、TIMED_WTING、TERMINATED
> 
> Java线程在运行的生命周期中,在任意给定的时刻,只能处于下列6种状态之一：
> 
>  NEW ：初始状态,线程被创建,但是还没有调用start方法。 
> RUNNABLE：可运行状态,线程正在JVM中执行,但是有可能在等待操作系统的调度。 BLOCKED ：阻塞状态,线程正在等待获取监视器锁。 
> WTING ：等待状态,线程正在等待其他线程的通知或中断。 
> TIMED_WTING：超时等待状态,在WTING的基础上增加了超时时间,即超出时间自动返回。 
> TERMINATED：终止状态,线程已经执行完毕。 
> 线程在创建之后默认为初始状态,在调用start方法之后进入可运行状态,可运行状态不代表线程正在运行,它有可能正在等待操作系统的调度。进入等待状态的线程需要其他线程的通知才能返回到可运行状态,而超时等待状态相当于在等待状态的基础上增加了超时限制,除了他线程的唤醒,在超时时间到达时也会返回运行状态。此外,线程在执行同步方法时,在没有获取到锁的情况下,会进入到阻塞状态。线程在执行完run方法之后,会进入到终止状态。
>  加分回答 
> Java将操作系统中的就绪和运行两个状态合并为可运行状态（RUNNABLE）。线程阻塞于synchronized的监视器锁时会进入阻塞状态,而线程阻塞于Lock锁时进入的却是等待状态,这是因为Lock接口实现类对于阻塞的实现均使用了LockSupport类中的相关方法。

#### 线程的通信方式

> 在Java中,常用的线程通信方式有两种,分别是利用Monitor实现线程通信、利用Condition实现线程通信。
> 
> 线程同步是线程通信的前提,所以究竟采用哪种方式实现通信,取决于线程同步的方式。
> 
> 如果是采用synchronized关键字进行同步,则需要依赖Monitor（同步监视器）实现线程通信,Monitor就是锁对象。在synchronized同步模式下,锁对象可以是任意的类型,所以通信方法自然就被定义在Object类中了,这些方法包括：wt()、notify()、notifyAll()。一个线程通过Monitor调用wt()时,它就会释放锁并在此等待。当其他线程通过Monitor调用notify()时,则会唤醒在此等待的一个线程。当其他线程通过Monitor调用notifyAll()时,则会唤醒在此等待的所有线程。
>  JDK 
> 1.5新增了Lock接口及其实现类,提供了更为灵活的同步方式。如果是采用Lock对象进行同步,则需要依赖Condition实现线程通信,Condition对象是由Lock对象创建出来的,它依赖于Lock对象。Condition对象中定义的通信方法,与Object类中的通信方法类似,它包括awt()、signal()、signalAll()。通过名字就能看出它们的含义了,当通过Condition调用awt()时当前线程释放锁并等待,当通过Condition调用signal()时唤醒一个等待的线程,当通过Condition调用signalAll()时则唤醒所有等待的线程。
>  加分回答 
> 线程同步是基于同步队列实现的,而线程通信是基于等待队列实现的。当调用等待方法时,即将当前线程加入等待队列。当调用通知方法时,即将等待队列中的一个或多个线程转移回同步队列。因为synchronized只有一个Monitor,所以它就只有一个等待队列。而Lock对象可以创建出多个Condition,所以它拥有多个等待队列。多个等待队列带来了极大的灵活性,所以基于Condition的通信方式更为推荐。
> 
> 比如,在实现生产消费模型时,生产者要通知消费者、消费者要通知生产者。相反,不应该出现生产者通知生产者、消费者通知消费者这样的情况。如果使用synchronized实现这个模型,由于它只有一个等待队列,所以只能把生产者和消费者加入同一个队列,这就会导致生产者通知生产者、消费者通知消费者的情况出现。采用Lock实现这个模型时,由于它有多个等待队列,可以有效地将这两个角色区分开,就能避免出现这样的问题。

#### 5、锁

###### 自旋锁（等待锁）

> 当获取不到锁的时候，线程会一直进行while循环，直到拿到锁

###### 互斥锁（无等待锁）

> 当没有获取到锁时，线程放入等待队列，让出CPU，等待唤醒

###### 读写锁

> 适用于明确区分读写的场景
> 
> 读多写少的时候，优势大

###### 悲观锁

> 上述锁都是悲观锁
> 
> 认为多线程同时修改共享资源的概率较高，很容易发生冲突，所以在访问之前，先上锁

###### 乐观锁（无锁编程）

> 先修改共享资源，再判断是否冲突，冲突就放弃操作

###### 死锁

> 两个进程都在等待对方释放锁，互相等待，于是就灭了
> 
> 产生的四个条件
> 
> * 互斥条件：多个线程不能同时使用一个资源
> 
> * 持有并等待条件
>   
>   * 我看着碗里的排骨（线程A有线程B要的资源）
>   
>   * 但是我不吃（因为等着线程C释放资源）
>   
>   * 因为我看着锅里的牛肉什么时候好（线程C 的资源）
>   
>   * 我不吃（线程A等待不释放资源）
>   
>   * 但是我家的狗在等待我碗里的骨头（线程B想要资源）
> 
> * 不可剥夺条件：在自己使用完之前不能被抢走
> 
> * 环路等待条件：两个线程获取资源的书序构成环形链

###### 避免死锁的方式

> 破坏上述四个条件之一
> 
> 常见方法
> 
> * 破坏环路等待：使用资源有序分配

### 进程和线程的区别:woman_teacher:

> 主要差别在于它们是不同的操作系统资源管理方式
> 
> 地址空间、开销、并发性、内存

##### 地址空间

> 进程：有独立的地址空间
> 
> 线程：无独立的地址空间，但有自己的堆栈和局部变量,

##### 开销

> 上下文切换时，进程耗费资源大，开销时间长，效率更差

##### 并发性

>  进程的并发性较低
> 
> 线程的并发性较高；

##### 内存

> 进程：系统在运行的时候会为每个进程分配不同的内存空间
> 
> 线程：除CPU外，线程的资源都来自所属进程的资源
> 
>             线程组之间只能共享资源

> 每个独立的进程有一个程序运行的入口、顺序执行序列和程序的出口
> 
> 线程不能够独立执行,必须依存在应用程序中,由应用程序提供多个线程执行控制；

#### 协程与线程的区别

> 机制、执行方式、定义

##### 机制

> 线程进程都是同步机制
> 
> 协程则是异步

##### 执行方式

> 线程是抢占式
> 
> 协程是非抢占式：需要用户自己释放使用权来切换到其他协程，因此同一时间其实只有一个协程拥有运行权

##### 定义

> 线程：是被分割的CPU资源，是协程的资源
> 
> 协程：抽象在线程之上，是组织好的代码流程, 协程需要线程来承载运行,

##### 其他

>  线程是操作系统的资源,线程的创建、切换、停止等都非常消耗资源,而创建协程不需要调用操作系统的功能,编程语言自身就能完成,所以协程也被称为用户态线程,协程比线程轻量很多；
> 
>  线程在多核环境下是能做到真正意义上的并行,而协程是为并发而产生的；
> 
> 一个具有多个线程的程序可以同时运行几个线程,而协同程序却需要彼此协作的运行；
> 
> 操作系统对于线程开辟数量限制在千的级别,而协程可以达到上万的级别。

## 五、内存管理子系统

> 对os的内存记性管理、分配、回收、隔离

### 虚拟内存

> os如何管理虚拟地址和物理地址的关系

#### 1、内存分段

> 不同的段有不同的属性（代码分段、栈段）

##### 如何建立映射

> 通过段表，每个段在**段表中会有一个项**
> 
> 在项内找到**段的基地址再加上偏移量**

##### 虚拟地址的组成

> **段选择子和段偏移量**
> 
> * 段选择子：在段寄存器内，里面有**段号**，是**段表的索引**
> 
> * 段表：保存的是段的**基地址、界限、特权等级**
> 
> * 段内偏移量：位于0--段界限之间，合法就将**段基地址+段偏移量**--->物理内存地址

##### 分段的问题

> 内存碎片
> 
> * 外部内存碎片：产生多个不连续的小物理内存
> 
> * 内部内存碎片：程序所有内存都加载到物理内存，但程序不常使用，导致内存浪费
> 
> 解决方法
> 
> * 外部内存碎片--内存交换，将内存数据写回硬盘后，紧挨着上面的内存后面放入
> 
> 缺点：如果交换大内存，硬盘访问速度慢，造成卡顿

#### 2、内存分页

> 把整个虚拟和物理内存空间切成一段段固定尺寸大小（linux每页为4kb），**连续且尺寸固定的内存空间**，叫做页
> 
> 页表：存储在内存里，用来将虚拟地址映射到物理地址上，MMU（内存管理单元）就做转换的工作

##### 1、缺页异常

> 访问的虚拟地址查不到，就会产生缺页异常
> 
> 解决：进入**内核空间**分配物理内存、更新**进程页表**，再返回**用户空间**，恢复进程运行

##### 2、内存碎片

> 采用分页，释放内存以页为单位释放，就不会产生无法给进程的小内存
> 
> 内存交换：一次性写入磁盘的只有少数的几个页，交换效率高

##### 3、虚拟地址

> 页号+页偏移量
> 
> 页号：页表的索引
> 
> 页表：包含物理页每页所在物理内存的基地址
> 
> 物理内存地址：基地址+页内偏移

### TODO

## 六、文件管理子系统:page_with_curl:

> 对文件进行管理

### 1、文件的两个数据结构

#### 索引节点（inode）

> 用来记录文件的元信息（如inode编号、文件大小、创建时间、数据在磁盘的位置等）
> 
> 是文件的唯一标识，索引节点占用磁盘空间

#### 目录项（dentry）

> 记录文件的名字、**索引节点指针**以及与其他目录项的层级关联关系
> 
> 是由**内核维护的**一个数据结构，缓存在内存

### 2、虚拟文件系统（VFS）

> 作用：对用户提供一个统一的接口
> 
> 定义：VFS定义了一组所有文件系统都支持的数据结构和标准化接口

### 3、文件的打开

> 文件系统的基本操作单位是数据块

### 4、文件的存储

#### 连续空间存放

> 文件存放在磁盘李旭的物理空间中
> 
> 优点：读写效率高，根据起始位置和长度**一次磁盘寻道**则可以
> 
> 缺点
> 
> * 外存空间碎片
> 
> * 文件长度不易扩展

#### 非连续空间存放:man_teacher:

> 链表方式
> 
> 索引方式

##### 链表方式

> 离散的，不连续的存储方式
> 
> 缺点：不适用于大磁盘

###### 隐式链表

> 结构：文件头包含**第一块和最后一块的位置**，且每个数据块留出一个指针空间，用来存放**下一个数据块的位置**
> 
> 优点
> 
> - 消除磁盘碎片
> 
> - 提高磁盘空间利用率
> 
> - 文件长度可动态扩展
> 
> 缺点
> 
> * 无法直接访问数据块
> 
> * 消耗一定的存储空间
> 
> * 稳定性差

###### 显式链表

> 结构：用于链接文件各数据块的指针，显式的存在**内存**中的一张链接表
> 
>             内存中村这一个表格**文件分配表（FAT）**，每个表项中存放着链接指针，指               向下一个数据块
> 
> 优点
> 
> * 查找在内存，提高检索速度
> 
> * 减少了对磁盘的访问

##### 索引方式

> 优点
> 
> - 文件的创建、增大、缩小很方便
> 
> - 不会有碎片化的问题
> 
> - 支持顺序读取和随机读取
> 
> 缺点
> 
> * 存储索引带来的开销

### 5、空闲空间管理

#### 空闲表法（连续分配）

> 为所有空闲空间创建一张表
> 
> 优点：少量空闲区时，适用于请求分配和回收磁盘空间
> 
> 缺点：空闲区碎片，空闲表变得很大，查询效率很低

#### 空闲链表法

> 每一个空闲块里有一个指针指向下一个空闲块
> 
> 缺点：不适用于大型文件系统

#### 位图法

> 利用二进制的一位表示磁盘中一个盘的使用情况
> 
> 当值为0时，表示对应的盘块空闲
> 
> 当值为1时，表示对应的盘块已分配

### 6、文件系统的结构

### 7、软连接和硬链接

#### 硬链接

> 多个目录项中的**索引节点**指向同一个inode
> 
> 特点：不可用于跨文件系统，只有删除文件的所有硬链接以及源文件时，系统才会彻底删除该文件

#### 软连接

> 文件有独立的inode，相当于重新创一个文件，但文件内容是**另一个文件的路径**
> 
> 特点：可以跨文件系统，甚至目标文件删除，链接文件还在

### 8、文件I/O

#### 缓冲与非缓冲I/O:monkey_face:

> 区分：是否利用标准库缓冲

##### 缓冲I/O

> 利用**标准库的缓存**实现文件的加速访问
> 
> **标准库**再通过系统调用访问文件

##### 非缓冲I/O

> 直接通过系统调用访问文件

#### 直接与非直接I/O:hear_no_evil:

> 是否利用操作系统缓存

##### 直接I/O

> 不会发生内核缓存和用户程序间数据复制
> 
> 而是直接经过文件系统访问磁盘

##### 非直接I/O

> 读操作时，数据从**内核缓存**中拷贝到用户程序
> 
> 写操作时，数据从**用户程序**拷贝到内核缓存
> 
> 由内核决定何时写入磁盘

##### 触发内核缓存写入磁盘的场景

> * 在调用write的最后，内核缓存数据过多
> 
> * 在用户主动地调用sync，内核缓存会刷到磁盘上
> 
> * 在内存十分紧张，无法再分配页面时
> 
> * 内核缓存数据的缓存时间超过了某个时间

#### 阻塞与非阻塞I/O:speak_no_evil:

##### 阻塞I/O

> 当用户程序执行read，线程阻塞，等**内核数据准备好**
> 
> 并把数据从**内核缓冲区**拷贝到**应用程序的缓冲区**中
> 
> 当拷贝完成，read返回

##### 非阻塞I/O

> 非阻塞的read请求在**数据为准备好**的情况下，**立即返回**
> 
> 可以继续执行，此时应用程序**不断轮询内核**
> 
> 直到数据准备好，**内核**将数据拷贝到**应用缓冲区**
> 
> read调用才可以获得结果

### TODO
