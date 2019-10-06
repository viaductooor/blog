---
title: 面试常见手撕代码题
tags: [interview]
categories: [interview]
layout: post
---

* content
{:toc}

# 常见排序算法的实现

## 堆排序

https://www.jianshu.com/p/11655047ab58

（升序排序）整体思路是：

1. 初始数组表示一个未排序的原始堆
2. 构造一个最大堆
3. 依次将堆顶元素交换到堆尾，调整使其保持最大堆，堆尾指针前移，直到所有元素有序（堆中之剩下堆顶元素）

## 快速排序

快排的核心部分在于partition的实现。partition需要找到一个元素的正确位置，将其移动到这个位置，并返回这个位置。

## 归并排序

https://www.cnblogs.com/chengxiao/p/6194356.html

归并排序的核心在于分治。分就是把原数组一分为二，然后再分，直到每一部分只剩下最多一个元素；并就是把结果合并，当每一部分最多有一个元素的时候可以直接交换，而多于一个元素的时候可以将有序的两部分从头到尾比较排序。

在合并的时候会用到额外的空间，因此可以在一开始就定义一块和原数组相同大小的空间用于合并，避免递归过程中频繁开辟新的空间。

# 链表

## 反转链表（递归和非递归）

<https://leetcode-cn.com/problems/reverse-linked-list/>

### 非递归（头插法）

```java
public ListNode reverseList(ListNode head) {
    ListNode newHead = null;
    while(head!=null){
        ListNode t = head.next;
        head.next = newHead;
        newHead = head;
        head = t;
    }
    return newHead;
}
```

### 递归（头插法）

```java
public ListNode reverseList(ListNode head) {
    return reverse(null, head);
}

public ListNode reverse(ListNode newHead, ListNode head) {
    if(head==null){
        return newHead;
    }
    else{
        ListNode t = head.next;
        head.next = newHead;
        newHead = head;
        return reverse(newHead, t);
    }
}
```

## 倒数第K个节点

使用双指针的时候要注意链表的长度，要考虑第二个指针在第一次前进k个节点时出现null的情况。

## 检测环

使用快慢指针。

### 引申问题1：为什么快指针的步长是2，而不是3、4、5...？

https://blog.csdn.net/xgjonathan/article/details/18034825

结论：

1. 无论步长为多少，快慢指针在环里都能相遇
2. 步长设置为2能最快确定有环

### 引申问题2：如何确定环的入口？

> s：从起始点到环入口的距离
>
> cl：环长度

当慢指针刚好进入环中，也就是慢指针走了 s 步之后，快指针走了 2s 步，所以快指针在环中走了 2s - s = s 步；
由于存在 s > cl 的情况，我们记快指针超出 ks（k为自然数） 的距离是 s % cl ；
此时，快指针需要追及慢指针的距离是 cl - s % cl；
因此，当慢指针在环中走了cl - s % cl 步后，快指针追上了慢指针；

所以，相遇之后的慢指针距离 ks 的距离是 cl - (cl - s % cl) = s % cl 。因为有环的存在，我们可以把这个距离看成 s + M * cl，M 是正整数。所以相遇时的慢指针距离环的起始结点ks 是 s 。这时，我们再设置另一个指针从单向链表的头开始，以步长为 1 移动，移动 s 步后相遇，而这个相遇结点正好就是环的起始结点。

# 二叉树

## 二叉树的递归遍历以及（前中后序）非递归遍历

https://www.cnblogs.com/dolphin0520/archive/2011/08/25/2153720.html

## 二叉树的层序遍历（使用/不使用额外的数据结构）

https://blog.csdn.net/m0_37925202/article/details/80796010

# 图（矩阵）的深度优先和广度优先遍历

https://blog.csdn.net/jeffleo/article/details/53309286

# 动态规划

## 最长公共子串

https://segmentfault.com/a/1190000002641054

## 最长递增子序列

https://blog.csdn.net/u013178472/article/details/54926531

## 最长回文串

https://blog.csdn.net/kangroger/article/details/37742639

# 实现一个LRU的Cache

设计一个类LRUCache，满足以下条件：

1. 这个类的构造函数应该传入一个int类型的参数size，表示最多可容纳多少个元素
2. 包含方法put、get。put是放入新元素，get是获取某一个元素
3. 当LRUCache满了之后删除最近最久未使用的元素。

## 1. 使用Java自带的LinkedHashMap

这种方法比较简单，就是维护一个LinkedHashMap，重写它的removeEldestEntry方法。

LinkedHashMap的三个构造参数分别表示初始大小、扩张因子、使用accessOrder排序。如果不指定第三个参数，则会按照添加的顺序排序。

```java
public class LRUCache {
    private LinkedHashMap<String,String> cache;
    private int maxsize;
    public LRUCache(int maxsize){
        this.maxsize = maxsize;
        this.cache = new LinkedHashMap<String,String>(16,0.75f,true){
            @Override
            protected boolean removeEldestEntry(Map.Entry<String,String> eldest) {
                return this.size()>maxsize;
            }
        };
    }
    public void put(String key,String value){
        cache.put(key,value);
    }
    public String get(String key){
        return cache.get(key);
    }
}
```



## 2. 仅使用Java中的HashMap和Deque

本质上LRUCache就是一个HashMap，只不过需要使元素之间维持一定的顺序。LinkedHashMap实现了这个功能。如果不使用LinkedHashMap的话，则需要使用与其类似的思路，使用HashMap存储键值对，然后使用一个双端队列来记录访问顺序。这样效率很低，因为查询的时候需要先从队列中找到对应的key，完成一系列出队、入队操作最后再将这个key入队。

```java
public class LRUCache {
    private HashMap<String,String> cache;
    private Deque<String> orderQueue;
    private int maxsize;

    public LRUCache(int maxsize){
        this.maxsize = maxsize;
        this.orderQueue = new ArrayDeque<>();
        this.cache = new HashMap<>();
    }
    public void put(String key,String value){
        if(cache.size()>=maxsize){
            String eldestKey = orderQueue.poll();
            cache.remove(eldestKey);
        }
        cache.put(key,value);
        orderQueue.offer(key);
    }
    public String get(String key){
        if(cache.size()<1||!cache.containsKey(key)){
            return null;
        }
        Deque<String> stack = new ArrayDeque<String>();
        String e = null;
        while(!(e=orderQueue.poll()).equals(key)){
            stack.push(e);
        }
        while(!stack.isEmpty()){
            orderQueue.offerFirst(stack.pop());
        }
        orderQueue.offer(e);
        return cache.get(key);
    }
}

```

# 实现一个阻塞队列

阻塞队列的实现需要保证多线程围绕队满/队空这两个条件来协作。当队满时，添加元素的线程应当阻塞；当队空时，获取元素的线程应当阻塞。

```java
public  class  MyBlockingQueue<T>{
    private volatile List<T> queue;
    private int size;

    public MyBlockingQueue(int size){
        queue = new ArrayList<>();
        this.size = size;
    }

    public synchronized void put(T element) throws InterruptedException {
        while(queue.size()>=size){
            wait();
        }
        if(queue.size()==0){
            // 如果为0，则可能有其他线程在阻塞get，因此调用notifyAll
            notifyAll();
        }
        queue.add(element);
    }
    public synchronized T get() throws InterruptedException {
        while(queue.isEmpty()){
            wait();
        }
        if(queue.size()>=size){
            // 如果为size，则可能有其他线程在阻塞put，因此调用notifyAll
            notifyAll();
        }
        return queue.remove(0);
    }
}
```

# 实现生产者/消费者模型

使用上面的阻塞队列实现：

```java
public class ProducerConsumer{
    private static MyBlockingQueue<Integer> queue;
    static class Producer extends Thread{
        private MyBlockingQueue<Integer> queue;

        public Producer(MyBlockingQueue<Integer> queue){
            super();
            this.queue = queue;
        }

        @Override
        public void run(){
            Random rnd = new Random();
            for(int i=0;i<100;i++) {
                try {
                    queue.put(i);
                    System.out.println("Producing "+i);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    static class Consumer extends Thread{
        private MyBlockingQueue queue;

        public Consumer(MyBlockingQueue<Integer> queue){
            super();
            this.queue = queue;
        }

        @Override
        public void run(){
            while(true){
                try {
                    System.out.println("Consuming "+queue.get());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    public static void main(String[] args){
        MyBlockingQueue<Integer> queue = new MyBlockingQueue<>(10);
        Producer p = new Producer(queue);
        Consumer c = new Consumer(queue);
        p.start();
        c.start();
    }
}

```

# 实现两个线程交替打印

两个线程围绕一个合作变量flag进行合作。

需要将flag声明为volatile，否则程序会卡住，这跟JMM有关系。理论上说，每次工作线程修改了flag都迟早会同步到主内存，但是如果while循环体为空的话，**访问flag会太频繁导致JVM来不及将修改后的值同步到主内存**，这样一来程序就会一直卡在一个位置。

如果不使用volatile也是可以的，将循环体里面加上一句`System.out.print("")`就行了，这样可以降低访问flag的频率，从而使JVM有空将工作内存中的flag和主内存中的flag进行同步。

```java
public class PrintAlternately {
    static volatile int flag = 0;
    static class EvenThread extends Thread{
        @Override
        public void run(){
            for(int i=0;i<101;i+=2){
                while(flag!=0){}
                System.out.println(i);
                flag = 1;
            }
        }
    }
    static class OddThread extends Thread{
        @Override
        public void run(){
            for(int i=1;i<100;i+=2){
                while(flag!=1){}
                System.out.println(i);
                flag = 0;
            }
        }
    }
    public static void main(String[] args){
        EvenThread e = new EvenThread();
        OddThread o = new OddThread();
        e.start();
        o.start();
    }
}
```

# 文件的输入和输出

最长见的方法是使用BufferedReader逐行（字符流）读取文件，使用FileWriter或者BufferedWriter（字符流）写文件，区别是后者提供了方法newLine来写换行符，而前者需要手动写入`\n`或者`\r\n`来换行。

如果要求写文件的时候是在文件末尾添加，而不是整体覆盖，则FileWriter构造函数需要传入第二个参数true（表示使用append模式）。

```java
public class WriterReader {
    public static void main(String[] args){
        try(BufferedReader reader = new BufferedReader(new FileReader("from.txt"));
            BufferedWriter writer = new BufferedWriter(new FileWriter("to.txt"));){
            String line = null;
            while((line=reader.readLine())!=null){
                writer.write(line);
                writer.newLine();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

# 字符串转整型

思路：应该尽快调试出来一个转化的程序，然后再考虑各种情况。面试时手撕代码时间非常有限，所以优先使代码能工作，而不是所有情况都考虑全了，发现时间不够或者疯狂调bug。

先是一个合法字符串转正整数的方法：

```java
public int transform(String str){
    int res = 0;
    int base = 1;
    for(int i=str.length()-1;i>-1;i--){
        char ch = str.charAt(i);
        res = res + base*(ch-'0');
        base *= 10;
    }
    return res;
}
```

然后再考虑下面这些情况：

- 正负号
- 小数点
- 溢出
- 非法字符

考虑所有问题后，写出来的参考代码为：

```java
public class Solution {
    public static int transform(String str) throws Exception{
        String input = str;
        
        // 先判断是否合法
        if(!valid(str)){
            throw new Exception("Invalid input string!");
        }

        // 判断正负
        boolean isNegative = false;
        if(str.charAt(0)=='-'){
            isNegative = true;
            input = input.substring(1,input.length());
        }

        // 去掉前面的0
        int t = 0;
        while(input.charAt(t)=='0'){
            t += 1;
            if(t>input.length()-1){
                return 0;
            }
        }
        input = input.substring(t, input.length());

        // 去掉小数点
        int pointIndex = getPointIndex(input);
        int extra = 0;
        if(pointIndex>-1){
            if(pointIndex==input.length()-1){
                input = input.substring(0, input.length()-1);
            }else if(input.charAt(pointIndex+1)>'4'){
                extra = 1;
                input = input.substring(0, pointIndex);
            }else{
                input = input.substring(0, pointIndex);
            }
        }

        // 第一个字符是小数点，则输出只能是0或1
        if(input.length()<1){
            return extra;
        }
        
        // 溢出判断
        String MAXPOS = Integer.MAX_VALUE+"";
        String MAXNEG = Integer.MIN_VALUE+"";
        if(isNegative){
            String temp = '-'+input;
            if(temp.length()>MAXNEG.length()){
                throw new Exception("Overflow error!");
            }else if(temp.length()==MAXNEG.length()){
                if(temp.compareTo(MAXNEG)>0){
                    throw new Exception("Overflow error!");
                }else if(temp.compareTo(MAXNEG)==0&&extra>0){
                    throw new Exception("Overflow error!");
                }
            }
        }else{
            if(input.length()>MAXPOS.length()){
                throw new Exception("Overflow error!");
            }else if(input.length()==MAXPOS.length()){
                if(input.compareTo(MAXPOS)>0){
                    throw new Exception("Overflow error!");
                }else if(input.compareTo(MAXPOS)==0&&extra>0){
                    throw new Exception("Overflow error!");
                }
            }
        }

        //当前的字符串为合法的，无符号的，只包含数字的字符串，可以直接转化为数字（按需要添加负号）
        int res = 0;
        int base = 1;
        for(int i=input.length()-1;i>-1;i--){
            char ch = input.charAt(i);
            res = res + base*(ch-'0');
            base *= 10;
        }
        return res*(isNegative?-1:1);
    }

    public static int getPointIndex(String str){
        for(int i=0;i<str.length();i++){
            if(str.charAt(i)=='.'){
                return i;
            }
        }
        return -1;
    }

    public static boolean valid(String str){
        if(str.charAt(0)=='-'&&str.length()>1){
            return valid(str.substring(1, str.length()));
        }
        int nPoint = 0;
        for(char ch:str.toCharArray()){
            if(ch=='.'){
                nPoint += 1;
                if(nPoint>1){
                    return false;
                }
            }else if(ch>'9'||ch<'0'){
                return false;
            }
        }
        return true;
    }

    public static void main(String args[]){
        //-2147483648 ~ 2147483647
        String[] strs = new String[]{
            "-2147483648.6", //Overflow error!
            "2147483647.1", //2147483647
            "0.67", //1
            "adfasdf", //Invalid input string!
            "2147483648", //Overflow error!
            "1.1.5", //Invalid input string!
            "123.", //123
            ".67" //1
        };
        for(String s:strs){
            try{
                System.out.println(transform(s));
            }catch(Exception e){
                System.out.println(e.getMessage());
            }
            
        }
    }
}
```