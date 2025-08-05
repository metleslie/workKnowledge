## 1.并行和并发的区别
并行：多个任务在多个cpu上同时处理
并发：多个任务在一个cpu上同一时间段内交替执行，通过时间片轮转实现交替执行，用于解决IO密集型任务的瓶颈。

### 如何理解线程安全？
如果一个方法或一段代码被多个线程同时执行，还能够正确的共享和处理数据那么就能证明这个代码或方法是线程安全的。

三个要素保证线程安全：
原子性：一个操作要么全部执行，要么全部不执行，不能处于中间状态。可以通过锁来实现。
可见行：当一个线程修改了共享变量，其他线程能够立即看到变化。
**有序性**：要确保线程不会因为死锁、饥饿、活锁等问题导致无法继续执行。


## 2.进程和线程的区别
进程：操作系统中分配资源的最小单位
线程：cpu能够调度的最小单位

==线程是进程里面的独立执行单位，它们可以共享同一个进程的资源，内存等，每个线程都有自己独立的栈和寄存器。

## 3.线程有几种创建方式？
三种常用的方式

### 1.继承Thread
创建一个类继承Thread重写run方法
```
package xc.xccj;  
  
//继承Thread  
public class MyThread extends Thread{  
    @Override  
    public void run(){  
        for (int i = 0; i < 100; i++) {  
            System.out.println(getName()+"打了"+i+"个小兵。");  
        }  
    }  
  
    public static void main(String[] args) {  
        Thread thread = new MyThread();  
        Thread thread2 = new MyThread();  
        Thread thread3 = new MyThread();  
  
        thread.setName("虞姬");  
        thread2.setName("后羿");  
        thread3.setName("狄仁杰");  
  
        //启动线程  
        thread.start();  
        thread2.start();  
        thread3.start();  
  
    }  
}
```


### 2.实现Runnable接口
创建一个实现Runnable接口的类，重新run方法。
```package xc.xccj;  
  
public class MyRunnable implements Runnable {  
    @Override  
    public void run() {  
        for (int i = 0; i < 100; i++) {  
            try {  
                //暂停20毫秒看出差异  
                Thread.sleep(20);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
            System.out.println(Thread.currentThread().getName()+"击杀了第"+i+"个小兵。");  
        }  
    }  
  
    public static void main(String[] args) {  
        MyRunnable myRunnable = new MyRunnable();  
  
        new Thread(myRunnable,"张飞").start();  
        new Thread(myRunnable,"赵云").start();  
        new Thread(myRunnable,"刘备").start();  
  
    }  
}
```

### 3.实现Callable接口
可以实现Callable接口，重写run方法，并可以通过FutureTask获取任务执行的返回值
```package xc.xccj;  
  
import java.util.concurrent.Callable;  
import java.util.concurrent.ExecutionException;  
import java.util.concurrent.FutureTask;  
  
public class CallerTask implements Callable<String> {  
    @Override  
    public String call() throws Exception {  
        return "Hello I am running";  
    }  
  
    public static void main(String[] args) {  
        FutureTask<String> task = new FutureTask<>(new CallerTask());  
        new Thread(task).start();  
  
        try {  
            String s = task.get();  
            System.out.println(s);  
        } catch (ExecutionException e) {  
            throw new RuntimeException(e);  
        } catch (InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
    }  
}
```


### 重写run方法？
默认run方法是空，重写run方法是我们线程要做的任务。
### run方法和start的方法区别？
run:封装线程执行的代码，直接调用相当用调用普通方法。
start():启动线程，然后由JVM调用该方法。

### **通过继承 Thread 的方法和实现 Runnable 接口的方式创建多线程，哪个好？**
实现Runnable好，Java单继承，如果继承Thread就不能用别的继承类。
适合多个相同的程序代码去处理同一资源的情况，把线程、代码和数据有效的分离，更符合面向对象的设计思想。Callable 接口与 Runnable 非常相似，但可以返回一个结果。

## 4为什么不直接调用run方法？
调用run方法相当于主线程直接调用了一个方法，start是jvm用底层调度机制去启动一个新的线程。

![[Pasted image 20250731150616.png]]

## 5.线程有哪些常用的调度方法？
![[Pasted image 20250731150738.png]]

## 6.线程有几种状态？
6种
- new是新建状态但未启动
- runnable线程处于就绪或正在运行状态
- blocked代表线程被阻塞等待获取锁
- waitting代表等待其他线程的通知或中断
- timed-witting代表线程会等待一段时间，超时自动回复
- terminated代表线程执行完毕，生命周期结束
![[Pasted image 20250731151315.png]]
线程生命周期主要分为五个主要阶段：新建，就绪，运行，阻塞和终止。

### 如何终止线程？
1.调用interrupt方法，请求终止线程。
2.在线程的run方法中检查中断状态，如果线程被中断就退出线程

```
class MyTask implements Runnable {
    @Override
    public void run() {
        while (!Thread.currentThread().isInterrupted()) {
            try {
                System.out.println("Running...");
                Thread.sleep(1000); // 模拟工作
            } catch (InterruptedException e) {
                // 捕获中断异常后，重置中断状态
                Thread.currentThread().interrupt();
                System.out.println("Thread interrupted, exiting...");
                break;
            }
        }
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(new MyTask());
        thread.start();
        Thread.sleep(3000); // 主线程等待3秒
        thread.interrupt(); // 请求终止线程
    }
}
```

## 7.线程上下文切换
并发是一个cpu在同一时间段内执行多个线程，在某一时刻只能执行一个，为了实现多线程的并发，需要不断在多个线程来回切换。
上下文切换就是指cpu从一个线程到另一个线程的过程。
在切换的过程中需要保存当前线程的状态，并加载下一个线程的上下文。

为了让用户感觉多线程同时执行，cpu的资源分配采用时间片的形式，线程在时间片内占用资源执行任务，使用完当前时间片后，会让其他线程使用。

### 多核调度？
多核处理器提供了并行执行多个线程的能力，每个核心单独执行一个或多个线程，操作系统的任务调度器会根据策略和算法，如优先级调度、轮转调度等，决定哪个线程何时在哪个核心上运行。


## 8.守护线程
守护线程是一种特殊线程，用来服务其他线程的，当所有的非守护线程结束了，它也自动结束了，即使还在运行也会被强制结束。

例如常见的日志记录，定时器，心跳检测，GC回收都是守护线程。

设置守护线程为在start()前调用setDaemon（true）方法。

## 9.线程间的通信方式

线程之间传递信息的方式有多种，比如说使用 volatile 和 synchronized 关键字共享对象、使用 `wait()` 和 `notify()` 方法实现生产者-消费者模式、使用 Exchanger 进行数据交换、使用 Condition 实现线程间的协调等

### 简单说说 volatile 和 synchronized 的使用方式？
| 关键字            | 作用                               |
| -------------- | -------------------------------- |
| `volatile`     | 保证**可见性**（你改了我马上能看到），但**不保证原子性** |
| `synchronized` | 保证**可见性 + 原子性 + 互斥**（加锁）         |
举例：
```
public class TestVolatile {  
    static volatile boolean flag = false;  
  
    public static void main(String[] args) {  
        new Thread(()->{  
            //一直循环  
            while (!flag){  
  
            }  
            System.out.println("flag变true");  
        }).start();  
  
        try {  
            Thread.sleep(1000);  
        }catch (Exception e){  
  
        }  
        flag = true;  
  
    }  
}
```
这段代码，是一直循环的在不佳volatile的情况下，虽然主线程改了该flag的值，但是自线程一直看不到的。
volatile关键字就是保证可见性的，主线程改动以后，子线程能立马看到。
但不保证原子性，有可能一条线程没执行完，被另一个线程读到了原来的值。

synchronized可以修饰方法或者同步代码块。是一个互斥锁，只允许单个线程拿到资源。

### wait和notify方法的使用方式了解嘛？
线程调用wait方法时，会进入该对象的等待池，释放已经持有的锁，进入等待状态。
调用notify的时候，会唤醒对象等待池的一个对象，使其进入锁池，等待获取锁。

```
package xc.suo;  
  
public class MessageBox {  
    private String message;  
    private boolean empty = true;  
  
    public synchronized void produce(String message){  
        while (!empty){  
            try {  
                wait();  
            }catch (InterruptedException e){  
                Thread.currentThread().interrupt();  
            }  
        }  
        empty = false;  
        this.message = message;  
        notifyAll();  
    }  
  
    public synchronized String consume(){  
        while (empty){  
            try {  
                wait();  
            }catch (InterruptedException e){  
                Thread.currentThread().interrupt();  
            }  
        }  
        empty = true;  
        notifyAll();  
        return message;  
    }  
  
    public static void main(String[] args) {  
        MessageBox box = new MessageBox();  
  
        new Thread(()->{  
            for (int i = 0; i < 5; i++) {  
                box.produce("消息 " + i);  
                System.out.println("生产者发送消息"+i);  
            }  
        }).start();  
  
        new Thread(()->{  
            for (int i = 0; i < 5; i++) {  
                String message = box.consume();  
                System.out.println("消费者接收消息： " + message);  
            }  
        }).start();  
  
    }  
}
```
这是一个生产者消费者的例子，当empty为空的时候，生产者生产消息，生产以后empty变为false，然后唤醒消费者去消费该消息，消费者消费之后唤醒生产者去生产这样一个例子。


### Exchange的使用方式？
exchange是一个线程之间交换数据的方法，一个线程调用exchange（）方法，将数据传递给另一个线程，同时获取另一个线程的数据

```
Exchanger<String> exchange = new Exchanger<>();  
new Thread(()->{  
    try {  
        String message = "线程1";  
        String response = exchange.exchange(message);  
        System.out.println("线程1："+response);  
    } catch (InterruptedException e) {  
        Thread.currentThread().interrupt();  
    }  
}).start();  
  
new Thread(()->{  
    try {  
        String message = "线程2";  
        String response = exchange.exchange(message);  
        System.out.println("线程1："+response);  
    } catch (InterruptedException e) {  
        Thread.currentThread().interrupt();  
    }  
}).start();
```
两个线程互相交换message

### CompletableFuture是什么？
Java8引入的一个类，他支持异步编程，允许线程在完成计算后将结果传递给其他线程。

可以执行异步任务
可以把多个任务拼接起来（任务完成后执行回调）
可以组合多个任务（并行执行，等待结果）

```
package xc.suo;  
  
import java.util.concurrent.CompletableFuture;  
import java.util.concurrent.ExecutionException;  
  
public class CompletableFutureTest {  
    public static void main(String[] args) throws ExecutionException, InterruptedException {  
        /**  
        启动异步任务  
         **/  
  
        /*CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {  
            System.out.println("异步任务执行中："+Thread.currentThread().getName());  
        });        //阻塞等待执行结果  
        future.get();  
        System.out.println("主线程结束");*/  
  
        /**  
        有返回值的异步任务  
         **/  
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {  
            return "返回异步任务名称"+Thread.currentThread().getName();  
        });  
  
        System.out.println(future.get());  
  
        /**  
         * 任务完成后的回调  
         */  
        CompletableFuture.supplyAsync(() -> 42)  
                .thenApply(result->result*2)  
                .thenAccept(result-> System.out.println("最终结果"+result));  
  
        /**  
         * 组合任务  
         */  
        //两个任务都完成后再执行  
  
        CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> 10);  
        CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> 20);  
  
        CompletableFuture<Integer> result = f1.thenCombine(f2, (x, y) -> x + y);  
  
        System.out.println("结果：" + result.get()); // 输出 30  
        //任意一个完成就执行  
        CompletableFuture<String> f3 = CompletableFuture.supplyAsync(() -> "来自任务1");  
        CompletableFuture<String> f4 = CompletableFuture.supplyAsync(() -> "来自任务2");  
  
        CompletableFuture<String> result2 = f3.applyToEither(f4, s -> "最快的是：" + s);  
  
        System.out.println(result2.get());  
  
    }  
}
```
举例的是常见方法
runAsync这个是直接执行
supplyAsync这个会有返回值
thenApply完成后的回调，thenAccept完成后的接收值
thenCombine两个任务都完成
applyToEither完成其中一个即可

## 10.sleep()和wait()的区别
所属类不同
- sleep()属于Thread类
- wait()属于
锁行为不同
- sleep方法调用的时候，锁不会在该线程释放，时间过后自动继续执行
- wait方法调用的时候，锁会在该线程被释放，等到超时或者被别人用notify唤醒尝试获取锁继续执行。
使用条件不同
- sleep可以在任意地方使用
- wait只能在同步代码块或方法中使用，因为使用wait的前提是当前线程必须持有对象锁。否则会跑出IllegalMonitorStateException异常。
唤醒方式不同
- 调用sleep方法，线程进入Timed-waiting状态，时间结束进入runnable
- wait是进入watiing状态，直到别的线程调用notify方法或超时才从waiting进入到runnable

## 11.怎么保证线程安全
线程安全是指在并发环境下，多个线程访问共享资源时，程序能够正确运行，不会出现数据不一致的问题。

为了保证线程安全，可以使用 [synchronized 关键字](https://javabetter.cn/thread/synchronized-1.html)对方法加锁，对代码块加锁。线程在执行同步方法、同步代码块时，会获取类锁或者对象锁，其他线程就会阻塞并等待锁。

如果需要更细粒度的锁，可以使用 [ReentrantLock 并发重入锁](https://javabetter.cn/thread/reentrantLock.html)等。

如果需要保证变量的内存可见性，可以使用 [volatile 关键字](https://javabetter.cn/thread/volatile.html)。

对于简单的原子变量操作，还可以使用 [Atomic 原子类](https://javabetter.cn/thread/atomic.html)。

对于线程独立的数据，可以使用 [ThreadLocal](https://javabetter.cn/thread/ThreadLocal.html) 来为每个线程提供专属的变量副本。

对于需要并发容器的地方，可以使用 [ConcurrentHashMap](https://javabetter.cn/thread/ConcurrentHashMap.html)、[CopyOnWriteArrayList](https://javabetter.cn/thread/CopyOnWriteArrayList.html) 等。



# ThreadLocal

## 12.ThreadLocal是什么
ThreadLocal提供了线程局部变量：
- 每个线程都能存一份自己的数据
- 互不干扰，线程之间相互隔离

### 为什么需要ThreadLocal
正常情况下，多线程访问同一个变量我们需要加锁来保证安全
但有些情况下，我们不想让线程共享变量，而是每一个线程保留一份副本，这就用刀ThreadLocal来解决
例如
- 每个线程维护自己的信息（登陆信息）
- 数据库连接，session对象在线程中共享
- 格式化工具 `SimpleDateFormat`（它不是线程安全的，可以用 `ThreadLocal` 让每个线程有自己的对象）

优点：变量副本独立，避免共享变量引起的线程安全问题。由于ThreadLocal实现了变量的线程独占，使得变量不需要同步处理，因此能够避免资源竞争。

ThreadLocal 可用于跨方法、跨类时传递上下文数据，不需要在方法间传递参数。

## 13.使用场景有哪些？
最常见的是用户信息，我们的MVC三层架构，在登录时会把用户信息放到token里，在控制层解析用户信息。
如果在服务层和持久层也要用到这个信息，就不需要层层往下传，使用ThreadLocal存入。

例如其他的CookieSession也可以使用它去进行数据隔离。

## 14.ThreadLocal怎么实现的

Thread里面有个ThreadLocalMap,ThreadLocal本身并不存数据。
存入的时候，把ThreadLocal作为key，value作为值存入。
取得时候也是一样，保证每个线程用同一个ThreadLocal变量互不影响。
```
public void set(T value) {
    Thread t = Thread.currentThread();         // 取当前线程
    ThreadLocalMap map = t.threadLocals;       // 获取该线程的 ThreadLocalMap
    if (map != null) {
        map.set(this, value);                  // this 就是 ThreadLocal 自己
    } else {
        createMap(t, value);
    }
}

```
set方法简化。

所有线程都是基于 `Thread`，但**每个线程各自有一个 ThreadLocalMap**，它们不共享。  
一个 `ThreadLocal` 变量，在不同线程里，会对应不同的值。

ThreadLocalMap类似于Map结构，专门为ThreadLocal定制的。
key是弱引用，为什么用弱引用
==是因为避免内存泄漏，如果ThreadLocal没人用了，GC会回收。==
==value不会自动回收，需要使用remove方法移除==

## 15.内存泄漏是怎么回事
ThreadLocalMap的key是弱引用，但Value是强引用。
如果一个线程一直运行，并且value一直指向某个强引用对象，这个对象不会被回收。胆汁里面的Vlaue越来越多，导致内存泄漏。

### 如何解决内存泄漏？
使用完ThreadLocal即使调用remove方法清楚Value。

### 为什么要设计成弱引用？
弱引用的好处，当内存不足时，JVM能够及时回收掉对象。


# Java内存模型

线程分为本地内存和共享内存，本地内存存储共享内存的副本。
当一个本地内存修改了变量副本需要JVM刷新到主内存中，同样的当需要读取的时候先读本地的然后再看是否过时，过时了再去公共内存的值刷新到本地，本地内存和共享内存中间的交互由JMM控制。

Java内存模型是一个抽象模型，是用来解决多线程之间变量的交互问题。
解决的是可见性，原子性以及有序性的三大并发问题。它通过happend-before规则保证在哪些操作之间必须保持顺序性和可见性。常用的手段有volatile（可见性+禁止部分重排）syn（互斥+可见性）以及线程启动终结等机制。确保多线程下的执行结果可预测。

Java用的是内存共享模型，通过读写内存中的公共状态进行隐式通信。

Happes-before规则：
程序顺序执行规则
锁规则
volatile规则
主要是这三个规则

## 是什么指令重排？
我们在多线程运行的时候有时候一个操作需要多个数据，我们在加载的时候需要等待多个数据加载完成，这时候我们可以在空余的时间内执行别的操作，来提高cpu的效率。

从Java源代码到最终执行的指令排序，会经历三种
- 编译器重排序
- 指令并行重排序
- 内存系统重新排序

指令重排可能会导致一些操作失效例如双重检查锁机制。
双重检查锁机制是一种延迟初始化（懒加载）单例的写法。

第一次用到的时候才创建。
线程安全
减少锁开销（只在第一次创建时加锁，后续直接读）

==双重检查锁失效是指令重排的问题，在我定义对象指向这块内存的时候但是内存还没初始化完成，另一个线程第一次检查发现instance不为空了因为对象已经指向了内存，直接返回导致拿到的是半初始化的对象导致报错。==

解决该问题办法：
加入volatile关键字，加入关键字后会遵循happens-before规则，在线程a未完成前，线程b不会去读取，禁止构造过程重排。

## happens-before规则？
它是Java内存模型一种用来保证线程间可见性和顺序性的规则。

如果A happens-before B
那么B一定在A执行后执行
A的结果B一定能读取到

有哪些规则：
==程序性规则：只在单线程内，代码按顺序执行。==
==监视器锁定规则：解锁后别的线程能看到最新数据。==
==volatile规则：写volatile变量happens-before读volatile规则==
传递性规则：A-B B-C 则A-C
线程启动规则：线程 A 执行操作 `ThreadB.start()`，那么 A 线程的 `ThreadB.start()` 操作 happens-before 于线程 B 中的任意操作。
线程终止规则：线程的所有操作 Happens-Before `Thread.join()`；例如 `t.join();` 之后，主线程一定能看到 t 的修改。


## volatile关键字？
两个作用：
- 保证可见性，线程修改它之后，其他的线程能够看到最新的值。
- 防止指令重排，它的写入不会被重排到它之前的代码，算是一个基准线。

### 怎么保证可见性
==内存屏障==
当线程对 volatile 变量进行写操作时，JVM 会在这个变量写入之后插入一个写屏障指令，这个指令会强制将本地内存中的变量值刷新到主内存中。
当线程对 volatile 变量进行读操作时，JVM 会插入一个读屏障指令，这个指令会强制让本地内存中的变量值失效，从而重新从主内存中读取最新的值。

### 怎么保证有序性
JVM 会在 volatile 变量的读写前后插入 “内存屏障”，以约束 CPU 和编译器的优化行为：

- StoreStore 屏障可以禁止普通写操作与 volatile 写操作的重排
- StoreLoad 屏障会禁止 volatile 写与 volatile 读重排
- LoadLoad 屏障会禁止 volatile 读与后续普通读操作重排
- LoadStore 屏障会禁止 volatile 读与后续普通写操作重排

### volatile和syn锁的区别
volatile用来修饰变量，确保变量的更新操作所有线程都可以看到，即当这个变量更新时，其他线程看大最新的变量
syn用来修饰代码块和方法，确保用一时刻只有一个线程执行该方法。


### volatile关键字加在基本类型上和对象上有什么区别？
- 加在基本类型上操作时是直接操作主内存的数据。
- 加在引用类型上能确保引用类型是最新的，确保引用对象指向的对象地址是最新的，但是并不能保证对象内部状态的线程安全
-例如加在了一个引用对象count类上，这个类里面的int a= 1，两个线程同时执行a++时，并不能保证结果是3，只能保证这个对象是最新的，别的线程去拿的时候拿到的可能是它的2，也可能是3，但只能保证引用的count是最新的，不能保证内部操作。保证内部操作要用到syn锁。

# 锁

## synchronized用过吗？
频率较高在代码的日常场景中，JDK1.6之后进行了锁优化，增加了偏向锁和轻量级锁。

### 锁的对象是什么？
普通方法锁的是该方法
静态方法锁的是这个类的class对象
代码块锁的是大括号里面的内容

## synchronized怎么保证可见性，有序性，以及可重入那？
通过两步操作实现可见性
加锁时必须从主内存读取最新数据。
释放时线程必须把修改的数据刷回到内存里，这样其他线程获取锁后，就能看到最新的数据。

通过JVM的monitorenter以及monitorexit实现有序性，锁里面的内容会在这两个之间严格按顺序执行。

可重入定义
==可重入的定义为，会检查锁的持有者是不是一个线程，同一个线程在持有锁的情况下，可以再次获取这把锁而不用等他释放。==
如果不可重入，该线程会一直等待线程内的锁释放就会造成死锁。
底层实现
也是通过JVM的monitor实现的，里面有个owner和count，owner记录线程的ID，如果尝试获取锁的时候会查询ID，ID相同则直接不用再等待释放，count+1即可。

## synchronized锁升级
先理解什么是CAS
CAS是一种非阻塞式并发控制技术，主要用于多个线程同时访问一个共享资源可能带来的竞争条件问题。

轻量级所里的CAS流程
1.线程要获取锁
- 现在线程在栈帧里面创建一个Lock Record（锁记录）
2.CAS尝试加锁
- 线程用CAS比较对象头立现在的值，（期望是无锁状态或指向自己的偏向锁）
- 如果相等说明没人占锁或偏向自己，把对象头替换为指向自己的Lock Record指针（表示锁被自己持有）
- 如果不相等说明有人占用
	-如果是别的线程的偏向锁-先撤销偏向锁，再竞争轻量级锁（原先的单独持有的偏向锁等用完再竞争）
	-如果CAS几次之后还是失败，升级为重量锁，竞争线程会阻塞等待。

可以类比街道店铺旗子特点

四种锁状态
无锁：mark woword存储对象的信息
偏向锁：存储一个线程的ID，后续统一线程获取锁时，直接使用。可重入概念
轻量级锁：当多个线程在不用时间获取同一把锁，即不存在锁竞争的情况下，JVM会采用轻量级锁避免线程阻塞。
未持有锁的将通过CAS自旋等待锁释放。
重量级锁：如果自选超过一定次数或者一个线程持有锁，一个自旋，又有第三个线程进入 synchronized 加锁的代码时，轻量级锁就会升级为重量级锁。
此时，对象头的锁类型会更新为“10”，Mark Word 会存储指向 Monitor 对象的指针，其他等待锁的线程都会进入阻塞状态。

## synchronized和ReentrantLock的区别
前者是自动加锁，后者需要手动lock和unlock

ReentrantLock 可以实现多路选择通知，绑定多个 [Condition](https://javabetter.cn/thread/condition.html)，而 synchronized 只能通过 wait 和 notify 唤醒，属于单路通知；
synchronized 可以在方法和代码块上加锁，ReentrantLock 只能在代码块上加锁，但可以指定是公平锁还是非公平锁
ReentrantLock 提供了一种能够中断等待锁的线程机制，通过 `lock.lockInterruptibly()` 来实现。

==并发量大的情况下建议使用后者==

- ReentrantLock 提供了超时和公平锁等特性，可以应对更复杂的并发场景。
- ReentrantLock 允许更细粒度的锁控制，能有效减少锁竞争。
- ReentrantLock 支持条件变量 Condition，可以实现比 synchronized 更友好的线程间通信机制。condition可以理解为多个等待队列。

### Lock是什么
Lock 是 JUC 中的一个接口，最常用的实现类包括可重入锁 ReentrantLock、读写锁 ReentrantReadWriteLock 等。
### ReentrantLock里面的Lock方法
lock 方法的具体实现由 ReentrantLock 内部的 Sync 类来实现，涉及到线程的自旋、阻塞队列、CAS、AQS 等。

lock 方法会首先尝试通过 CAS 来获取锁。如果当前锁没有被持有，会将锁状态设置为 1，表示锁已被占用。否则，会将当前线程加入到 AQS 的等待队列中


## AQS是什么？
AQS 是一个抽象类，它维护了一个共享变量 state 和一个线程等待队列，为 ReentrantLock 等类提供底层支持。
AQS 的思想是，如果被请求的共享资源处于空闲状态，则当前线程成功获取锁；否则，将当前线程加入到等待队列中，当其他线程释放锁时，从等待队列中挑选一个线程，把锁分配给它。

## ReentrantLock的实现原理
是基于AQS实现的可重入排他锁，使用CAS尝试获取锁，失败的话进入CLH阻塞队列，支持公平锁，非公平锁，可以中断，超时等待。

内部有一个计数器state跟踪锁的状态和持有次数，当线程调用lock方法是，会检查state的值，如果为0，通过CAS修改为1，表示成功加锁，否则根据当前线程的公平性策略，加入等待队列中。

线程首次获取锁时，state 值设为 1；如果同一个线程再次获取锁时，state 加 1；每释放一次锁，state 减 1。

当线程调用 `unlock()` 方法时，ReentrantLock 会将持有锁的 state 减 1，如果 `state = 0`，则释放锁，并唤醒等待队列中的线程来竞争锁。

`new ReentrantLock()` 默认创建的是非公平锁 NonfairSync。在非公平锁模式下，锁可能会授予刚刚请求它的线程，而不考虑等待时间。当切换到公平锁模式下，锁会授予等待时间最长的线程。

### 公平锁和非公平
Reen创建公平锁，直接true就可以，否则默认就是非公平。
区别：
公平锁意味着在多个线程竞争锁时，获取锁的顺序与线程请求锁的顺序相同，即先来先服务。

非公平锁不保证线程获取锁的顺序，当锁被释放时，任何请求锁的线程都有机会获取锁，而不是按照请求的顺序。
### 公平锁实现逻辑
公平锁的核心逻辑在 AQS 的 `hasQueuedPredecessors()` 方法中，该方法用于判断当前线程前面是否有等待的线程。
如果队列前面有等待线程，当前线程就不能抢占锁，必须按照队列顺序排队。如果队列前面没有线程，或者当前线程是队列头部的线程，就可以获取锁。


## CAS是什么？
CAS是一种乐观锁，用于比较一个变量的值是否等于预期值，如果相等则更新值，否则重试。

在CAS中有三个值，E,V,N E是预期值，如果E=V那么将V的值设置为N，否则继续重试，也就是线程里面的锁，
对象头里面指向新的lock record 线程栈，如果对象头里面的指向是无状态或本身，则把对象头的指向当前线程帧，否则继续重试。

### 怎么保证原子性？
CPU会发出一个lock指令，阻止其他处理器对内存地址进行操作，直到当前指令完成。

### CAS有什么问题？
三个经典问题：ABA问题，自旋开销大，只能操作一个变量。

ABA问题
- 一个值原来是A后来改成B后来又改成A，这时CAS会认为这个值没有发生变化
		可以是用版本号，或者时间戳的方式解决该问题。
自旋开销大
- CAS 失败时会不断自旋重试，如果一直不成功，会给 CPU 带来非常大的执行开销。可以加一个自旋次数的限制，超过一定次数，就切换到 synchronized 挂起线程。
多个变量问题
- 将多个变量封装成一个对象使用 AtomicReference 进行 CAS 更新。

## JAVA保证原子性的办法？
使用原子类，Atomic开头的类，加入JUC锁ReentrantLocak，加入Syn锁。



## 原子类
原子类是基于CAS和Volatile类实现的。
像 AtomicIntegerArray 这种以 Array 结尾的，还可以原子更新数组里的元素。

```
class AtomicArrayExample {
    public static void main(String[] args) {
        AtomicIntegerArray atomicArray = new AtomicIntegerArray(new int[]{1, 2, 3});

        atomicArray.incrementAndGet(1); // 对索引 1 进行自增
        System.out.println(atomicArray.get(1)); // 输出 3
    }
}
```

像 AtomicStampedReference 还可以通过版本号的方式解决 CAS 中的 ABA 问题。

```
class AtomicStampedReferenceExample {
    public static void main(String[] args) {
        AtomicStampedReference<Integer> ref = new AtomicStampedReference<>(100, 1);

        int stamp = ref.getStamp(); // 获取版本号
        ref.compareAndSet(100, 200, stamp, stamp + 1); // A → B
        ref.compareAndSet(200, 100, ref.getStamp(), ref.getStamp() + 1); // B → A
    }
}
```

## 线程死锁
死锁发生在线程相互等待对方释放锁。比如说线程1持有线程锁R，等待R2，线程2持有锁R2，等待R1.

> 释放锁的条件是“锁保护的临界区任务完成”，而不是“获取到另一个锁”。  
> 死锁是因为线程都没到释放锁的地方，就互相等对方的锁，导致永远卡住。

死锁发生的四个条件
- 互斥：资源不能被多个线程共享，一次只能有一个线程使用。如果一个线程已经使用了，其他的线程必须等待，直到资源被释放。
- 请求并持有：一个线程持有一个资源，并且在等待获取其他线程的持有资源。
- 不可剥夺：资源不能强制从线程中夺走，必须等待主动释放
- 循环等待：存在一种线程等待链，线程 A 等待线程 B 持有的资源，线程 B 等待线程 C 持有的资源，直到线程 N 又等待线程 A 持有的资源

### 如何避免死锁
- 所有的线程都按照固定的顺序去申请资源，例如，先申请R1再申请R2。
- 如果线程发现无法获取某个资源，可以先释放已经持有的资源，重新尝试申请。

## 如何查看死锁？
首先从系统级别上排查，比如说在 Linux 生产环境中，可以先使用 `top` `ps` 等命令查看进程状态，看看是否有进程占用了过多的资源。

接着使用jdk自带的工具排查比如jps -l查看当前进程，然后使用 `jstack 进程号` 查看当前进程的线程堆栈信息，看看是否有线程在等待锁资源

举例：
```
package xc;  
  
public class SiSuo {  
    public static Object lock = new Object();  
    public static Object lock1 = new Object();  
  
    public static void main(String[] args) {  
  
        new Thread(()->{  
            synchronized (lock) {  
                System.out.println("线程1获取锁1");  
                try {  
                    Thread.sleep(10000);  
                }catch (InterruptedException e){  
                    e.printStackTrace();  
                }  
                synchronized (lock1){  
                    System.out.println("线程1获取锁2");  
                }  
            }  
        }).start();  
        new Thread(()->{  
            synchronized (lock1) {  
                System.out.println("线程1获取锁1");  
                try {  
                    Thread.sleep(10000);  
                }catch (InterruptedException e){  
                    e.printStackTrace();  
                }  
                synchronized (lock){  
                    System.out.println("线程1获取锁2");  
                }  
            }  
        }).start();  
  
  
  
    }  
}
```
该代码是个最简单的死锁。

在终端里找到pid
jps -l

记下对应的死锁pid
用 `jstack` 打线程 dump 并保存
jstack -l 12345 > threaddump.txt
grep -A 20 "Found one Java-level deadlock" threaddump.txt
可以用这个直接找死锁的提示。
会查看到类似死锁的例子
Found one Java-level deadlock:
" T1":
  waiting to lock monitor 0x00000000f... (object 0x12345678, a java.lang.Object),
  which is held by "T2"
"T2":
  waiting to lock monitor 0x00000000f... (object 0x87654321, a java.lang.Object),
  which is held by "T1"

## 悲观锁和乐观锁
悲观锁认为每次访问共享资源时都会发生冲突，所在在操作前一定要先加锁，防止其他线程修改数据。

乐观锁认为冲突不会总是发生，所以在操作前不加锁，而是在更新数据时检查是否有其他线程修改了数据。如果发现数据被修改了，就会重试。

乐观锁如果发现有线程过来修改数据，怎么办？
可以重新读取数据，然后尝试更新，直到成功或达到最大次数为止。

## 说一下ConcurrentHashMap
它是hashmap的线程安全版本

JDK 8 中的 ConcurrentHashMap 取消了分段锁，采用 CAS + synchronized 来实现更细粒度的桶锁，并且使用红黑树来优化链表以提高哈希冲突时的查询效率，性能比 JDK 7 有了很大的提升。

put方法
第一步，计算 key 的 hash，以确定桶在数组中的位置。如果数组为空，采用 CAS 的方式初始化，以确保只有一个线程在初始化数组。
第二步，如果桶为空，直接 CAS 插入节点。如果 CAS 操作失败，会退化为 synchronized 代码块来插入节点。

插入的过程中会判断桶的哈希是否小于 0（`f.hash >= 0`），小于 0 说明是红黑树，大于等于 0 说明是链表。

第三步，如果链表长度超过 8，转换为红黑树。
第四步，在插入新节点后，会调用 `addCount()` 方法检查是否需要扩容。


# 线程池
## 什么是线程池
线程池是用来管理和复用的工具，它可以减少线程的创建和开销。

在 Java 中，ThreadPoolExecutor 是线程池的核心实现，它通过核心线程数、最大线程数、任务队列和拒绝策略来控制线程的创建和执行。

举个例子：就像你开了一家餐厅，线程池就相当于固定数量的服务员，顾客（任务）来了就安排空闲的服务员（线程）处理，避免了频繁招人和解雇的成本。

## 线程池的主要参数
线程池有 7 个参数，需要重点关注的有核心线程数、最大线程数、等待队列、拒绝策略。

**①、corePoolSize**：核心线程数，长期存活，执行任务的主力。

**②、maximumPoolSize**：线程池允许的最大线程数。

**③、workQueue**：任务队列，存储等待执行的任务。

**④、handler**：拒绝策略，任务超载时的处理方式。也就是线程数达到 maximumPoolSiz，任务队列也满了的时候，就会触发拒绝策略。

**⑤、threadFactory**：线程工厂，用于创建线程，可自定义线程名。

**⑥、keepAliveTime**：非核心线程的存活时间，空闲时间超过该值就销毁。

**⑦、unit**：keepAliveTime 参数的时间单位：

参数之间的关系，也是创建一个线程的方式。
一句话：任务优先使用核心线程执行，满了进入等待队列，队列满了启用非核心线程备用，线程池达到最大线程数量后触发拒绝策略，非核心线程的空闲时间超过存活时间就被回收。

核心线程数不够怎么处理
当提交的任务数超过了 corePoolSize，但是小于 maximumPoolSize 时，线程池会创建新的线程来处理任务。

当提交的任务数超过了 maximumPoolSize 时，线程池会根据拒绝策略来处理任务。


## 线程池的拒绝策略有哪些

有四种：

- AbortPolicy：默认的拒绝策略，会抛 RejectedExecutionException 异常。
- CallerRunsPolicy：让提交任务的线程自己来执行这个任务，也就是调用 execute 方法的线程。
- DiscardOldestPolicy：等待队列会丢弃队列中最老的一个任务，也就是队列中等待最久的任务，然后尝试重新提交被拒绝的任务。
- DiscardPolicy：丢弃被拒绝的任务，不做任何处理也不抛出异常。

## 线程池提交execute和submit有什么区别
execute 方法没有返回值，适用于不关心结果和异常的简单任务
submit 有返回值，适用于需要获取结果或处理异常的场景。

## 线程池如何关闭？
可以调用线程池的`shutdown`或`shutdownNow`方法来关闭线程池。

shutdown 不会立即停止线程池，而是等待所有任务执行完毕后再关闭线程池。
shutdownNow 会尝试通过一系列动作来停止线程池，包括停止接收外部提交的任务、忽略队列里等待的任务、尝试将正在跑的任务 interrupt 中断。

## 常见线程池
主要有四种：

固定大小的线程池 `Executors.newFixedThreadPool(int nThreads);`，适合用于任务数量确定，且对线程数有明确要求的场景。例如，IO 密集型任务、数据库连接池等。

缓存线程池 `Executors.newCachedThreadPool();`，适用于短时间内任务量波动较大的场景。例如，短时间内有大量的文件处理任务或网络请求。

定时任务线程池 `Executors.newScheduledThreadPool(int corePoolSize);`，适用于需要定时执行任务的场景。例如，定时发送邮件、定时备份数据等。

单线程线程池 `Executors.newSingleThreadExecutor();`，适用于需要按顺序执行任务的场景。例如，日志记录、文件处理等。

## 线程池异常怎么处理？
常见的处理方式有，使用 try-catch 捕获、使用 Future 获取异常、自定义ThreadPoolExecutor 重写 afterExecute 方法、使用 UncaughtExceptionHandler 捕获异常。

## 线程池的状态
有 5 种状态，它们的转换遵循严格的状态流转规则，不同状态控制着线程池的任务调度和关闭行为。

状态由 RUNNING → SHUTDOWN → STOP → TIDYING → TERMINATED 依次流转。

**RUNNING** 状态的线程池可以接收新任务，并处理阻塞队列中的任务；**SHUTDOWN** 状态的线程池不会接收新任务，但会处理阻塞队列中的任务；**STOP** 状态的线程池不会接收新任务，也不会处理阻塞队列中的任务，并且会尝试中断正在执行的任务；**TIDYING** 状态表示所有任务已经终止；**TERMINATED** 状态表示线程池完全关闭，所有线程销毁

## 线程池调优
首先我会根据任务类型设置核心线程数参数，比如 IO 密集型任务会设置为 CPU 核心数*2 的经验值。

其次我会结合线程池动态调整的能力，在流量波动时通过 setCorePoolSize 平滑扩容，或者直接使用 DynamicTp 实现线程池参数的自动化调整。

最后，我会通过内置的监控指标建立容量预警机制。比如通过 JMX 监控线程池的运行状态，设置阈值，当线程池的任务队列长度超过阈值时，触发告警。

### IO和CPU密集型任务
CPU 密集型 → 算力是瓶颈，线程数等于 CPU 核数  
I/O 密集型 → I/O 是瓶颈，线程数可以多，让 CPU 不闲着

cpu密集型任务
- 程序的主要耗时都在 **CPU 计算** 上，比如数学运算、加解密、视频编码等。
    
- CPU 一直满负荷运转，I/O 操作很少或者几乎没有。
    
- 性能瓶颈在 CPU 的计算能力。
- 大规模数据排序（内存操作）
    
- 图像渲染
    
- 压缩、解压缩
    
- 大量数学计算（科学计算、机器学习推理）

IO密集型任务
- 程序的大部分时间花在 **等待 I/O 完成**（磁盘、网络、数据库等），CPU 在等待时会空闲。
    
- 性能瓶颈在 I/O 操作速度，而不是 CPU 计算能力。
- 网络爬虫（等待网络响应）
    
- 数据库读写操作
    
- 文件读写、日志写入
    
- Web 服务器处理请求


## 线程池任务执行断电处理？
线程池本身只能在内存中进行任务调度，并不会持久化，一旦断电，线程池里的所有任务和状态都会丢失。

我会考虑以下几个方面：

第一，持久化任务。可以将任务持久化到数据库或者消息队列中，等电恢复后再重新执行。

第二，任务幂等性，需要保证任务是幂等的，也就是无论执行多少次，结果都一致。

第三，恢复策略。当系统重启时，应该有一个恢复流程：检测上次是否有未完成的任务，将这些任务重新加载到线程池中执行，确保断电前的工作能够恢复。


