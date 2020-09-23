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
    * Callable接口与Runnable接口区别？  
        1. Callable有返回值，Runnable没有  
        2. Callable抛异常，Runnable没有  
        3. 落地方法不一样，Callable是call()，Runnable是run()
    * get方法一般请放在最后一行  
  
## Lambda表达式  
1. 为什么使用Lambda表达式？  
    避免匿名内部类定义过多；简化代码；去掉无意义代码，只留核心的逻辑。  
2. 理解函数式接口(Functional Interface)  
    * 任何接口，如果只包含唯一一个抽象方法，那么它就是一个函数式接口。允许存在default  
    ```java
        @FunctionalInterface  
        public interface Runnable{
            public abstract void run();
        }
    ```  
    * 对于函数式接口，可以通过lambda表达式来创建该接口的对象。  

3. Lambda格式  
    * (....) -> { 具体实现 };  
    小括号直接copy  
* Demo1  
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
* Demo2  
    ```java
    @FunctionalInterface
    interface Foo {
        //    public void sayHello();
        public int add(int a, int b);

        default int div(int x, int y) {
            System.out.println("****div*****");
            return x / y;
        }

        public static int mul(int x, int y) {
            return x * y;
        }

    }


    public class LambdaExpressDemo {
        public static void main(String[] args) {
    /*        Foo foo = new Foo() {
                @Override
                public void sayHello() {
                    System.out.println("******hello******");
                }
            };
            foo.sayHello();*/
    //        Foo foo = () -> {
    //            System.out.println("****hello****");
    //        };
    //        foo.sayHello();
            Foo foo = (int a, int b) -> {
                return a + b;
            };
            System.out.println("a + b = " + foo.add(3, 6));
            System.out.println(foo.div(10, 5));
            System.out.println(Foo.mul(4, 9));  // Foo.mul 类方法
        }
    }
    /*
        a + b = 9
        ****div*****
        2
        36
    */
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

{{< admonition type=msg title="sleep与wait区别" open=true >}}
都是当前线程暂停，wait会让线程释放手中的锁，sleep不会释放锁
{{< /admonition >}}

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
  
## 生产者消费者案例  
题目：两个线程，操作一个初始值为0的变量  
    实现一个线程对该变量加1，一个线程对该变量减1  
    实现交替，来10轮，变量初始值为0  

1. 高聚合低耦合，线程操作资源类
2. 判断/干活/通知
3. 多线程交互中，必须要防止多线程的虚假唤醒，即判断用while，别用if  
> Demo注释部分为synchronized版本，新的为使用lock的版本
```java
package com.demo.thread;


import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Temperature {
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void increment() throws InterruptedException{
        lock.lock();
        try{
            // 1. 判断
            while (number != 0) {
                condition.await(); //this.wait();
            }
            // 2. 干活
            number++;
            System.out.println(Thread.currentThread().getName() + "\t" + number);
            // 3. 通知
            condition.signalAll(); //this.notifyAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void decrement() throws InterruptedException{
        lock.lock();
        try{
            // 1. 判断
            while (number == 0) {
                condition.await(); //this.wait();
            }
            // 2. 干活
            number--;
            System.out.println(Thread.currentThread().getName() + "\t" + number);
            // 3. 通知
            condition.signalAll(); //this.notifyAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

/*    public synchronized void increment() throws InterruptedException {
        // 1. 判断
        while (number != 0) {
            this.wait();
        }
        // 2. 干活
        number++;
        System.out.println(Thread.currentThread().getName() + "\t" + number);
        // 3. 通知
        this.notifyAll();

    }*/

    /*public synchronized void decrement() throws InterruptedException {
        while (number == 0) {
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName() + "\t" + number);
        this.notifyAll();
    }*/

}

/**
 * 两个线程，操作一个初始值为0的变量
 * 实现一个线程对该变量加1，一个线程对该变量减1
 * 实现交替，来10轮，变量初始值为0
 * <p>
 * 1. 高聚合低耦合，线程操作资源类
 * 2. 判断/干活/通知
 * 3. 多线程交互中，必须要防止多线程的虚假唤醒，即判断用while，别用if
 */

public class ThreadWaitNotifyDemo {
    public static void main(String[] args) {
        Temperature temperature = new Temperature();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(200);
                    temperature.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(200);
                    temperature.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(100);
                    temperature.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(300);
                    temperature.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"D").start();
    }
}

```  

## 精准通知和唤醒，顺序访问Demo  
题目：多线程之间按顺序调用，实现 A -> B -> C  
    三个线程启动，要求如下：  
    AA打印5次，BB打印10次，CC打印15次  
    接着  
    AA打印5次，BB打印10次，CC打印15次  
    ......来10轮  
> **注意标志位的修改和定位**  
> 该案例体现出了Lock和Condition的优点，synchronized无法做到
```java
package com.demo.thread;


import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class ShareResource {
    private int number = 1; // 1A  2:B  3:C
    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();

    public void print5() {
        lock.lock();
        try {
            //1.判断
            while (number != 1) {
                condition1.await();
            }
            //2.干活
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + "\t" + i);
            }
            //3.通知
            number = 2;
            condition2.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void print10() {
        lock.lock();
        try {
            //1.判断
            while (number != 2) {
                condition2.await();
            }
            //2.干活
            for (int i = 1; i <= 10; i++) {
                System.out.println(Thread.currentThread().getName() + "\t" + i);
            }
            //3.通知
            number = 3;
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void print15() {
        lock.lock();
        try {
            //1.判断
            while (number != 3) {
                condition3.await();
            }
            //2.干活
            for (int i = 1; i <= 15; i++) {
                System.out.println(Thread.currentThread().getName() + "\t" + i);
            }
            //3.通知
            number = 1;
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}


/**
 * 多线程之间按顺序调用，实现 A -> B -> C
 * 三个线程启动，要求如下：
 * AA打印5次，BB打印10次，CC打印15次
 * 接着
 * AA打印5次，BB打印10次，CC打印15次
 * ......来10轮
 * <p>
 * 1. 高聚合低耦合，线程操作资源类
 * 2. 判断/干活/通知
 * 3. 多线程交互中，必须要防止多线程的虚假唤醒，即判断用while，别用if
 * 4. 注意标志位的修改和定位
 */
public class ThreadOrderAccess {
    public static void main(String[] args) {
        ShareResource shareResource = new ShareResource();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                shareResource.print5();
            }
        },"AA").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                shareResource.print10();
            }
        },"BB").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                shareResource.print15();
            }
        },"CC").start();
    }
}
```  

## 线程8锁  
1. 标准访问，先打印邮件还是短信？？   
2. 邮件方法暂停4秒，先打印邮件还是短信？？  
> * 一个对象里面如果有多个synchronized方法，某一时刻内，只要一个线程去调用其中的一个synchronized方法了，其他的线程都只能等待，换句话说，某一时刻内，只能有唯一一个线程去访问这些synchronized方法  
> * 锁的是当前对象this，被锁定后，其他线程都不能进入到当前对象的其他的synchronized方法  
3. 新增一个普通方法hello()，先打印邮件，还是hello  
4. 两部手机，先打印邮件还是短信？？  
> 加个普通方法后和同步锁无关  
> 换成两个对象后，不是同一把锁了
5. 两个静态同步方法，同一部手机，请问先打印邮件还是短信？  
6. 两个静态同步方法，两部手机，请问先打印邮件还是短信？  
> synchronized实现同步的基础：Java中的每一个对象都可以作为锁  
> 对于普通同步方法，锁的是当前实例对象  
> 对于同步方法块，锁的是synchronized括号里配置的对象  
> 对于静态同步方法，锁的是当前类的Class对象  
7. 1个静态同步方法，一个普通同步方法，一部手机，请问先打印邮件还是短信？  
8. 1个静态同步方法，一个普通同步方法，两部手机，请问先打印邮件还是短信？  
> * 当一个线程试图访问同步代码块是，它首先必须得到锁，退出或抛出异常时必须释放锁。  
> * 也就是说如果一个实例对象的非静态同步方法获取锁后，该实例对象的其他非静态方法必须等待获取锁的方法释放锁后，才能获取锁。  
> * 别的实例对象的非静态同步方法因为跟该实例对象的非静态同步方法用的是不同的锁，所以不用等待该实例对象释放锁，就可以获取他们自己的锁。  
> * 所有的静态同步方法用的也是同一把锁，即类对象本身  
> * **这两把锁是两个不同的对象**，所以静态同步方法与非静态同步方法之间不会有竞争条件的  
> * 一旦一个静态同步方法获取锁后，其他的静态同步方法都必须等待该方法释放锁，才能获得锁。不管是同一个实例对象的静态同步方法之间，还是不同的实例对象的静态同步方法之间，只要他们是同一个类的实例对象。  

## list不安全  
1. 故障现象  
    `java.util.ConcurrentModificationException`(并发修改异常)  
2. 导致原因  
    ArrayList线程不安全,add方法没有加synchronized
3. 解决方案  
    3.1 使用Vector，Vector线程安全，add方法有synchronized
    3.2 `Collections.synchronizedList(new ArrayList<>())`  
    3.3 `new CopyOnWriteArrayList<>();`
4. 优化建议（同样的错误，不出现第二次）  
  
* 写时复制：CopyOnWrite容器即写时复制的容器。往一个容器添加元素的时候，不直接往当前容器object[\]添加，而是现将当前容器object[\]进行Copy，复制出一个新的容器object[\] newElements,然后新的容器object[\] newElements里添加元素，添加完之后，再将2元容器的引用指向新的容器setArray(newElements);。这样做的好处是可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素，所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。
```java
    // CopyOnWriteArrayList，Add方法源码
    public boolean add(E var1) {
        ReentrantLock lock = this.lock;
        lock.lock();

        boolean var6;
        try {
            Object[] elements = this.getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = var1;
            this.setArray(var5);
            var6 = true;
        } finally {
            lock.unlock();
        }

        return var6;
    }
```  

## CountDownLatch  
* 主要有两个方法，当一个或多个线程调用await方法时，这些线程会阻塞  
* 其他线程调用countDown方法会将计数器减1(调用countDown方法的线程不会阻塞)  
* 当计数器的值变为0时，因await方法阻塞的线程会被唤醒，继续执行  

```java
package com.demo.thread;

import java.util.concurrent.CountDownLatch;

/**
 * @author: lqy
 * @create: 2020/9/23
 * @description: CountDownLatchDemo
 */
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 1; i <= 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t离开教室");
                countDownLatch.countDown();
            }, String.valueOf(i)).start();
        }
        countDownLatch.await();
        System.out.println(Thread.currentThread().getName() + "\t班长关门走人");
    }

    public static void closeDoor() {
        for (int i = 1; i <= 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t离开教室");
            }, String.valueOf(i)).start();
        }
        System.out.println(Thread.currentThread().getName() + "\t班长关门走人");
    }
}

```  

## CyclicBarrier  
* 与CountDownLatch相反，做加法
```java
package com.demo.thread;

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

/**
 * @author: lqy
 * @create: 2020/9/23
 * @description: CyclicBarrierDemo
  */
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
            System.out.println("***召唤神龙*****");
        });

        for (int i = 1; i <= 7; i++) {
            final int tempInt = i;
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"\t收集到第"+tempInt+"颗龙珠");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }


    }
}
```  

## Semaphore  
* 信号量两种操作：  
    * acquire：当一个线程调用acquire操作时，它要么通过成功获取信号量（信号量减1），要么一直等下去，直到有线程释放信号量，或超时  
    * release：实际上会将信号量加1，然后唤醒等待的线程  
* 信号量用于多个共享资源的互斥使用；用于并发线程数的控制  
  

