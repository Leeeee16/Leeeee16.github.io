# Java多线程

 
## 线程的三种创建方式：  
1. 继承Thread类  
    * 自定义线程类继承Thread类  
    * 重写run()方法，编写线程执行体  
    * 创建线程对象，调用start()方法启动线程  
    > 线程开启不一定立即执行，由CPU调度  
    > 启动线程：子类对象.start()
2. 实现Runnable接口  
    * 定义MyRunnable类实现Runnable接口  
    * 实现run()方法，编写线程执行体  
    * 创建线程对象，调用start()方法启动线程  
    > 启动线程：传入目标对象+Thread对象.start()  
    > 该方式更加灵活方便，方便同一个对象被多个线程使用  
3. 实现Callable接口  
    * 实现Callable接口，需要返回值类型  
    * 重写call方法，需要抛出异常  
    * 创建目标对象  
    * 创建执行服务：`ExecutorService ser = Executors.newFixedThreadPool(1);`  
    * 提交执行：`Future<Boolean> result1 = ser.submit(t1);`  
    * 获取结果：`boolean r1 = result1.get()`  
    * 关闭服务：`ser.shutdownNow();`  
    > 可以定义返回值  
    > 可以抛出异常
  
## Lambda表达式  
1. 为什么使用Lambda表达式？  
    避免匿名内部类定义过多；简化代码；去掉无意义代码，只留核心的逻辑。  
2. 理解函数式接口(Functional Interface)  
    * 任何接口，如果只包含唯一一个抽象方法，那么它就是一个函数式接口。  
    ```java
        public interface Runnable{
            public abstract void run();
        }
    ```  
    * 对于函数式接口，可以通过lambda表达式来创建该接口的对象。  
    ```java
    package com.demo.lambda;

    /*
    推导lambda表达式
    */
    public class TestLambda1 {

        //3. 静态内部类
        static class Like2 implements ILike {
            @Override
            public void lambda() {
                System.out.println("i like lambda2");
            }
        }

        public static void main(String[] args) {
            ILike like = new Like();
            like.lambda();

            like = new Like2();
            like.lambda();

            //4. 局部内部类
            class Like3 implements ILike {
                @Override
                public void lambda() {
                    System.out.println("i like lambda3");
                }
            }
            like = new Like3();
            like.lambda();

            //5. 匿名内部类,没有类的名称，必须借助接口或者父类
            like = new ILike() {
                @Override
                public void lambda() {
                    System.out.println("i like lambda4");
                }
            };
            like.lambda();

            //6. 用lambda简化
            like = () -> {
                System.out.println("i like lambda5");
            };
            like.lambda();
        }
    }

    //1. 定义一个函数式接口
    interface ILike {
        void lambda();
    }

    //2. 实现类
    class Like implements ILike {
        @Override
        public void lambda() {
            System.out.println("i like lambda");
        }
    }
    ```  

## 静态代理模式  
* 静态代理模式总结：
    * 真实对象和代理对象都要事先同一个接口(Marry)
    * 代理对象要代理真实角色
* 好处：
    * 代理对象可以做很多真实对象做不了的事情
    * 真实对象专注做自己的事情  
```java

/*
静态代理模式总结：
    真实对象和代理对象都要事先同一个接口(Marry)
    代理对象要代理真实角色
好处：
    代理对象可以做很多真实对象做不了的事情
    真实对象专注做自己的事情
 */
public class StaticProxy {
    public static void main(String[] args) {
        You you = new You();
        new Thread( () -> System.out.println("i love u") ).start();

        new WeddingCompany(new You()).HappyMarry();
    }
}

interface Marry{
    void HappyMarry();
}

// 真是角色
class You implements Marry{
    @Override
    public void HappyMarry() {
        System.out.println("小明要结婚了");
    }
}

// 代理角色，帮你结婚
class WeddingCompany implements Marry{

    // 代理谁 --》 真实目标角色
    private Marry target;

    public WeddingCompany(Marry target) {
        this.target = target;
    }

    @Override
    public void HappyMarry() {
        before();
        this.target.HappyMarry();//这是真实对象，
        after();
    }

    private void after() {
        System.out.println("结婚后，收钱");
    }

    private void before() {
        System.out.println("结婚前，布置会场");
    }
}
```  
## 线程休眠  
* sleep指定当前线程阻塞的毫秒数  
* sleep存在异常InterruptedException  
* sleep时间达到后线程进入就绪状态  
* 每一个对象都有一个锁，sleep不会释放锁  

## 线程礼让  
* 让当前正在执行的线程暂停，但不阻塞  
* 将线程从运行状态转为就绪状态  
* 让CPU重新调度，礼让不一定成功  
```java
//测试礼让线程
//礼让不一定成功
public class TestYield {
    public static void main(String[] args) {
        MyYield myYield = new MyYield();

        new Thread(myYield, "a").start();
        new Thread(myYield, "b").start();
    }

}

class MyYield implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "线程开始执行");
        Thread.yield(); // 礼让
        System.out.println(Thread.currentThread().getName() + "线程停止执行");
    }
}
//b线程开始执行
//a线程开始执行
//b线程停止执行
//a线程停止执行
```  

## Join  
* 待此线程执行完成后，再执行其他线程，其他线程阻塞  
* 感觉join不能完全理解为“插队”，两个线程可以交替的运行，一旦某个线程调用了join方法，那么这个线程会一直运行到结束，其他线程才会继续运行。
```java
// 测试Join方法，相当于插队
public class TestJoin implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("线程vip来了" + i);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        //启动我们的线程
        TestJoin testJoin = new TestJoin();
        Thread thread = new Thread(testJoin);
        thread.start();

        //主线程

        for (int i = 0; i < 500; i++) {
            if (i == 200) {
                thread.join();//插队
            }
            System.out.println("main" + i);
        }
    }
}
```  



## 线程优先级  
* Java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度哪个线程来执行  
* 范围1~10；  
    `Thread.MIN_PRIORITY = 1`
    `Thread.MAX_PRIORITY = 10`
    `Thread.NORM_PRIORITY = 1`  
* 获得优先级/改变优先级  
    `getPiority()`  
    `setPriority(int xxx)`  
> 1. 优先级低知识意味着获得调度的概率低，并不是优先级低就不会被调用。还是需要看CPU怎么调度。  
> 2. 优先级的设定建议在start()调度前  

## 守护(daemon)线程  
* 线程有用户线程和守护线程  
* 虚拟机必须确保用户线程执行完毕  
* 虚拟机不用等待守护线程执行完毕  
* **作用**：后台记录操作日志，监控内存，垃圾回收等...  
```java
public class TestDaemon {
    public static void main(String[] args) {
        God god = new God();
        Human human = new Human();

        Thread thread = new Thread(god);
        thread.setDaemon(true); //默认是false表示是用户线程，正常的线程都是用户线程
        thread.start(); // 守护线程启动

        new Thread(human).start();// human用户线程启动
    }
}

// god
class God implements Runnable{

    @Override
    public void run() {
        while(true){
            System.out.println("上帝保佑你");
        }
    }
}

// human
class Human implements Runnable{
    @Override
    public void run() {
        for (int i = 1; i <= 36500; i++) {
            System.out.println("开心第"+i+"天~");
        }
        System.out.println("****************886**********");
    }
}
```  


## 线程同步  
* 由于同一进程的多个线程共享同一块存储空间，为了保证数据在方法中被访问的正确性，在访问时加入**锁机制(synchronized)**，当一个线程获得对象的排它锁，独占资源，其他线程必须等待，使用后释放锁即可。存在以下问题：  
    * 一个线程持有锁会导致其他所有需要此锁的线程挂起  
    * 在多线程竞争下，加锁，释放锁会导致比较多的上下文切换 和 调度延时，引起性能问题  
    * 如果一个高优先级的线程等待一个优先级低的线程释放锁，会导致优先级倒置，引起性能问题  

* **同步方法**:`public synchronized void method(int args){}`  
    synchronized方法控制对“对象”的访问，每个对象对应一把锁，每个synchronized方法都必须获得调用该方法的对象的锁才能执行，否则线程会阻塞，方法一旦执行，就独占该锁，直到该方法放回才释放锁，后面被阻塞的线程才能获得这个锁，继续执行  
    **缺陷**：若将一个大方法的方法申明为synchronized将会影响效率
    * 方法里面需要修改的内容才需要锁，锁的太多，浪费资源  
* 同步块:`synchronized(Obj){}`  
    * Obj称之为**同步监视器**  
        * Obj可以是任何对象，但是推荐使用共享资源作为同步监视器  
        * 同步方法中无需指定同步监视器，因为同步方法的同步监视器就是this，就是这个对象本身，或者是class  
    * 同步监视器的执行过程：  
        1. 第一个线程访问，锁定同步监视器，执行其中代码  
        2. 第二个线程访问，发现同步监视器被锁定，无法访问  
        3. 第一个线程访问完毕，解锁同步监视器  
        4. 第二个线程访问，发现同步监视器没有锁，然后锁定并访问  

## 死锁  
* 多个线程各自占有一些共享资源，并且互相等待其他线程占有的资源才能运行，而导致两个或者多个线程都在等待对方释放资源，都停止执行的情况。某一个同步块同时拥有**两个以上对象的锁**，就可能发生死锁问题。  
* 产生死锁的四个必要条件：  
    1. 互斥条件：一个资源每次只能被一个进程使用  
    2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放  
    3. 不剥夺条件:进程已获得的资源，在未使用完之前，不能强行剥夺  
    4. 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系  

## Lock(锁)  
* JDK5.0开始，显式定义同步锁对象，Lock对象  
* kava,util.concurrent.locks.Lock接口。锁提供了对共享资源的独占访问，每次只能有一个线程对Lock对象加锁，线程开始访问共享资源之前应该先获得Lock对象。  
* ReentrantLock类实现了Lock，它拥有与Synchronized相同的并发性和内存语义，在实现线程安全的控制中，比较常用的是ReentrantLock，可以显式加锁、释放锁。  
```java
import java.util.concurrent.locks.ReentrantLock;

//测试Lock锁
public class TestLock {
    public static void main(String[] args) {
        TestLock2 testLock2 = new TestLock2();

        new Thread(testLock2).start();
        new Thread(testLock2).start();
        new Thread(testLock2).start();
    }
}

class TestLock2 implements Runnable {

    int ticketNums = 10;

    //定义Lock锁
    private final ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            try {
                lock.lock();//加锁
                if (ticketNums > 0) {

                    System.out.println(ticketNums--);
                } else {
                    break;
                }
            } finally {
                //解锁
                lock.unlock();
            }

        }
    }
}
```


{{< admonition type=msg title="synchronized 与 Lock对比" open=true >}}
* Lock是显式锁（手动开启和关闭锁），synchronized是隐式锁，出了作用域自动释放  
* Lock只有代码块锁，synchronized有代码块锁和方法锁  
* 使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）  
* 优先使用顺序：Lock > 同步代码块(已经进入了方法体，分配了相应的资源) > 同步方法(在方法体之外)
{{< /admonition >}}  

## 线程协作  
* Java提供了几个方法解决线程之间的通信问题  

| 方法名 | 作用 |
| :----: | :--- |
| wait() | 表示线程一直等待，直到其他线程通知，会释放锁。与sleep不同 |
| wait(long timeout) | 指定等待的毫秒数 |
| notify() | 唤醒一个处于等待状态的线程  |
| notifyAll() | 唤醒同一个对象上所有调用wait()方法的线程，优先级别高的线程优先调度 |  

## 线程池  
* 提前创建好多个线程，放入线程池中，使用时直接获取，使用后放回池中。提高性能  
* **好处**：便于线程管理  
* ExecutorService：真正的线程池接口。常见子类ThreadPoolExecutor  
    * void execute(Runnable command):执行任务/命令，没有返回值，一般用来执行Runnable  
    * <T> Future<T> submit(Callable<T> task)：执行任务，没有返回值，一般用来执行Callable  
    * void shutdown():关闭连接池  
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class TestPool {
    public static void main(String[] args) {
        //1. 创建线程池
        //newFixedThreadPool 参数为：线程池的大小
        ExecutorService service = Executors.newFixedThreadPool(10);

        //执行
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());

        //2. 关闭链接
        service.shutdown();
    }
}

class MyThread implements Runnable {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
//pool-1-thread-1
// pool-1-thread-4
// pool-1-thread-3
// pool-1-thread-2
```


