# JVM学习随笔


## JVM的架构模型  
JAVA编译器输入的指令流基本是一种基于**栈的指令集架构**，另一种架构是基于寄存器的。  
两者的区别：  
- **基于栈式架构的特点**  
    - 设计和实现更简单，适用于资源受限的系统，比如嵌入式。  
    - 避开了寄存器的分配难题：采用零地址指令方式分配。（零地址就是只有操作码，没有地址字段，因为栈的话只对栈顶进行操作，就不需要地址）  
    - 指令流中的指令大部分是零地址指令，执行过程依赖操作栈。**指令集更小**，编译器容易实现。  
    - 不需要硬件支持，可移植性更好，更好实现**跨平台**  
- **基于寄存器架构的特点**  
    - 指令集完全依赖于硬件，可移植性差。  
    - 性能优秀、执行高效  
    - 花费更少的指令来完成一项操作  
    - 在大部分情况下，采用一地址指令、二地址指令和在地址指令为主。  
  
## JVM的生命周期  
- 虚拟机的启动：Java虚拟机的启动时通过引导类加载器(bootstrap class loader)创建一个初始类(initial calss)来完成的，这个类是由虚拟机的具体实现指定的。  
- 虚拟机的执行：  
    - 任务：执行Java程序  
    - 程序开始执行时它才运行，程序结束它就停止  
    - 执行一个所谓的Java程序的时候，真真正正在执行的是一个叫做Java虚拟机的进程  
- 虚拟机的退出：  
    - 程序正常执行结束  
    - 程序碰到异常或错误而异常终止  
    - 由于操作系统出错而导致Java虚拟机进程终止  
    - 某线程调用Runtime类或者System类的exit方法，或Runtime类的halt方法，并且Java安全管理器也允许这次exit或halt操作  
    - 除此之外，JNI(Java Native Interface)规范描述了用JNI Invocation API来加载或卸载Java虚拟机时，Java虚拟机的退出情况  
  
## JVM整体结构  
![JVM整体结构](/images/JVM_Note/JVM_jiegou.jpg "JVM整体结构")  

## 类加载器子系统  
![类加载器子系统](/images/JVM_Note/JVM_leijiazaiqi.jpg "类加载器子系统")
* 类加载器子系统负责从文件系统或者网络中加载Class文件，class文件在文件开头有特定的文件标识。  
* ClassLoader只负责class文件的加载，至于它是否可以运行，则由Execution Engine决定。  
* 加载的类信息存放在一块称为方法区的内存空间。除了类的信息外，方法区中还会存放运行时常量池信息，可能还包括字符串字面量和数字常量（这部分信息是Class文件中常量池部分的内存映射）
  
### 类的加载阶段  
1. 加载：  
    1. 通过一个类的全限定名定义此类的二进制字节流  
    2. 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构  
    3. 在内存中生成一个代表这个类的**java.lang.Class对象**，作为方法区这个类的各种数据的访问入口。  
2. 链接：  
    1. 验证：  
        * 目的在于确保class文件的字节流中包含信息符合当前虚拟机要求，保证被加载类的正确性  
        * 主要包括四种验证：文件格式验证、元数据验证、字节码验证、符号引用验证  
    2. 准备：
        * 为类变量分配内存并且设置类变量为默认初始值，即零值  
        * 这里不包含final修饰的static，因为final在编译的时候就会分配了，准备阶段会显式初始化  
        * 这里不会为实例变量分配初始化，类变量会分配在方法区中，而实例变量是会随着对象一起分配到Java堆中。  
    3. 解析：  
        * 将常量池内的符号引用转换为直接引用的过程。  
        * 解析操作往往会伴随着JVM在执行完初始化之后再执行。  
        * 解析动作主要针对类或接口、字段、类方法、接口方法、方法类型等。  
3. 初始化：  
    * 初始化阶段就是执行类构造器方法\<clinit\>()的过程。  
    * 此方法不需定义，是javac编译器自动收集类中的所有类变量的赋值动作和**静态代码块**中的语句合并而来的。  
    * \<clinit\>()不同于类的构造器。(构造器是虚拟机视角下的\<init\>())  
    * 若该类具有父类，JVM会保证子类的\<clinit\>()执行前，父类\<clinit\>()已经执行完毕。  
    * 虚拟机必须保证一个类的\<clinit\>()方法在多线程下被同步加锁。

### 类加载器的分类  
* JVM支持两种类型的类加载器：**引导类加载器(Bootstrap ClassLoader)** 和**自定义类加载器(User-Defined ClassLoader)**  
* 从概念上讲，自定义类加载器一般指程序中由程序员自定义的一类类加载器，但Java虚拟机规范指出，将所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器  
* 无论定义咋分，最常见的三个类加载器如下图：  
（引导类加载器、扩展类加载器和系统类加载器）  
![](/images/JVM_Note/JVM_leijiazaiqifenlei.jpg)  
**这里的四者之间的关系是包含关系。不是上下层关系，也不是父类的继承关系。**  
Demo:  
    ```java
    public class ClassLoaderTest {
        public static void main(String[] args) {

            // 获取系统类加载器
            ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
            System.out.println(systemClassLoader); // sun.misc.Launcher$AppClassLoader@18b4aac2

            // 获取其上层：扩展类加载器
            ClassLoader extClassLoader = systemClassLoader.getParent();
            System.out.println(extClassLoader); // sun.misc.Launcher$ExtClassLoader@1b6d3586

            // 获取其上层：获取不到引导类加载器
            ClassLoader bootstrapClassLoader = extClassLoader.getParent();
            System.out.println(bootstrapClassLoader); // null

            // 对于用户自定义类来说：默认使用系统类加载器进行加载
            ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
            System.out.println(classLoader); // sun.misc.Launcher$AppClassLoader@18b4aac2

            // String类使用引导类加载器进行加载 ---> Java的核心类库都是使用引导类加载器进行加载
            ClassLoader classLoader1 = String.class.getClassLoader();
            System.out.println(classLoader1); // null
        
        }
    }
    ```  

### 启动类加载器(引导类加载器，Bootstrap ClassLoader)  
* 这个类加载器使用C/C++语言实现的，嵌套在JVM内部  
* 它用来加载Java的核心库(JAVA_HOME/jre/lib/rt.jar、resources.jar或sun.boot.class.path路径下的内容)，用于提供JVM自身需要的类  
* 并不继承自java.lang.ClassLoader，没有父加载器  
* 加载扩展类和应用程序类加载器，并指定为他们的父类加载器  
* 处于安全考虑，Bootstrap启动类加载器只加载包名为java、javax、sun等开头的类  

### 扩展类加载器(Extension ClassLoader)  
* Java编写  
* 派生于ClassLoader类  
* 父类加载器为启动类加载器  
* 从java.ext.dirs系统属性所指定的目录中加载类库，或从JDK的安装目录jre/lib/ext子目录(扩展目录)下加载类库。如果用户创建的JAR放在此目录下，也会自动由扩展类加载器加载  

### 应用程序类加载器(系统加载器，AppClassLoader)  
* Java编写  
* 派生于ClassLoader类  
* 父类加载器为扩展类加载器  
* 它负责加载环境变量classpath或系统属性 java.class.path 指定路径下的类库  
* 该类加载是程序中默认的类加载器，一般来说，Java应用的类都是由它完成加载的  
* 通过ClassLoader#getSystemClassLoader()方法可以获取到该类加载器  
```java  
    import java.net.URL;

    public class ClassLoaderTest {
        public static void main(String[] args) {
            System.out.println("*************启动类加载器*************");
            // 获取BootstrapClassLoader能够加载的api路径
            URL[] urls = sun.misc.Launcher.getBootstrapClassPath().getURLs();
            for(URL element : urls){
                System.out.println(element.toExternalForm());
            }
            /*
                *************启动类加载器*************
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/lib/resources.jar
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/lib/rt.jar
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/lib/sunrsasign.jar
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/lib/jsse.jar
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/lib/jce.jar
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/lib/charsets.jar
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/lib/jfr.jar
                file:/E:/Java/jdk8/jdk1.8.0_251/jre/classes
            */

            System.out.println("**************扩展类加载器**************");
            String extDirs = System.getProperty("java.ext.dirs");
            for(String path : extDirs.split(";")){
                System.out.println(path);
            }

            /*
                **************扩展类加载器**************
                E:\Java\jdk8\jdk1.8.0_251\jre\lib\ext
                C:\Windows\Sun\Java\lib\ext
            */
        }
    }
```  

### 用户自定义类加载器  
* 在Java的日常应用程序开发中，类的加载几乎是由上述三种类加载器相互配合执行的，在必要时，我们还可以自定义类加载器，类定制类的加载方式。  
* 目的：  
    * 隔离加载器  
    * 修改类加载器  
    * 扩展加载源  
    * 防止源码泄露 

### 获取ClassLoader的方式 
1. `class.getClassLoader()`：获取当前类的ClassLoader  
2. `Thread.currentThread().getContextClassLoader()`：获取当前线程上下文的ClassLoader  
3. `ClassLoader.getSystemClassLoader()`：获取系统的ClassLoader  
4. `DriverManager.getCallerClassLoader()`：获取调用者的ClassLoader  
  
### 双亲委派机制  
* Java虚拟机对class文件采用按需加载的方式，加载时采用双亲委派记至，即把请求交由父类处理，它是一种任务委派模式。  
* 原理：  
    1. 如果一个类加载器收到了类加载请求，它不会先自己加载，而是把这个请求委托给父类的加载器去执行；  
    2. 如果父类加载器还存在其父类加载器，则进一步向上层委托，以此类推，直到请求最终达到顶层的启动类加载器；  
    3. 如果父类加载器可以完成加载任务，就成功返回，倘若父类加载器无法完成此次加载任务，子加载器才会尝试自己去加载  
    ![](/images/JVM_Note/shuangqinweipai.jpg)  
* 优势：  
    1. 避免类的重复加载  
    2. 保护程序安全，防止核心API被随意篡改  

### 沙箱安全机制  
* 将Java代码限定在JVM特定的运行范围中，并且严格限制代码对本地系统资源访问，通过这样的措施来保证代码的有效隔离，防止对本地系统造成破坏。  
* 如果我们自定义一个类名为String的类，并且包为java.lang。如果没有沙箱安全机制，可能会污染到原本jdk中的String类。根据双亲委派机制，委托到顶层的启动类加载器，就可以完成String的加载，避免了代码污染。
  
### 线程  
* JVM允许多线程并行执行  
* 在Hotspot JVM中，每个线程都与操作系统的本地线程直接映射。当一个Java线程准备好执行后，此时一个操作系统的本地线程也同时创建。Java线程执行终止后，本地线程也会回收。  
* 操作系统负责所有线程的安排调度到任何一个CPU上。一旦本地线程初始化成功，它就会调用Java线程中的run()方法。 
  
## 运行时数据区  
![运行时数据区](/images/JVM_Note/yunxingshishujuqu.jpg "运行时数据区")  
![运行时数据区](/images/JVM_Note/yunxingshishujuqu2.jpg "运行时数据区")  
* Java虚拟机定义了若干种程序运行期间会使用到的运行时数据区，其中有一些会随着虚拟机启动而创建，随着虚拟机的退出而销毁。另外一些则是与线程一一对应，这些与线程对应的数据区会随着线程开始和结束而创建和销毁。  
* 上面第一张图中，红色区域(方法区和堆)为一个进程共享的区域，而灰色部分(PC寄存器、本地方法栈和虚拟机栈)则为一个线程一份。  
* 每个JVM只有一个Runtime实例。即为运行时环境。  

 
  
### 程序计数器(PC寄存器，Program Counter Register)  
#### 概述  
* 存储指令相关的现场信息，CPU只有把数据装载到寄存器才能够运行。  
* 这里的PC寄存器只是JVM对物理PC寄存器的一种抽象模拟，并不是物理寄存器。  
* 作用：PC寄存器用来存储指向下一条指令的地址，也即将要执行的指令代码，由执行引擎读取下一条指令。(和计组学的那个功能类似。。)  
* 它是一块很小的内存空间，运行速度最快的存储区域  
* 在JVM规范中，每个线程都有它自己的程序计数器，是线程私有的，生命周期与线程的生命周期保持一致。  
* 任何时间一个线程都只有一个方法在执行，也就是所谓的当前方法。程序计数器会存储当前线程正在执行的Java方法的JVM指令地址；或者，如果是在执行native方法，则是未指定值(undefined)  
* 它是程序控制流的指示器，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖它来完成。  
* 字节码解释器工作时是通过改变程序计数器的值来选取下一条需要执行的字节码指令。  
* 它是唯一一个在Java虚拟机规范中没有规定任何OutOfMemoryError情况的区域。  
  
#### 问题  
1. 使用PC寄存器存储字节码指令地址有什么用？(为什么使用PC寄存器记录当前线程的执行地址呢？)  
因为CPU需要不停地切换各个线程，这时候切换回来以后，就需要知道接着从哪开始继续执行。JVM的字节码解释器就需要通过改变PC寄存器的值来明确下一条应该执行什么样的字节码指令。  
2. PC寄存器为什么会被设定为线程私有？  
多线程就是在一个特定的时间内只会执行其中某一个线程，CPU会不停地做任务切换，因此就会涉及到中断和恢复。为了能够准确地记录每个线程正在执行的当前字节码指令地址，就必须为每个线程分配一个PC寄存器。  

### 虚拟机栈  
#### 概述  
* 栈是运行时的单位，堆是存储的单位  
* 生命周期与线程一致。即每个线程在创建时都会创建一个虚拟机栈，其内部保存一个个的栈帧(Stack Frame)，对应着一次次的Java方法调用。是线程私有的。  
* 作用：管Java程序的运行，保存方法的局部变量、部分结果。并参与方法的调用和返回。  
* 对于栈来说，不存在垃圾回收问题。  
* 栈中可能出现的异常：  
    * 如果采用固定大小的Java虚拟机栈，那每一个线程的Java虚拟机栈容量可以在线程创建的时候独立选定。如果线程请求分配的容量超过Java虚拟机栈允许的最大容量，Java虚拟机将会抛出**StackOverflowError**异常。  
    * 如果Java虚拟机栈可以动态扩展，并且在尝试扩展的时候无法申请到足够的内存，或者在创建新的线程时没有足够的内存区创建对应的虚拟机栈，那Java虚拟机将会抛出一个**OutOfMemoryError**异常。  
* 设置栈内存大小：`-Xss`  
#### 栈的存储单位  
* 每个线程都有自己的栈，栈中的数据都是以**栈帧(Stack Frame)**的格式存在  
* 在这个线程上正在执行的每个方法都各自对应一个栈帧  
* 栈帧是一个内存区块，是一个数据集，维系着方法执行过程中的各种数据信息  
* JVM对Java栈的操作：压栈和出栈  
* 当前执行的方法的栈帧：当前栈帧->当前方法->当前类  
* 不同线程中所包含的栈帧是不允许存在互相引用的  
* Java方法的两种返回方式：一是正常放回，return；另一种是抛出异常。不管使用哪种方式，都会导致栈帧被弹出。  
#### 栈帧的内部结构  
* 局部变量表(Local Variables)  
* 操作数栈(Operand Stack)(或表达式栈)  
* 动态链接(Dynamic Linking)(或指向运行时常量池的方法引用)  
* 方法返回地址(Return Address)(或方法正常退出或者异常退出的定义)  
* 一些附加信息  
PS：方法返回地址、动态链接和一些附加信息也称作帧数据区  
##### 局部变量表(Local Variables)  
* 也称局部变量数组或本地变量表  
* 定义一个**数字数组**，主要用于存储方法参数和定义在方法体内的局部变量(各类基本数据类型、对象引用(reference)、returnAddress类型)  
* 局部变量表建立在线程的栈上，是线程私有的数据，因此不存在数据安全问题  
* 局部变量表所需的容量大小是在**编译期**确定的，并保存在方法的Code属性的maximum local variables数据项中。在方法运行期间是不会改变局部变量表的大小的。  
* 方法嵌套调用的次数由栈的大小决定。站越大，方法嵌套调用次数越多。  
* 局部变量表中的变量只在当前方法调用中有效。在方法执行时，虚拟机通过使用局部变量表完成参数值到参数变量列表的传递过程。当方法调用结束后，随着方法栈帧的销毁，局部变量表也会随之销毁。  
* Slot:  
    * 局部变量表，最基本的存储单元是Slot(变量槽)  
    * 32位以内的类型只占用一个slot(包括returnAddress类型)；64位的类型(long和double)占用两个slot  
        > byte、short、char在存储前被转换为int，boolean也被转换为int(0为false，1为true)  
    * JVM会为局部变量表中的每一个Slot都分配一个访问索引，通过这个索引即可访问到局部变量表中指定的局部变量值  
    * 局部变量按照声明的顺序被复制到局部变量表的每一个Slot上  
    * 如果当前帧是由构造方法或者实例方法创建的，那么**该对象引用this将会存放在index为0的slot处**，其余的参数按照参数表顺序继续排列  

    Demo:  
    ```java
    package com.demo;

    public class LocalVariablesTest {
        public static void main(String[] args) {
            LocalVariablesTest test = new LocalVariablesTest();
            double a = 9.6;
            int b = 6;
            test.add(a, b);

        }

        public double add(double a, int b) {
            System.out.println("a = " + a);
            System.out.println("b = " + b);
            System.out.println("a + b = " + (a + b));
            return a + b;
        }
    }
    ```  
    ![](/images/JVM_Note/jububianliangbiao.jpg)  
    * 栈帧中的局部变量表中的槽位是可以重复利用的。如果一个局部变量过了其作用域，那么在其作用域之后申明的新的局部变量就很可能会服用过期局部变量的槽位，从而节省资源  
    ```java
    public void test(){
        int a = 0;
        {
            int b = 0;
            b = a + 1;
        }
        int c = a + 1;
    }
    ```  
    虽然上述方法有四个变量，但是局部变量表的槽位只有三个。变量b在出了括号后，过了其作用域。因此之后声明的变量c占了之前b的slot的位置。这样就节省了资源。  
    ![](/images/JVM_Note/JVM_jububianliangbiao2.jpg)  

{{< admonition type=tip title="回顾" open=true >}}
变量的分类：  
* 按照数据类型分：1. 基本数据类型 2. 引用数据类型  
* 按照在类中声明的位置分：  
    1. 成员变量：在使用前，都经历过默认初始化赋值  
        * 类变量：linking的prepare阶段：给类变量默认赋值 ---> initial阶段：给类变量显式赋值即静态代码块赋值  
        * 实例变量：随着对象的创建，会在对空间中分配实例变量空间，并进行默认赋值  
    2. 局部变量：在使用前，必须进行显式赋值。否则编译不通过
{{< /admonition >}}  
    
{{< admonition type=msg title="补充说明" open=true >}}
1. 在栈帧中，与性能调优关系最密切的部分就是局部变量表。在方法执行时，虚拟机使用局部变量表完成方法的传递。如果局部变量表中的变量不存在了，指向堆空间的指针也就不存在了，那么堆中垃圾就需要被回收  
2. 局部变量表中的变量也是重要的垃圾回收根节点，只要被局部变量表中直接或间接引用的对象都不会被回收
{{< /admonition >}}  

##### 操作数栈(Operand Stack)  
* 每个独立的栈帧中除了包含局部变量表以外，还包含一个后进先出的操作数栈，也可以成为表达式栈。  
* 主要用于保存计算过程的中间结果，同时作为计算过程中变量临时的存储空间。  
* 如果被调用的方法带有返回值，其返回值将会被压入当前栈帧的操作数栈中，并更新PC寄存器下一条需要执行的字节码指令。  
* 一个方法刚开始执行时，栈帧被创建，随之这个方法的操作数栈也被创建，但为空。且最大深度在编译期就定义好了。    
* 操作数栈**并非采用访问索引的方式来进行数据访问**，只能通过push和pop来操作，因为它是个**栈**，但是它是由数组实现的。  
* Java虚拟机的届时引擎是基于栈的执行引擎，其中栈指的就是操作数栈。  
  
#### 栈顶缓存技术(ToS, Top-of-Stack Cashing)  
基于栈式架构的虚拟机所使用的零地址指令更加紧凑，但完成一项操作的时候必然需要使用更多的入栈和出栈操作，使得需要更多的指令分派次数和内存读写此时。  
HotSpot JVM提出栈顶缓存技术(ToS, Top-of-Stack Cashing)，**将栈顶元素全部缓存在物理CPU的寄存器中，以此降低对内存的读写次数，提升执行引擎的执行效率**。  

#### 动态链接    
* 每一个栈帧内部都包含一个指向**运行时常量池**中**该栈帧所属方法的引用**。包含这个引用的目的就是为了支持当前方法的代码能够实现**动态链接(Dynamic Linking)**。比如invokedynamic指令  
* 在Java源文件被编译到字节码文件中时，所有的变量和方法引用都作为符号引用(Symbolic Reference)保存在class文件的常量池里。比如：描述一个方法调用另外的其他方法时，就是通过常量池中指向方法的符号引用来表示的，那么**动态链接的作用就是为了将这些符号引用转换为调用方法的直接引用**。
![](https://cdn.jsdelivr.net/gh/Leeeee16/blog_picBed/dataDynamicLinking.jpg)  

#### 方法的调用  
在JVM中，将**符号引用**转换为调用方法的**直接引用**与方法的绑定机制相关。  
* 静态链接：  
当一个字节码文件被装进JVM内部时，如果被调用的**目标方法在编译期可知**，且运行期保持不变时。这种情况下将调用方法的符号引用转换为直接引用的过程，称之为静态链接。  
* 动态链接：  
如果**被调用的方法在编译期无法被确定下来**，也就是说，只能够在程序运行期间将方法的符号引用转换为直接引用，由于这种引用转换过程具备动态性，因此称之为动态链接。  
- - -  
对应的方法的绑定机制为：早期绑定(Early Binding)和晚期绑定(Late Binding)。绑定是一个字段、方法或者类在符号引用被替换为直接引用的过程，这仅仅发生一次。  
* 早期绑定：  
就是指被调用的**目标方法如果在编译期可知，且运行时保持不变**时，即可将这个方法与所属的类型进行绑定，这样一来，由于明确了被调用的目标方法究竟是哪一个，因此就可以使用静态链接的方式将符号引用转换为直接引用。  
* 晚期绑定：  
如果**被调用的方法在编译期无法被确定下来，只能够在程序运行期根据实际的类型绑定相关的方法**，这种绑定方式也就被称为晚期绑定。  

#### 虚方法与非虚方法  
* 非虚方法：  
    * 如果方法在编译期就确定了具体的调用版本，这个版本在运行时是不可变的。  
    * 静态方法、私有方法、final方法、实例构造器、父类方法等  
    * 其他方法称为虚方法  
* 子类对象的多态性的使用前提：1. 类的继承关系 2. 方法的重写  
* 虚拟机中提供了一下几条方法调用指令：   
    * 普通调用指令：  
        1. invokestatic：调用静态方法，解析阶段确定唯一方法版本  
        2. invokespecial：调用<init>方法、私有及父类方法，解析阶段确定唯一方法版本  
        3. invokevirtual：调用所有虚方法  
        4. invokeinterface：调用接口方法  
    * 动态调用指令：  
        5. invokedynamic：动态解析出需要调用的方法，然后执行  
    * 前四条指令固化在虚拟机内部，方法的调用执行不可认为干预，而invokedynamic指令则支持由用户确定方法版本，其中invokestatic指令和invokespecial指令调用的方法称为虚方法，其余的（final修饰的除外）称为虚方法。  

