# 多线程与并发

    synchronized
    线程安全问题的主要诱因
    1.存在共享数据(也称临界资源)
    2.存在多条线程共同操作这些共享数据

# 解决问题的根本方法：

# 同一时刻有且只有一个线程在操作共享数据，其他线程必须等到该线程处理数据后再对共享数据进行操作

# 互斥锁的特性

    互斥性：即在同一时间只允许一个线程持有某个对象锁，通过这种特性来实现多线程的协调机制，这样在同一时间只有一个线程对需要同步的代码块(复合操作)进行访问。互斥性也称为操作的原子性。
    可见性：必须确保在锁被释放之前，对共享变量所做的修改，对于随后获得该锁的另一个线程是可见的(即在获得锁时应获得最新共享变量的值)，否则另一个线程可能是在本地缓存的某个副本上继续操作，从而引起不一致。
    synchronized锁的不是代码，锁的都是对象

# 跟进获取的锁的分类：获取对象锁和获取类锁

    获取对象锁的两种用法
    1.同步代码块(synchronized(this),synchronized(类实例对象))，锁是小括号()中的实例对象
    2.同步非静态方法(synchronized method)，锁是当前对象的实例对象
    获取类锁的两种用法
    1.同步代码块(synchronized(类.class))，锁是小括号()中的类对象(Class对象)
    2.同步静态方法(synchronized static method)，锁是当前对象的类对象(Class对象)


# 对象锁和类锁的总结

    1.有线程访问对象的同步代码块时，另外的线程可以访问该对象的非同步代码块
    2.若锁住的是同一对象，一个线程在访问对象的同步代码块时，另一个访问对象的同步代码块的线程会被阻塞
    3.若锁住的是同一个对象，一个线程在访问对象的同步方法时，另一个访问对象同步方法的线程会被阻塞；
    4.若锁住的是同一个对象，一个线程在访问对象的同步代码块时，另一个访问对象同步方法的线程会被阻塞，反之亦然；
    5.同一个类的不同对象的对象锁互不干扰；
    6.类锁由于也是一种特殊的对象锁，因此表现和上述1,2,3,4一致，而由于一个类只有一把对象锁，所以同一个类的不同对象使用类锁将会是同步的；
    7.类锁和对象锁互不干扰

# 实现synchronized的基础

    1.Java对象头
    2.Monitor

# 对象在内存中的布局

    1.对象头
    2.实例数据
    3.对其填充

# 对象头的结构：

    Mark Word 默认存储对象的hashCode，分代年龄，锁类型，锁标志位等信息
    Class Metadata Address
    类型指针指向对象的类元数据，JVM通过这个指针确定该对象是哪个类的数据

# Monitor：每个Java对象天生自带了一把看不见的锁

# 什么是重入

    从互斥锁的设计上来说，当一个线程试图操作一个由其他线程持有的对象锁的临界资源时，将会处于阻塞状态，当当一个线程再次请求自己持有对象锁的临界资源时，这种情况属于重入

# 早期sychronized开销大

    1.早期版本中，synchronized属于重量级锁，依赖于Mutex Lock实现
    2.线程之间的切换需要从用户态转换到核心态，开销较大
    
    Java6以后，synchronized性能得到了很大的提升
    1.Adaptive Spinning
    2.Lock Eliminate
    3.Lock Coarsening
    4.Lightweight Locking
    5.Biased Locking

## 自旋锁与自适应自旋锁

# 自旋锁

    1.许多情况下，共享数据的锁定状态持续时间较短，切换线程不值得
    2.通过让线程执行忙循环等待锁的释放，不让出CPU
    3.缺点：若锁被其他线程长时间占用，会带来许多性能上的开销
    PreBlockSpin

# 自适应自旋锁

    1.自旋的次数不再固定
    2.由前一次在同一锁上的自旋时间及锁的拥有者的状态来决定

# 锁消除

    更彻底的优化
    JIT编译时，对运行上下文进行扫描，去除不可能存在竞争的锁


# 锁粗化

    另一种极端
    通过扩大加锁的范围，避免反复加锁和解锁

# synchronized的四种状态

    无锁、偏向锁、轻量级锁、重量级锁
    锁膨胀方向：无锁->偏向锁->轻量级锁->重量级锁

# 偏向锁：减少同一线程获取锁的代价

    大多数情况下，锁不存在多线程竞争，总是由同一线程多次获得
    核心思想：
    如果一个线程获得了锁，那么锁就进入偏向模式，此时Mark Word的结构也变为偏向锁结构，当改线程再次请求锁时，无需再做任何同步操作，即获取锁的过程只需要检查Mark Word的锁标记位为偏向锁以及当前线程Id等于Mark Wrod的ThreadID即可，这样就省去了大量有关锁申请的操作。
    不适用于锁竞争比较激烈的多线程场合

# CAS(Compare And Swap)

# 轻量级锁

    轻量级锁是由偏向锁升级来的，偏向锁运行在一个线程进入同步块的情况下，当第二个线程加入锁争用的时候，偏向锁就会升级为轻量级锁。
    适应的场景：线程交替执行同步块
    若存在同一时间访问同一锁的情况，就会导致轻量级锁膨胀为重量级锁

# 锁的内存语义

    当线程释放锁时，Java内存模型会把该线程对应的本地内存中的共享变量刷新到主内存中；
    而当线程获取锁时，Java内存模型会把该线程对应的本地内存置为无效，从而使得被监视器保护的临界区代码必须从主内存中读取共享变量。


# ReetrantLock（再入锁）

    1.位于java.util.concurrent.locks包
    2.和CountDownLatch、FutureTask、Semaphore一样基于AQS实现
    3.能够实现比synchronized更细粒度的控制，如控制fairness
    4.调用lock()之后，必须调用unlock()释放锁
    5.性能未必比synchronized高，并且也是可重入的

# ReentrantLock公平性的设置

    1.ReetrantLock fairLock = new ReentrantLock(true);
    2.参数为true时，倾向于将锁赋予等待时间最久的线程
    3.公平锁：获取锁的顺序按先后调用lock方法的顺序(慎用)
    4.非公平锁：抢占的顺序不一定，看运气
    5.synchronized是非公平锁

# ReentrantLock将锁对象化

    1.判断是否有线程，或者某个特定线程，在排队等待获取锁
    2.带超时的获取锁的尝试
    3.感知有没有成功获取锁

# synchronized和ReentrantLock的区别

    总结：
    1.synchronized是关键字，ReentrantLock是类
    2.ReentrantLock可以对获取锁的等待时间进行设置，避免死锁
    3.ReentrantLock可以获取各种锁的信息
    4.ReentrantLock可以灵活地实现多路通知
    机制：sync操作Mark Word,lock调用Unsafe类的park()方法

# 什么是Java内存模型中的happens-before

    Java内存模型JMM
    Java内存模型(即Java Memory Model，简称JMM)本身是一种抽象的概念，并不真实存在，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量(包括实例字段，静态字段和构成数据对象的元素)的访问方式。

# JMM中的主内存

    1.存储Java实例对象
    2.包括成员变量、类信息、常量、静态变量等
    3.属于数据共享的区域，多线程并发操作时会引发线程安全问题

# JMM中的工作内存

    1.存储当前方法的所有本地变量信息，本地变量对其他线程不可见
    2.字节码行号指示器、Native方法信息
    3.属于线程私有数据区域，不存在线程安全问题

# JMM与Java内存区域划分是不同的概念层次

    1.JMM描述的是一组规则，围绕原子性，有序性，可见性展开
    2.相似点：存在共享区域和私有区域

# 主内存与工作内存的数据存储类型以及操作方式归纳

    1.方法里的基本数据类型本地变量将直接存储在工作内存的栈帧结构中
    2.引用类型的本地变量：引用存储在工作内存中，实例存储在主内存中
    3.成员变量、static变量、类信息均会被存储在主内存中
    4.主内存共享的方式是线程各拷贝一份数据到工作内存，操作完成后刷新回主内存

# 指令重排序需要满足的条件

    1.在单线程环境下不能改变程序运行的结果
    2.存在数据依赖关系的不允许重排序
    无法通过happens-before原则推导出来的，才能进行指令的重排序

# happens-before 的八大原则

    1.程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作
    2.锁定规则：一个unLock操作先行发生于后面对同一个锁的lock操作
    3.volatile变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作
    4.传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出A先行发生于操作C
    5.线程启动规则：Thread对象的start()方法先行发生于此线程的每一个动作
    6.线程中断规则：对线程interrupt()方法的调用先行发生于被中断线程的代码检查到中断事件的发生
    7.线程终结规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Tread.jion()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止
    8.对象终结规则：一个对象的初始化完成先行发生于他的finalize()方法的开始

# happens-before的概念

    如果两个操作不满足上述任意一个happens-before规则，那么这两个操作就没有顺序的保障，JVM可以对着两个操作进行重排序；
    如果操作A happens-before 操作B，那么操作A在内存上所做的操作对操作B都是可见的


# volatile :JVM提供的轻量级同步机制

    1.保障被volatile修饰的共享变量对所有线程总是可见的
    2.禁止指令重排序优化

# volatile变量为何立即可见

    1.当写一个volatile变量是，JMM会把该线程对应的工作内存中的共享变量值刷新到主内存中
    2.当读取一个volatile变量时，JMM会把该线程对应的工作内存置为无效

# volatile如何禁止重排优化

    内存屏障(Memory Barrier)
    1.保证特定操作的执行顺序
    2.保证某些变量的内存可见性

    通过插入内存屏障指令禁止在内存屏障前后的指令执行重排序优化
    强制刷出各种CPU的缓存数据，因此任何CPU上的线程都能读取到这些数据的最新版本


# volatile和synchronized的区别

    1.volatile本质是在告诉JVM当前变量在寄存器(工作内存)中的值是不确定的，需要从主存中读取；synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住知道该线程完成变量操作为止
    2.volatile仅能使用在变量级别；synchronized则可以使用在变量、方法和类级别
    3.volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized则可以保证变量修改的可见性和原子性
    4.volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞
    5.volatile标记的变量不会被编译器优化；synchronized标记的变量可以被编译器优化

# CAS(Compare and Swap)

    一种高效实现线程安全性的方法
    1.支持原子更新操作，适用于计数器，序列发生器等场景
    2.属于乐观锁机制，号称lock-free
    3.CAS操作失败时由开发者决定是继续尝试，还是执行别的操作

# CAS多数情况下对开发者来说是透明的

    1. J.U.C的atomic包提供了常用的原子性数据类型以及引用、数组等相关原子类型和更新操作工具，是很多线程安全程序的首选
    2.Unsafe类虽提供CAS服务，但因能够操作任意内存地址读写而有隐患
    3.Java9以后，可以使用Variable Handle API来替代Unsafe

    缺点：
    1.若循环时间长，则开销很大
    2.只能保证一个共享变量的原子操作
    3.ABA问题 解决：AtomicStampedReference

# Java线程池

    利用Executors创建不同线程池满足不同场景的 需求
    1. newFixedThreadPool(int nThreads)
    指定工作线程数量的线程池
    2.newCachedThreadPool()
    处理大量短时间工作任务的线程池
    2.1试图缓存线程并重用，当无缓存线程可用时，就会创建新的工作线程；
    2.2如果线程闲置的时间超过阈值，则会被终止并移除缓存
    2.3系统长时间闲置的时候，不会消耗什么资源
    3.newSingleThreadExecutor()
    创建唯一的工作者线程来执行任务，如果线程异常结束，会有另一个线程取代它
    4.newSingleThreadExecutor()与newScheduledThreadPool(int corePoolSize)定时或者周期性的工作调度，两者的区别在于单一工作线程还是多个线程
    5.newWorkStealingPool()
    内部会构建ForkJoinPool，利用working-stealing算法，并行地处理任务，不保证处理顺序

# Fork/Join框架

    把大任务分割成若干个小任务并行执行，最终汇总每个小任务结果后得到大任务结果的框架
    Work-Stealing算法：某个线程从其他队列里窃取任务来执行

# 为什么要使用线程池

    1.降低资源消耗
    2.提高线程的可管理性

# J.U.C的三个Executor接口

    1.Executor：运行新任务的简单接口，将任务提交和任务执行细节解耦
    2.ExecutorService：具备管理执行器和任务生命周期的方法，提交任务机制更完善
    3.ScheduledExecutorService：支持Future和定期执行任务

# ThreadPoolExecutor 的构造函数

    1.corePoolSize：核心线程数量
    2.maximumPoolSize：线程不够用时能够创建的最大线程数
    3.workQueue：任务等待队列
    4.keepAliveTime：抢占的顺序不一定，看运气
    5.threadFactory：创建新线程，Executor.defaultThreadFactory()

# handler：线程池的饱和策略

    1.AbortPolicy：直接抛出异常，这是默认策略
    2.CallerRunsPolicy：用调用者所在的线程来执行任务
    3.DiscardOldestPolicy：丢弃队列中靠最前的任务，并执行当前任务
    4.DiscardPolicy：直接丢弃任务
    5.实现RejectedExecutionHandler接口的自定义handler

# 新任务提交execute执行后的判断

    1.如果运行的线程少于corePoolSize，则创建新线程来处理任务，即使线程池中的其他线程是空闲的
    2.如果线程池中的线程数量大于等于corePoolSize且小于maximumPoolSize，则只有当workQueue满时才创建新的线程去处理任务
    3.如果设置的corePoolSize和maximumPoolSize相同，则创建的线程池的大小是固定的，这时如果有新任务提交，若workQueue未满，则将请求放入workQueue中，等待有空闲的线程去从workQueue中取任务并处理
    4.如果运行的线程数量大于等于maximumPoolSize，这时如果workQueue已经满了，则通过handler所指定的策略来处理任务

# 线程池的状态

    1.RUNNING：能接受新提交的任务，并且也能处理阻塞队列中的任务
    2.SHUTDOWN：不再接受新提交的任务，但可以处理存量任务
    3.STOP：不再接受新提交的任务，也不处理存量任务
    4.TIDYING：所有的任务都已终止
    5.TERMINATED：terminated()方法执行完后进入该状态

# 线程池的大小如何选定

    1.CPU密集型：线程数=按照核数或者核数+1设定
    2. I/O密集型：线程数=CPU核数*(1+平均等待时间/平均工作时间)


