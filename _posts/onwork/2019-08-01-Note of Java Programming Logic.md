---
title: Java编程逻辑笔记
tags: [java]
categories: [java]
layout: post
---

* content
{:toc}

## 第1章 编程基础

数据基本类型有：

- 整数类型：byte/short/int/long，分别占1、2、4、8个字节
- 小数类型：float/double，分别占4、8字节
- 字符类型：char，占2个字节，使用常量字符串初始化时使用**单引号**括起来
- 布尔类型：boolean

基本数据类型都有对应的数组类型。

数组在存储的时候使用到了**两块空间**，一块存储内容本身，一块存储内容的位置，因此在赋值的时候只改变引用即可。

Java中的逻辑运算包括：

- 与（&）
- 或（|）
- 非（!）
- 异或（^）
- 短路与（&&）
- 短路或（||）

**短路**的意义在于，可以通过前一个表达式的值得到结果，就不会再去看后一个表达式的值。

Java中的函数有两类特殊类型的参数：数组和可变长度的参数。基本数据类型作为参数时，对其修改不会影响它本身的值，但是对数组的修改会影响原数组内容。可变长度参数`int max(int min, int ... a)`实际上会将这多个参数转化成数组来处理。**可变长度参数必须是参数列表中的最后一个**。

void函数也是可以使用return的，只不过不带值，表示提前结束函数执行：`return;`

## 第2章 数据背后的二进制

### 整数

整数使用补码进行表示，因此正整数的表示方式与原码相同，负整数表示方式为**绝对值按位取反加一**。

对于byte类型，整数最大表示是01111111，即127，负数最小表示为10000000，即-128，其他类型的整数也类似，**负数能多表示一个数**。

位运算包括：

- 左移（<<），右边的低位补0，高位舍弃。左移一位相当于乘以2
- 无符号右移（>>>），右边的舍弃，左边补0
- 有符号右移（>>），右边舍弃，左边补什么取决于最高位是什么，原来是1就补1，是0就补0.右移一位相当于除以2

逻辑运算包括：

- 按位与&
- 按位或|
- 按位取反~
- 按位异或^

### 小数

Java中的float类似是32位的，double类型是64位的。

在32位格式中，1位表示符号23位表示尾数，8位表示指数。

在64位格式中，1位表示符号，52位表示尾数，11位表示指数。

### 字符的编码

ASCII码中最高位设置为0，用剩下的7位表示字符，因此可以表示128个字符（0~127）。

GB2312用两个字节表示汉字。两个字节的最高为都是1（0表示这是一个ASCII字符）。

GBK兼容GB2312，但是**低位字节**最高位可能是0，因为这个编码规定汉字固定使用两个字节表示，因此都是两个两个处理，不考虑低位。

Unicode是统一编码方式，有多种方案，包括UTF-32，UTF-16和UTF-8：

- UTF-32：用4个字节表示一个字符，根据最高位是第一个字节还是最后一个字节可以分为大端字节序（UTF-32-BE）和小端字节序（UTF-32LE）
- UTF-16：用变长字节表示。常用字符用两个字节表示，增补字符用4个字节表示
- UTF-8：用变长字节表示，编号小的使用的字节就少，编号大的使用的字节就多，位1~4不等。

在Java中，可以使用`public byte[] getBytes(String charsetName)`来获取一个字符串的给定编码格式的二进制形式；使用`public String(byte bytes[], String charsetName)`可以按照指定编码格式解读一个二进制数组位字符串。

### char

char可以表示英文字符和中文字符，并且可以进行算术运算和比较，原因是char在Java中是用UTF-16BE表示的。

**char本质上是一个固定占用两个字节的无符号正整数，这个正整数对应于Unicode编号，用于表示那个Unicode编号对应的字符**

## 第3章 类的基础

每个类都要有一个构造方法，如果没有定义，Java会自动生成一个默认构造方法。一旦定义了，就不会生成默认构造方法。

Java编译和运行时，都需要指定一个classpath，即类路径。类路径可以有多个，对于直接的class文件，路径就是class文件的根目录；对于jar包，路径是jar包的完整名称（包括路径和jar包名）。在windows系统中，多个路径使用分号分隔，在其他系统中，以冒号分隔。

Java运行时，会根据类的**完全限定名**寻找并加载类，寻找的方式就是在类路径中寻找，如果是class文件的根目录，则直接查看是否有对应的子目录及文件，如果是jar文件，则现在内存中解压文件，然后再查看是否有对应的类。

## 第4章 类的继承

### 动态绑定

子类可以重写父类的非private方法，当调用的时候会动态绑定，执行子类的方法。

### 静态绑定

如果子类的实例变量、静态方法和静态变量与父类重名，

当通过b（静态类型Base）访问时，访问的是Base的变量和方法，当通过c（静态类型Child）访问时，访问的是Child的变量和方法，这称之为静态绑定，即访问绑定到变量的静态类型。静态绑定在程序编译阶段即可决定，而动态绑定则要等到程序运行时。实例变量、静态变量、静态方法、private方法，都是静态绑定的。

当有多个重名函数的时候，在决定要调用哪个函数的过程中，首先是按照参数类型进行匹配的，换句话说，寻找在所有重载版本中最匹配的，然后才看变量的动态类型，进行动态绑定。

一个父类的变量能不能转换为一个子类的变量，取决于这个父类变量的动态类型（即引用的对象类型）是不是这个子类或这个子类的子类。



## 第15章 并发基础知识

### 15.1 线程的基本概念

**线程表示一条单独的执行流，有自己的程序执行计数器，有自己的栈。**在Java中有两种方式创建线程：一种是继承Thread，另一种是实现Runnable接口。**不管哪种方式，最后都应该通过调用Thread的start方法来启动线程**（实现Runnable接口后，应该将此Runnable对象作为参数构造一个Thread对象）。直接调用run方法并不会启动一条单独的执行流，而是会将其作为一个普通方法在主线程中执行。

使用Thread的静态方法currentThread可以返回当前执行的线程对象。

线程有以下基本属性和方法：

- id和name：id是递增的整数，name可以通过setName设置；
- 优先级：优先级只是一种**建议**，而非强制；
- 状态：包含NEW, TERMINATED, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING；
- 是否为daemon线程：当程序中只剩下daemon线程时，程序会退出。垃圾回收线程也是daemon线程；
- sleep方法：睡眠期间，该线程会让出CPU。**睡眠期间可以被中断**，抛出InterruptedException；
- yield方法：静态方法，**建议**让其他线程运行，并非强制；
- join方法：如果在线程A中调用B.join()，那么线程A会等待线程B执行完再继续执行。

**竞态条件：**当多个线程访问和操作同一个对象时，最终执行结果与执行时序有关，可能正确也可能不正确。

**内存可见性问题：**因为数据会被存储到内存、CPU的寄存器以及各级缓存中，所以当一个线程访问一个变量的时候，访问到的不一定是最新的值（主要是**多处理器**会有多个缓存，他们一般会周期性同步）。

解决内存可见性的方法一般是：

- 使用volatile关键字
- 使用synchronized关键字或显式锁同步

### 15.2 理解synchronized

synchronized有三种使用方法：实例方法、静态方法和代码块。

**实例方法**

synchronized实例方法实际保护的是**同一个对象**的方法调用，因此多个线程可以执行同一个synchronized实例方法，只要他们属于不同对象。

同一个对象中的不同synchronized方法是不能同时执行的，但是非synchronized方法可以和synchronized方法同时执行，所以，**一般在保护变量时，需要在所有访问该变量的方法上加上synchronized**。

**静态方法**

synchronized修饰的静态方法同样保护的是一个对象，只不过是**类对象**。因此synchronized修饰的静态方法和实例方法保护的是不同的对象，他们可以同时执行。

**代码块**

synchronized代码块可以传入参数指定保护哪一个对象，比如`synchronized(this){//code block}`。

synchronized有三个特性：可重入性、内存可见性和可能出现死锁。

**可重入性**

可重入性表示：对同一个线程，它在获得了锁之后，如果要调用其他需要**同样锁**的代码，可以直接调用。并不是所有锁都是可重入的。

可重入是通过记录锁的**持有线程**和**持有数量**来实现的。

> 当调用被synchronized保护的代码时， 检查对象是否已被锁，如果是，再检查是否被当前线程锁定，如果是，增加持有数量，如果不是被当前线程锁定，才加入等待队列，当释放锁时，减少持有数量，当数量变为 0 时才释放整个锁。
>
> 马俊昌. Java编程的逻辑 (Java核心技术系列) (Kindle 位置 8524-8526). 北京华章图文信息有限公司. Kindle 版本. 

**内存可见性**

synchronized能保证内存可见性，在释放锁时，所有写入都会写回内存，而获得锁后，都会从内存读最新数据。

不过如果只是为了保证内存可见性，synchronized成本过高，可以直接使用关键字volatile修饰变量，保证读写到内存的最新值，而非缓存的值。

**死锁**

如果两个线程分别请求相同的两个锁，但是请求顺序相反，那么就可能出现死锁（相互等待对方释放锁）。为了避免死锁，应该：

- 尽量避免在持有一个锁的同时去申请另一个锁
- 如果要申请多个锁，所有代码都应该按照相同的顺序去申请锁

使用**jstack**可以检查是否发生了死锁，以及死锁的情况。

类`Collection`的一些方法可以返回线程安全的同步容器，比如`synchronizedCollection`、`synchronizedList`、`synchronizedMap`，他们是给**所有容器方法**都加上synchronized来实现安全的，因此性能比较低，一般使用下面这些容器类来实现线程安全：

- CopyOnWriteArrayList
- ConcurrentHashMap
- ConcurrentLinkedQueue
- ConcurrentSkipListSet

### 15.3 线程的基本协作机制

#### wait/notify

wait方法和notify方法都是Object类定义的方法。

因为wait/notify方法是围绕某一个**协作变量**工作的，容易出现竞态条件，所以一**般只能在synchronized代码块内被调用**。如果调用wait/notify时，当前线程没有持有锁，会抛出异常。

每个对象除了有一把锁和等待队列，还有一个**条件队列**用于线程间的协作。当调用wait方法时，会将当前线程放到条件队列上并阻塞，表示当前线程执行不下去了，需要等待一个**条件**，这个条件它自己改变不了，需要其他线程去改变。当其他线程改变了条件之后，会调用这个对象的**notify**方法。

notify方法的作用就是从条件队列中选择一个线程，将其从队列中**移除并唤醒**。notifyAll方法会移除条件队列中的所有线程并全部唤醒。

一般wait的调用方式是：

```java
synchronized(obj){
    while(条件不成立)
        obj.wait();
    ...//执行条件成立后的操作
}
```

其中的“条件”一般是指协作变量。

#### wait/notify的原理

虽然wait被synchronized保护，但是**调用wait时，线程会释放对象锁**。wait的过程为：

1. 把当前线程放入条件等待队列，释放对象锁，阻塞等待，线程状态变为WAITING或TIMED_WAITING；
2. 等待时间到或被其他线程调用notify/notifyAll时从条件队列中移除，这时要**重新竞争对象锁**：
   1. 如果能获得锁，线程状态变为RUNNABLE，并从wait调用中返回。
   2. 否则，线程进入对象锁等待队列，线程状态变为BLOCKED，只有在获得锁之后才会从wait调用中返回。

调用notify会把条件队列中等待的线程唤醒并从等待队列中移除，但是**不会立即释放对象锁**，只有在包含notify的synchronized代码块执行完后，等待的线程才会从wait调用中返回。

#### 生产者/消费者模式

生产者/消费者模式围绕一个队列工作，队列满时生产者需要**等待队列不满**，队列空时消费者需要**等待队列不空**。

实现这个模式的时候应该使用notifyAll而不是notify，避免唤醒的是同类线程。

Java提供了专门的阻塞队列实现：

- 接口BlockingQueue和BlockingDeque；
- ArrayBlockingQueue；
- LinkedBlockingQueue和LinkedBlockingDeque；
- PriorityBlockingQueue

#### 同时开始

在同时开始这个模型中，所有线程都**等待同一个开始信号**（协作变量）。

#### 等待结束

join()方法实际上调用的是wait，主要代码是：

```java
while(isAlive()){
    wait(0);
}
```

#### 集合点

多个线程分头行动，各自到达一个集合点，在集合点需要集齐所有线程，交换数据，然后进行下一步动作。协作的共享变量可以是线程的个数，等待条件为这个变量减为0。

### 15.4 线程的中断

停止一个线程的主要方法是中断（interrupt），**中断不是强迫终止一个线程，它是一种协作机制，给线程传递一个信号，但是由线程来决定如何以及何时退出**。

线程不同状态对interrupt的反应是不同的：

- RUNNABLE：如果再运行中且没有执行IO操作，interrupt只是设置中断标志位，没有其他作用。线程应该再运行过程中合适的位置检查中断标志位，比如循环的开始处。
- WAITING/TIMED_WAITING：会抛出InterruptedException，**中断标志位被清空**，所以抛出异常后输出isInterrupted会输出false。处理这类异常时，应该先清理，再调用interrupt设置中断标志位，告诉其他代码知道发生了中断。
- BLOCKED：只会设置线程的中断标志位，而线程依然会处于BLOCKED状态。**使用synchronized关键字获取锁的过程中不响应中断请求，显式锁Lock接口支持以响应中断的方式获取锁**。
- NEW/TERMINATE：没有任何效果，中断标志位也不会被设置。

作为线程的实现者，应该提供明确的取消/关闭方法，并用文档描述清楚其行为；作为线程的调用者，应该使用其取消/关闭方法，而不是贸然调用interrupt。

## 第16章 并发包的基石

### 16.1 原子变量和CAS

原子变量包括AtomicBoolean, AtomicInteger, AtomicLong, AtomicReference，以及针对数组类型的AtomicLongArray等。

原子变量的关键在于，它包含了一些以原子方式实现组合操作的方法，比如（以AtomicInteger为例）：

```java
int getAndSet(int newValue)
int getAndIncrement()
int getAndDecrement()
int getAndAdd(int delta)
int incrementAndGet()
int decrementAndGet()
```

 这些方法都依赖一个方法：`compareAndSet`，简称CAS。CAS操作有两个参数expect和update，如果当前值等于expect，则更新为update并返回true，否则不更新并返回false。

一般原子操作的主体都是死循环，更新失败则不断循环，直到更新成功为止。

CAS是乐观的，假设有较少的冲突情况；synchronized是悲观的，假设很可能发生冲突。

使用CAS方式更新有可能出现ABA问题，**这是一个与程序的逻辑有关的问题，一般不是问题**。Java中解决的办法是使用AtomicStampedReference，改值的同时附加一个时间戳，只有值和时间戳都相同才修改。

Java并发包中提供了一些非阻塞容器，比如：

- ConcurrentLinkedQueue和ConcurrentLinkedDeque：非阻塞并发队列
- ConcurrentSkipListMap和ConcurrentSkipListSet：非阻塞并发Map和Set

### 16.2 显式锁

显式锁位于java.util.concurrent.locks下；锁接口Lock，主要实现类是ReentrantLock；读写锁接口ReadWriteLock，主要实现类是ReentrantReadWriteLock。

Lock中的方法有：

- lock()/unlock()：普通的获取锁和释放锁，lock()会阻塞直到成功；
- lockInterruptibly()：获取锁，**可响应中断。**如果被中断了则抛出InterruptedException；
- tryLock()：**非阻塞**地尝试获取锁，**立即返回**。如果获取成功则返回true，否则返回false。
- tryLock(long time, TimeUnit unit)：先尝试获取锁，如果成功则立即返回true；否则阻塞等待，最长等待时间是time；在等待的同时响应中断，会抛出InterruptedException；如果等待后获得了锁则返回true，否则返回false。
- newCondition：新建一个条件，一个Lock可以关联多个条件。

使用显式锁，**一定要记得调用unlock**。一般将lock之后的代码包装到try语句内，在finally语句内释放锁。

**使用tryLock()可以避免死锁**。在持有一个锁获取另一个锁失败的时候，可以释放已持有的锁，然后重试获取所有锁。

#### ReentrantLock的实现原理

在最底层，ReentrantLock依赖于CAS，而在Java中首先是依赖于类LockSupport。

LockSupport类里包含这些方法：

```java
public static void park() 
public static void parkNanos( long nanos) 
public static void parkUntil( long deadline) 
public static void unpark( Thread thread)
```

park使得当前线程放弃CPU，进入等待状态（WAITING），操作系统不再对它进行调度，什么时候再调度呢？有其他线程对它调用了unpark，unpark使参数指定的线程恢复可运行状态。

**park是响应中断的**，当有中断发生时，park会返回，中断标志位会被设置。

parkNanos可以设置最长等待时间；parkUntil可以设置等待到什么时候，是相对于纪元时的毫秒数。

LockSupport中的park/unpark方法是调用了Unsafe类中的对应方法（和CAS相同）。

ReentrantLock直接依赖的类是**AQS**（AbstractQueuedSynchronizer）。

**AQS**

ReentrantLock内部有三个类和AQS有关：

```java
abstract static class Sync extends AbstractQueuedSynchronizer 
static final class NonfairSync extends Sync 
static final class FairSync extends Sync
```

ReentrantLock默认使用NonfairSync。ReentrantLock内部使用state表示是否被锁以及持有数量，如果未被锁定则立即获得锁，否则调用acquire(1)获得锁。acquire是AQS中的方法。

#### 对比ReentrantLock和synchronized

相比synchronized，ReentrantLock可以实现与synchronized相同的语义，而且支持以**非阻塞**方式获取锁，**可以响应中断**，**可以限时**，更为**灵活**。不过，synchronized的使用更为简单，写的代码更少，也更不容易出错。

synchronized代表一种**声明式编程思维**，程序员更多的是表达表达一种同步声明，由Java系统负责具体实现，程序员不知道其实现细节；显式锁代表一种**命令式编程思维**，程序员实现所有细节。

声明式编程的好处除了简单，还在于性能，在较新版本的JVM上，ReentrantLock和synchronized的性能是接近的，但Java编译器和虚拟机可以不断优化synchronized的实现，比如自动分析synchronized的使用，对于没有锁竞争的场景，自动省略对锁获取/释放的调用。

简单总结下，**能用synchronized就用synchronized**，不满足要求时再考虑Reentrant-Lock。

### 16.3 显式条件

**wait/notify与synchronized配合使用，显式条件与显式锁配合使用。**

创建条件变量需要调用显示锁的newCondition()方法。

*Condition*是一个接口，定义为：

```java
public interface Condition{
	void await() throws Interrupted Exception;
    void await Uninterruptibly();
    long awaitNanos(long nanosTimeout) throws InterruptedException;
    boolean await(long time,TimeUnit unit) throws InterruptedException;
    boolean awaitUntil(Date deadline) throws InterruptedException;
    void signal();
    void signalAll();
}
```

其中，await功能与wait类似，signal/signalAll与notify/notifyAll类似。*Condition*对象也是有wait和notify/notifyAll方法的，因为这些方法属于对象，但是在这里不能使用，因为wait和notify/notifyAll只能在synchronized中使用，一定不能混淆。

## 第17章 并发容器

### 17.1 写时复制

写时复制的类主要有*CopyOnWriteArrayList*和*CopyOnWriteArraySet*。

**写时复制的含义是：每次修改操作，都会新建一个数组，复制原数组的内容到新数组，在新数组上进行需要的修改，然后以原子方式设置内部数组的引用。**所有的读操作都是拿到当前引用的数组，然后直接访问该数组，修改操作不会影响读。

*CopyOnWriteArrayList*特点有:

- 线程安全，可以被多个线程并发访问；
- 迭代器不支持修改；
- 以原子方式支持一些复合操作：*addIfAbsent(E e)*和*addAllAbsent(Collection<? extends E> c)*。

*CopyOnWriteArraySet*是基于*CopyOnWriteArrayList*实现的。

### 17.2 ConcurrentHashMap

*HashMap*中可能发生的并发问题：多个线程同时扩容哈希表的时候，可能会出现死循环。

*ConcurrentHashMap*实现了*Map*接口和*ConcurrentMap*接口，*ConcurrentMap*接口定义了一些原子复合操作：

```java
V putIfAbsent(K key, V value)
boolean remove(Object key, Object value)
boolean replace(K key, V oldValue, V newValue)
V replace(K key, V value)
```

在Java 7中*ConcurrentHashMap*实现高并发的原因有：

- 分段锁
- 读不需要锁

内部使用了**CAS**实现，达到的效果是：**对于写操作，需要获取锁，不能并行，但是读操作可以，多个读可以并行，写的同时可以读**。

Java 8中对*ConcurrentHashMap*的实现进一步做了优化，首先是**在哈希冲突比较严重的时候将单链表转化为平衡的排序二叉树**，提高查找效率；其次是细化了锁的力度，改成了**每一个哈希桶（指向一个单链表或树）都有一个单独的锁**。

*ConcurrentHashMap*的**弱一致性**：创建迭代器后，会按照哈希表的结构遍历每个元素，但是在遍历中，内部元素可能会发生变化，如果变化发生在已遍历过的部分，迭代器就不会反映出来，而如果变化发生在未遍历过的部分，迭代器就会发现并反映出来。

### 17.3 基于跳表的Map和Set

*TreeMap*/*TreeSet*对应的并发版本是*ConcurrentSkipListMap*和*ConcurrentSkipListSet*，其中**ConcurrentSkipListSet是基于ConcurrentSkipListMap实现**的。

*ConcurrentSkipListMap*的特点有：

- 没有使用锁，所有操作都是无阻塞的，所有操作都可以并行，包括写，多线程可以同时写。
- 弱一致性
- 实现了*CurrentMap*接口，支持一些原子复合操作
- 可排序，实现了*SortedMap*和*NavigableMap*接口

### 17.4 并发队列

并发队列包括：

| 类型               | 包含的类                                                     |
| ------------------ | ------------------------------------------------------------ |
| 无锁非阻塞并发队列 | ConcurrentLinkedQueue、ConcurrentLinkedDeque                 |
| 普通阻塞队列       | 基于数组的ArrayBlockingQueue，基于链表的LinkedBlockingQueue和LinkedBlockingDeque |
| 优先级阻塞队列     | PriorityBlockingQueue                                        |
| 延时阻塞队列       | DelayQueue                                                   |
| 其他阻塞队列       | SynchronousQueue和LinkedTransferQueue                        |

**无锁非阻塞：**不使用锁，所有操作总是可以立即执行，主要通过**循环CAS**实现并发安全。

**阻塞队列：**使用**锁**和**条件**，很多操作都需要先获取锁或满足特定条件。获取不到锁或等待条件时，会等待（阻塞），获取到锁或满足条件再返回。

这些队列迭代都不会抛出*ConcurrentModificationException*，都是弱一致性的。

#### 无锁非阻塞并发队列

适用于多个线程并发使用一个队列的场合，特点有：

- 基于链表实现
- 没有限制大小

#### 普通阻塞队列

常用于**生产者/消费者模式**。

*ArrayBlockingQueue*基于循环数组实现，有界，创建时需指定大小，且再运行过程中不会改变。

*LinkedBlockingQueue*基于单向链表实现，可选择指定最大长度，默认无限。

内部，两者都是使用显示锁*ReentrantLock*和显式条件*Condition*实现。

#### 优先级阻塞队列

大部分结构都与*PriorityQueue*相同，包括使用堆结构，没有大小限制，要求元素实现*Comparable*结构或者主动提供Comparator等。

区别在于，*PriorityBlockingQueue*实现了*BlockingQueue*接口。使用*ReentrantLock*和*Condition*实现并发。

#### 延时阻塞队列

是一种特殊的优先级队列，无界，要求每个元素都实现*Delayed*接口。

*Delayed*扩展了*Comparable*接口，包含一个额外的方法*getDelay*，用于返回**再延迟多少时间**，如果小于或等于0则不再延迟。

*DelayQueue*可以用于实现定时任务，按元素的延时时间出列，只有当元素的延时过期之后才能被拿走。

*DelayQueue*是基于*PriorityQueue*实现的。

#### 其他阻塞队列

SynchronousQueue

LinkedTransferQueue

## 第18章 异步任务执行服务

### 18.1 基本概念和原理

异步任务执行服务设计到的基本接口有：

- *Runnable*和*Callable*：表示要执行的异步任务
- *Executor*和*ExecutorService*：表示执行服务
- *Future*：表示异步任务的结果

*Runnable*没有返回结果，而*Callable*有；*Runnable*不会抛出异常，而*Callable*会。

*Executor*表示最简单的执行服务，只包含一个`void execute(Runnable command)`；*ExecutorService*扩展了*Executor*，包含一系列的submit方法（返回*Future*）。

Future包含的方法有：

- cancel：取消异步任务
- get：返回异步任务的最终结果
- isCancelled
- isDone

get用于返回异步任务最终的结果，如果任务还未完成，会阻塞等待；另外有一个get可以设置时间，超时任务还未结束则会抛出*TimeoutException*。

任务最大概有三种结果：

- 正常完成，返回执行结果；如果是*Runnable*且未设置结果，返回null
- 任务执行抛出了异常，get方法会将异常包装位*ExcecutionException*重新抛出，通过异常的getCause可以获取原异常
- 任务被取消了，get方法会抛出异常*CancellationException*
- 调用get方法的线程被中断了，抛出*InterruptedException*

### 18.2 线程池

线程池主要由两个概念组成：**任务队列**和**工作者线程**。工作者线程主体是一个循环，循环从队列中接受任务并执行，任务队列保存待执行的任务。

线程池的优点有：

- 可以重用线程，避免线程创建的开销；
- 任务过多时，通过排队避免创建过多线程，减少系统资源消耗和竞争，确保任务有序完成。

Java并发包中线程池的实现类是*ThreadPoolExecutor*，它继承自*AbstractExecutorService*，实现了*ExecutorService*。

线程池的大小主要与4个参数有关：

- corePoolSize：核心线程个数；
- maximumPoolSize：最大线程个数；
- keepAliveTime和unit：空闲线程存活时间。

ThreadPoolExcecutor要求的队列类型是阻塞队列。

**ThreadPoolExecutor实现了生产者/消费者模式，工作者线程是消费者，任务提交者是生产者，线程池自己维护任务队列。当遇到类似生产者/消费者问题时，应该优先考虑直接使用线程池。**

### 18.3 定时任务

在Java中，主要有两种方式实现定时任务：

- 使用java.util包中的Timer和TimerTask
- 使用Java并发包中的ScheduledExecutorService

Timer内部主要由任务队列和Timer线程两部分组成。任务队列是基于堆实现的**优先级队列**，按照下次执行的时间排优先级。**一个Timer对象只有一个Timer线程**。

ScheduledThreadPoolExecutor的实现思路与Timer基本类似，都有一个基于堆的优先级队列，保存待执行的定时任务，不同在于：

- 它的背后是线程池，可以有多个线程执行任务；
- 它在任务执行后在设置下次执行的时间，对于固定延时的任务更为合理；
- 任务执行线程会捕获任务执行过程中的所有异常，一个定时任务的异常不会影响其他定时任务，不过，发生异常的任务不会再被调度。

## 第十九章 同步和协作工具类







## 第二十一章 反射

反射是在运行时，而非编译时，动态获取类型的信息，比如接口信息、成员信息、方法信息、构造方法信息等，根据这些动态获取到信息创建对象、访问/修改成员、调用方法等。

获取类对象的方法有：

- 使用Object类的`getClass`方法（基本类型不可用）
- 使用`类名.class`获取，基本类型可用
- 使用`Class.forName`方法根据类名获取类对象

反射和泛型

虽然在运行时，泛型参数会被擦除，但是类信息Class中依然有关于泛型的一些信息，可以通过反射得到：

```java
//Class中的
public TypeVariable<Class<T>> [] getTypeParameters() 

//Field中的    
public Type GetGenericType() 

//Method中的
public Type getGenericReturnType()
public Type[] getGenericParameterTypes()
public Type[] getGenericExceptionTypes()
    
//Constructor
public Type[] getGenericParameterTypes()
```

反射的优点是灵活，缺点有：

- 更容易出现运行时错误，因为使用反射的话类型是运行时才知道的，编译器无法检查；
- 反射的性能更低一些，在访问字段、调用方法前，反射要先查找对应的Field/Method

因此，如果能用接口实现同样的灵活性，就不要使用反射。

## 第二十二章 注解

Java的内置注解有：

| 注解             | 含义                                           |
| ---------------- | ---------------------------------------------- |
| @Override        | 表示重写了父类的该方法                         |
| @Deprecated      | 过时了，不建议使用                             |
| @SupressWarnings | 压制Java的编译警告，可以指定压制哪些类型的警告 |

创建注解的关键字为`@interface`，有两个元注解@Target和@Retention。

@Target表示注解的目标，是一个ElementType的枚举值，可以有多个（用`{}`表示）包含：

- TYPE：表示类、接口、注解或枚举声明
- FIELD：字段
- METHOD：方法
- PARAMETER：方法参数
- CONSTRUCTOR：构造方法
- LOCAL_VARIABLE：本地变量
- MODULE：模块（Java 9引入的）

**@Target默认适用于所有类型。**

@Retention表示注解信息保留到什么时候，值为RentionPolicy的枚举类型，可以是：

- SOURCE：仅保留到源代码
- CLASS：保留到字节码文件中
- RUNTIME：保留到运行时

**@Retention默认为CLASS。**

注解可以设置参数，不过参数类型只能是基本类型、String、Class、枚举、注解以及这些类型的数组。

可以用反射来获取注解类型：

```java
//获取所有注解
public Annotation[] getAnnotations();

//获取所有本元素上直接声明的注解
public Annotation[] getDeclaredAnnotations();

//获取指定类型的注解，没有返回null
public <A extends Annotation> A getAnnotation(Class<A> annotationClass);

//判断是否有指定类型的注解
public boolean isAnnotationPresent(Class<? extends Annotation> annotations)
```

## 第二十三章 动态代理

### 23.1 静态代理

```java
public class SimpleStaticProxyDemo { 
    static interface IService { 
        public void sayHello(); 
    } 
    static class RealService implements IService { 
        @Override
		public void sayHello() { 
            System. out. println(" hello"); 
        } 
    } 
    static class TraceProxy implements IService { 
        private IService realService; 
        public TraceProxy( IService realService) { 
            this. realService = realService; 
        } 
        @Override public void sayHello() { 
            System. out. println(" entering sayHello"); 
            this. realService. sayHello(); 
            System. out. println(" leaving sayHello"); 
        } 
    } 
    public static void main( String[] args) { 
        IService realService = new RealService(); 
        IService proxyService = new TraceProxy( realService); 
        proxyService. sayHello(); 
    } 
}
```

在上面的例子中，我们想达到的目的是在实际对象的方法调用前后加一些调试语句。为了在不修改原类 的情况下达到这个目的，我们在代码中创建了一个代理类 TraceProxy，它的代码是在写程序时固定的， 所以称为**静态代理**。

## 第24章 类加载机制

### 24.1 类加载的机制和过程

运行Java程序，就是执行java这个命令，指定包含main方法的完整类名，以及一个classpath，即类路径。类路径可以有多个，对于直接的class文件，路径是class文件的根目录，对于jar包，路径是jar包的完整名称（包括路径和jar包名）。

Java运行时，会根据类的**完全限定名**寻找并加载类，寻找的方式基本就是在系统类和指定的类路径中寻找，如果是class文件的根目录，则直接查看是否有对应的子目录及文件；如果是jar文件，则首先在内存中解压文件，然后再查看是否有对应的类。

**负责加载类的类就是类加载器，它的输入是完全限定的类名，输出是Class对象。**一般类加载器有3个（Java9之前）：

- 启动类加载器（Bootstrap ClassLoader）：是Java虚拟机实现的一部分，负责加载Java的基础类（String、ArrayList等）；
- 扩展类加载器（Extension ClassLoader）：负责加载扩展类，一般是位于JAVA_HOME/lib/ext中的jar包；
- 应用程序类加载器（Application ClassLoader）：负责加载应程序的类，包括自己写的和引入的第三方类库，即所有在类路径中指定的类。

**双亲委派模型**

加载一个类的过程为：

1. 判断是否已被加载，加载过了则直接返回Class对象，一个类只会被一个ClassLoader加载一次；
2. 如果没有被加载，先让父ClassLoader去加载，如果加载成功，返回得到的Class对象；
3. 在父ClassLoader没有加载成功的前提下，自己尝试加载类

双亲委派模型的好处是：避免Java类库被覆盖的问题。如果用户也定义了一个类java.lang.String，通过双亲委派，java.lang.String只会被Bootstrap ClassLoader加载，避免自定义的String覆盖Java类库中的定义。

### 24.2 ClassLoader和forName加载类的区别

调用Class的forName方法来加载类有两个方法：

```java
public static Class<?> forName(String className);
public static class<?> forName(String name, boolean initialize, ClassLoader loader);
```

forName是可以设置是否执行初始化代码（static语句块）的，而且默认执行；而ClassLoader的loadClass不会执行类的初始化代码。















