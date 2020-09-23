# Java基础

  
* * *
>重新看看Java基础  
>顺便随便记一些基础的东西。。:smiley:
* * *
  

## Java内存结构概述  
1. 栈(Stack): 存放方法中的局部变量。一旦超出作用域，java会自动释放它的内存空间。  
2. 堆(Heap): 凡是new出来的东东（对象、数组等等），都存放在堆中。  
堆中的东西都有一个内存地址值（16进制），且都有一个默认初始值。规则如下：  
int默认为0；double默认为0.0；字符默认为'\u0000'；布尔默认为false；引用类型默认为null。  
实体不再被使用后，会在不确定的时间内被垃圾回收器回收。  
3. 方法区(Method Area)：包含class和static变量。  
4. 本地方法栈(Native Method Stack)：与操作系统相关。  
5. 寄存器(pc Register)：CPU相关。CPU访问寄存器执行速度远大于在主存上的执行速度。
  
## 面向对象三大特征  
1. 封装：将实现的细节隐藏，对外界不可见。需要的时候直接调用即可，不需要知道实现细节。  
    （1） 方法就是一种封装  
    （2） 关键字private也是一种封装：使用private修饰后，只能在类的内部进行访问。在本类范围外则不能直接访问，不能对其进行修改。间接访问static成员变量，可以定义一组getter/setter方法。  
2. 继承：将共性提取到父类中。继承是多态的前提。  
Java是**单继承**的。可以**多级继承**。    

  
3. 多态：
  
## this关键字  
方法的局部变量和类的成员变量重名时，会就近使用方法的局部变量。如果要访问类的成员变量，需要使用this。格式：this.成员变量名。  
>ps：谁调用的方法，谁就是this。也就是当前的对象。
  
## 构造方法  
构造方法是用来创建对象的方法。一般通过new关键字来创建对象时调用的。格式：  
```Java
public 类名称(参数类型 参数名称, ...){
    方法体
}
```  
注：
1. 构造方法的名称必须和类名称完全一样。  
2. 构造方法无返回值。  
3. 如果无构造方法，默认一个无参构造方法，方法体啥都不做。  
4. 构造方法可以重载（方法名相同，参数列表不同）。 
  
## 标准Java类  
1. 所有成员变量使用private修饰。  
2. 为每个成员变量写一对Getter/Setter方法。  
3. 编写一个无参构造方法。  
4. 编写一个全参构造方法。
  
## Scanner类  
从键盘输入数据。  
1. 导包：`import java.util.Scanner`  
2. 创建对象：`Scanner sc = new Scanner(System.in)`  
3. 使用：参数类型 变量名 = sc.成员方法名` 
    从键盘获取一个int：`int num = sc.nextInt()`  
    从键盘获取一个String：`String str = sc.next()`  

## ArrayList  
1. 创建：`ArrayList<E> list = new ArrayList<>();`。E表示泛型。  
2. `System.out.print(list)`打印的是list的内容，不是地址。  
3. public boolean add(E e)：向集合添加元素，参数类型与创建集合的泛型一致。  
4. public int size()：获取list长度。  
5. public E remove(int index)：从集合删除索引为index的元素，返回被删除的元素。  
6. public E get(int index)：获取下标为index的元素，并返回该元素。  
7. 泛型只能为引用类型，不能为基本类型。必须使用基本类型的包装类。  

|  基本类型  |  包装类 |
| :------: | :-------: |
| byte     | Byte      |
| int      | Integer   |
| short    | Short     |
| long     | Long      |
| float    | Float     |
| double   | Double    |
| char     | Character |
| boolean  | Boolean   |

## String  
1. 字符串的构造方法  
    ```java
    public class DemoString {
        public static void main(String[] args) {
            String str1 = new String();
            System.out.println("第一个字符串:" + str1);   // 第一个字符串:

            char[] c = {'A', 'B', 'C'};
            String str2 = new String(c);
            System.out.println("第二个字符串：" + str2);   // 第二个字符串：ABC

            byte[] b = {97, 98, 99};
            String str3 = new String(b);
            System.out.println("第三个字符串：" + str3);   // 第三个字符串：abc

            String str4 = "Hello";
            System.out.println("第四个字符串：" + str4);   // 第四个字符串：Hello
        }
    }
    ```  
    
2. 字符串常量池  
    程序中直接用双引号表示的字符串，就在字符串池中。  
    字符串常量池存在堆中。因为引号表示的字符串，是不能更改的，相当于一个常量。而通过字符数组（或者字节数组）创建的字符串的内容，根据字符数组的改变而改变，它存的地址是数组地址。

    >对于基本类型来说，== 是进行***数值***的比较；  
    >对于引用类型来说，== 是进行***地址值***的比较。  
    ```java
    public class DemoStringPool {
        public static void main(String[] args) {
            String str1 = "abc";
            String str2 = "abc";

            char[] c = {'a', 'b', 'c'};
            String str3 = new String(c);

            System.out.println(str1 == str2);   //true
            System.out.println(str1 == str3);   //false
            System.out.println(str3 == str2);   //false
        }
    }
    ```
      
3. 字符串常用方法  
- equals  
    `public boolean equals(object obj)`：参数可以是任意对象，只有参数为字符串并且内容想到才返回true，否则返回false。
    ```java
    public class DemoStringEquals {
        public static void main(String[] args) {
            String str1 = "LeLe";
            String str2 = "LeLe";
            char[] c = {'L','e','L','e'};
            String str3 = new String(c);
            String str4 = "lele";

            System.out.println(str1.equals(str2));  //true
            System.out.println(str2.equals(str3));  //true
            System.out.println(str1.equals(str3));  //true
            System.out.println(str1.equals(str4));  //false
        }
    }
    ```  
- equalsIgnoreCase  
    `public boolean equalsIgnoreCase(String str)`：忽略大小写，比较内容。  
    上面的例子中：  
    `System.out.println(str1.equalsIgnoreCase(str4));    // true`
      
- length  
    `str1.length()`：获取字符串字符个数。  
  
- concat  
    `public String concat(String str)`：将当前字符串和参数字符串拼接，并返回新的字符串。  
- charAt  
    `public char charAt(int index)`：获取指定索引位置的单个字符。索引从0开始。  
- indexOf  
    `public int indexOf(String str)`：查找参数字符串在本字符串当中首次出现的索引位置，如果没有，返回-1。  
    **上面四个方法的示例如下：**  
    ```java
    public class DemoStringGet {
        public static void main(String[] args) {
            String str1 = "LeLe";
            System.out.println("str1的长度为：" + str1.length());    // str1的长度为：4

            String str2 = "Hello";
            System.out.println("str1 + str2为：" + str1.concat(str2));    // str1 + str2为：LeLeHello

            System.out.println("str2的第3个字符为：" + str2.charAt(1));    // str2的第3个字符为：e

            System.out.println("Le在str1中首次出现的索引为:" + str1.indexOf("Le"));   // Le在str1中首次出现的索引为:0
        }
    }
    ```  
- substring  
    `public String substring(int index):`：截取从参数位置一直到字符串结尾。返回新字符串。  
    `public String substring(int begin, int end)`：截取从begin到end中间的字符串。**左闭右开[begin,end)**  
    ```java
    public class DemoSubString {
        public static void main(String[] args) {
            String str1 = "Hello,LeLe";

            System.out.println(str1.substring(6));  // LeLe
            System.out.println(str1.substring(0,5));    // Hello
        }
    }
    ```  

- toCharArray  
    `public char[] toCharArray()`：将当前字符串拆分成字符数组作为返回值。  
- getBytes  
    `public byte[] getBytes()`：获取当前字符串底层的字节数组。  
- replace  
    `public String replace(CharSequence oldString,CharSequence newString)`：将所有出现的老字符串替换成新的字符串，返回替换之后的新的字符串。  
    ```java
    public class DemoStringConvert {
        public static void main(String[] args) {
            String str1 = "LeLe";
            char[] c = str1.toCharArray();
            System.out.println(c[1]);   // e

            byte[] b = str1.getBytes();
            System.out.println(b[2]);   // 76

            System.out.println(str1.replace("Le", "Ba"));
            // BaBa
        }
    }
    ```

- split  
    `public String[] split(String regex)`：按照参数的规则，将字符串切分成若干部分。  
    ```java
    public class DemoStringSplit {
        public static void main(String[] args) {
            String str1 = "Hello,LeLe,HaHa,Zhang San";
            String[] array1 = str1.split(",");
            for (int i = 0; i < array1.length; i++) {
                System.out.println(array1[i]);
            }
            // Hello
            // LeLe
            // HaHa
            // Zhang San
        }
    }
    ```  
    >注意：split方法的参数其实是一个**正则表达式**。需要注意转义。如按照英文句点"."来切分，必须写" \\\\. "
    
## HashMap（待补充）  
1. 底层？  
    * 初始大小16，默认负载因子0.75（可自定义），12时扩容，扩容一倍即32  
    * HashMap存的是Node节点，Node节点里包含key和value  
    * HashMap由数组+单向链表

## static  
1. 使用static关键字修饰的成员变量，不再属于对象自己，而是属于类的。所有的对象共享同一份。  
2. 使用static关键字修饰的成员方法，就是静态方法，它不再属于对象，而是属于类的。  
3. 如果没有static关键字，必须先创建对象，然后通过对象来使用。  
如果有static关键字，可以直接通过类名称来使用。  
无论是成员变量还是成员方法，如果有了static关键字修饰，都推荐使用类名称进行使用。  
4. 静态代码块：当第一次用到本类时，静态代码快执行唯一的一次。  
静态内容总是优先于非静态，所以静态代码快比构造方法先执行。  
一般用来一次性对静态成员变量进行赋值。  
```java
public class 类名称{
    static {
        // 静态代码块的内容
    }
}
```
> 1. 静态不能直接访问非静态。因为内存中是先有的静态内容，后有的非静态内容。  
> 2. 静态方法中不能使用this。this代表当前对象。  
> 3. 静态变量存在内存中的方法区里的静态区中。根据类名称访问静态成员变量的时候，和对象（存在内存的堆中）没关系，只和类有关系。    

Demo01  
```java
public class Student {
    private int id;
    private String name;
    private int age;
    private static int idCounter = 0;

    static String room;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
        this.id = ++idCounter;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}  

public class DemoStaticField {
    public static void main(String[] args) {
        Student one = new Student("张三", 20);
        Student.room = "Room 101";
        System.out.println("id:" + one.getId() + ",name:" + one.getName()
                + ",age:" + one.getAge() + ",room:" + Student.room);

        Student two = new Student("呆子", 10);
        System.out.println("id:" + two.getId() + ",name:" + two.getName()
                + ",age:" + two.getAge() + ",room:" + Student.room);
    }
    // output:
    //  id:1,name:张三,age:20,room:Room 101
    //  id:2,name:呆子,age:10,room:Room 101
}
```

Demo02  
```java
public class MyClass {

    int num = 1;
    static int numStatic = 2;

    // 成员方法
    public void method() {
        System.out.println("一个普通的成员方法。");
        // 成员方法可以访问成员变量，也可以访问静态变量
        System.out.println(num);
        System.out.println(numStatic);
    }

    // 静态方法
    public static void methodStatic() {
        System.out.println("这是一个static方法。");
        // 静态方法只能访问静态变量
        System.out.println(numStatic);
    }
}

public class Demo01StaticField {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.method();

        // 静态方法可以使用对象名来调用，也可以直接通过类名称来调用
        obj.methodStatic(); // 不推荐，这种写法编译后仍会被翻译成“类名称.静态方法名”
        MyClass.methodStatic();  // 推荐
    }
}
```
  
## 重写  
1. 重写：在继承关系中，方法名一样，参数一样。  
重载：方法名一样，参数不一样。  
2. @override写在方法前，检测是否是有效的正确的重写。  
  
## 父子类构造方法的访问  
1. 子类构造方法中，默认一个`super();`调用，必须先调用父类构造，再调用子类构造。  
2. 子类构造可以通过super关键字来调用父类重载构造。  
3. super的父类构造调用必须是子类构造方法中的第一个语句，且只能有一个super调用。
  
## super关键字  
1. 在子类成员方法中访问父类的成员变量。`super.xxx`  
2. 在子类的成员方法中访问父类的成员方法。`super.xxx()`  
3. 在子类的构造方法中访问父类的构造方法。`super()`  
  
## this关键字  
1. 在本类的成员方法中，访问本类的成员变量。`this.xxx`  
2. 在本类的成员方法中，访问本类的另一个成员方法。`this.xxx()`  
3. 在本类的构造方法中，访问本类的另一个构造方法。`this(...)`  
> 注意：在第三种用法中，`this(...)`调用必须也是构造方法的第一个且唯一的语句。super和this两种构造调用，不能同时使用。  
  
## 抽象类  
1. 抽象类不能创建对象。  
2. 抽象类可以有构造方法，是供子类创建对象时，初始化父类成员使用的。  
3. 抽象类不一定包含抽象方法，有抽象方法一定是抽象类。  
4. 抽象类的子类，必须重写抽象父类中所有的抽象方法。
  
## 接口  
- 接口没有静态代码块或者构造方法。  
- 一个类的直接父类是唯一的，单一个类可以同时实现多个接口。  
- 如果实现类所实现的多个接口中，存在重复的抽象方法，只需要覆盖重写一次即可。  
- 如果实现类没有覆盖重写所有接口的所有抽象方法，那么实现类必须为一个抽象类。  
- 如果实现类所实现的多个接口中，存在重复的默认方法，那么实现类一定要对冲突的默认方法进行覆盖重写。  
- 一个类如果父类中的方法，和接口中的默认方法产生冲突，优先用父类当中的方法。  
  
### 成员变量  
格式： `[public] [static] [final] 数据类型 常量名称 = 数据值;`  
> 常量必须进行赋值，一旦赋值，不能改变。  
> 常量命名建议全大写，用下划线分隔。  
  
### 接口中的抽象方法  
格式：  `[public] [abstract] 返回值类型 方法名称(参数列表);`  
> 实现类必须覆盖重写接口所有的抽象方法，除非实现类也是抽象类。  
  
### 接口的默认方法  
格式：  
```java
[public] default 返回值类型 方法名称(参数列表){
    方法体
}
```  
>1. 接口中的默认方法，可以解决接口升级的问题。如果接口需要添加一个新的抽象方法，但不能影响以及运行的实现类，则可以使用默认方法。  
>2. 接口的默认方法，可以通过接口实现类的对象直接调用。  
>3. 接口的默认方法，也可以被接口实现类进行覆盖重写。  
  
### 接口的静态方法  
格式：  
```java
public static 返回值类型 方法名称(参数列表){
    方法体
}
```  
>静态方法不能通过接口实现类的对象来调用，必须通过接口名称来调用。  
格式：接口名称.静态方法名(参数);  
  
### 私有方法  
（Java 9之后）格式：  
1. 普通私有方法：`public 返回值类型 方法名称(参数列表) { 方法体 }`  
2. 静态私有方法：`public static 返回值类型 方法名称(参数列表) { 方法体 }`  
> private的方法只有接口自己使用。  
  
## Iterator迭代器  
对集合进行遍历。两个常用方法：  
1. boolean hasNext():如果仍然有元素可以迭代，则返回true；判断集合是否还有下一个元素，有则返回true，无则返回false。
2. E next():返回迭代的下一个元素；取出集合中的下一个元素。  
Iterator是一个接口，无法直接用，需要使用Iterator接口的实现类对象。  
Collection接口中的iterator()方法，返回的就是迭代器的实现类对象  
`Iterator<E> iterator()` 返回在此collection的元素上进行迭代的迭代器。  
```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class DemoIterator {
    public static void main(String[] args) {
        Collection<String> coll = new ArrayList<>();
        coll.add("张三");
        coll.add("马云");
        coll.add("AAA");
        coll.add("BBB");
        Iterator<String> it = coll.iterator();

        while(it.hasNext()){
            String e = it.next();
            System.out.println(e);
        }
    }
}
```  
  
## for遍历  
使用的Iterator迭代器，形式上是for。  
```java
import java.util.ArrayList;

public class DemoFor {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();
        for (int i = 0; i < 6; i++) {
            list.add(i);
        }
        for (int i:list) {
            System.out.println(i);
        }
    }
}
```  
  
## 泛型  
未知数据类型，当创建对象或者具体使用的时候，再确定泛型的数据类型。  
```java
public class GenericClass<E> {
    private E name;

    public E getName() {
        return name;
    }

    public void setName(E name) {
        this.name = name;
    }
}

public class DemoFanxing {
    public static void main(String[] args) {
        GenericClass<Integer> gc = new GenericClass<>();

        gc.setName(6);
        Integer gcName = gc.getName();
        System.out.println(gcName); // 6
    }
}
```  
  
## 反射  
- 反射机制：将类的各个组成部分封装为其他**对象**。  
- java代码经历的三个阶段：  
    - Source源代码阶段(存在硬盘)：源码(xxx.java),编译后，字节码文件(xxx.class)。字节码文件存放成员变量、构造方法、成员方法等等。  

    - Class类对象阶段：通过类加载器(ClassLoader)将字节码文件加载到内存中去。  
    在内存中如何表述字节码文件的内容？Class类对象。  
    Class类对象包括用来描述成员变量、构造方法、成员方法的若干对象。  
    如成员变量，使用`Field[] fields`这样一个数组来表示所有的成员变量。  
    如构造方法，使用`Constructor[] cons`来表示所有的构造器。  
    成员方法，使用`Method[] methods`来表示所有成员方法。  
    之后可以通过Class类对象的行为，来new对象啊。。等等  

    - Runtime运行时阶段:xxx对象，new xxx()。  

### 获取Class类对象的方式  
1. Class.forName("全类名")：将字节码文件加载进内存，返回Class对象。  
    - 多用于配置文件，将类名定义在配置文件中。读取文件，加载类。  
2. 类名.class：通过类名的属性class获取。  
    - 多用于参数的传递。  
3. 对象.getClass()：getClass()方法在Object类中定义。  
    - 多用于对象的获取字节码的方式。  

    Demo：  
    ```java
        public class ReflectDemo {
            public static void main(String[] args) throws Exception {

                // 1. Class.forName("全类名")
                Class cls1 = Class.forName("com.demo.person.Person");
                System.out.println(cls1);   // class com.demo.person.Person

                // 2. 类名.class
                Class cls2 = Person.class;
                System.out.println(cls2);   // class com.demo.person.Person
                // 3. 对象.getClass();
                Person p = new Person();
                Class cls3 = p.getClass();
                System.out.println(cls3);   // class com.demo.person.Person

                // ==比较三个对象
                System.out.println(cls1==cls2); // true
                System.out.println(cls1==cls3); // true
            }
        }
    ```
- 结论：同一个字节码文件(*.class)在一次程序运行过程中，只被加载一次，不论通过哪一种方式获取的Class对象都是同一个。  
  
### Class对象的功能  
- 获取功能：
    1. 获取成员变量  
        - Field[] getFields()：获取所有**public**修饰的成员变量  
        - Field getField(String name)：获取指定名称的**public**修饰的成员变量
        - Field[] getDeclaredFields():获取所有的成员变量，不考虑修饰符  
        - Field getDeclaredFields(String name)  
        - Field操作：  
            1. 设置值：`void set(object obj, boject value)`  
            2. 获取值：`get(object obj)`  
            3. 忽略访问权限修饰符的安全检查(暴力反射)：setAccessible(true)  
      
        person类：
        ```java
            public class Person {
                private String name;
                private int age;
                public String public_a;
                protected int protected_b;
                int c;
                private int private_d;

                public Person() {
                }

                public Person(String name, int age) {
                    this.name = name;
                    this.age = age;
                }

                public int getAge() {
                    return age;
                }

                public void setAge(int age) {
                    this.age = age;
                }

                public String getName() {
                    return name;
                }

                public void setName(String name) {
                    this.name = name;
                }

                @Override
                public String toString() {
                    return "Person{" +
                            "name='" + name + '\'' +
                            ", age=" + age +
                            ", public_a='" + public_a + '\'' +
                            ", protected_b=" + protected_b +
                            ", c=" + c +
                            ", private_d=" + private_d +
                            '}';
                }

                public void eat(){
                    System.out.println("eating...");
                }

                public void eat(String food){
                    System.out.println("eating..." + food);
                }
            }
        ```

        Demo：
        ```java
            public class ReflectDemo02 {
                public static void main(String[] args) throws Exception {
                    // 0. 获取person.Class对象
                    Class personClass = Person.class;
                    // 1. Field[] getFields()
                    Field[] fields = personClass.getFields();
                    for (Field field : fields) {
                        System.out.println(field);  // public int com.demo.classDemo.Person.public_a
                    }
                    System.out.println("-----------------");

                    // 2. Field getField(String name)
                    Field a = personClass.getField("public_a");
                    // 获取成员变量a的值
                    Person p = new Person();
                    Object value = a.get(p);
                    System.out.println(value);  // null
                    // 设置值
                    a.set(p, "张三"); // Person{name='null', age=0, public_a='张三', protected_b=0, c=0, private_d=0}
                    System.out.println(p);
                    System.out.println("-------------------");

                    // 3.Field[] getDeclaredFields()
                    Field[] declaredFields = personClass.getDeclaredFields();
                    for(Field declaredField : declaredFields){
                        System.out.println(declaredField);
                    }
                    /*
                    private java.lang.String com.demo.classDemo.Person.name
                    private int com.demo.classDemo.Person.age
                    ...共6个...
                    */
                    // 4. Field getDeclaredFields(String name)
                    Field d = personClass.getDeclaredField("private_d");
                    // 忽略访问权限修饰符的安全检查，否则会报错
                    d.setAccessible(true);  // 暴力反射
                    Object value2 = d.get(p);
                    System.out.println(value2); // 0
                }
            }
        ```
    2. 获取构造方法  
        - Constructor<?>[] getConstructors()  
        - Constructor\<T\> getConstructor(类<?>... parameterTypes)
        - Constructor\<T\> getDeclaredConstructors()  
        - Constructor<?>[] getDeclaredConstructor(类<?>... parameterTypes)  
        - Constructor:构造方法  
            - 创建对象：`T newInstance(object... initargs)`  

        Demo：  
        ```java
            public class ReflectDemo03 {
                public static void main(String[] args) throws Exception {
                    Class personClass = Person.class;

                    // Constructor<?>[] getConstructors()
                    Constructor constructor1 = personClass.getConstructor(String.class, int.class);
                    System.out.println(constructor1);  // public com.demo.classDemo.Person(java.lang.String,int)
                    // 创建对象
                    Object person = constructor1.newInstance("张三", 18);
                    System.out.println(person); // Person{name='张三', age=18, public_a='null', protected_b=0, c=0, private_d=0}
                    System.out.println("---------------------");
                }
            }
        ```  
    3. 获取成员方法  
        - Method[] getMethods()  
        - Method getMethod(String name, 类<?>.. parameterTypes)
        - Method[] getDeclaredMethods()  
        - Method getDeclaredMethod(String name, 类<?>.. parameterTypes)  
        - Method:方法对象  
            - 执行方法:`object invoke(object obj, object...args)`  
            - 获取方法名称:`String getName`  
      
        Demo:
        ```java
        public class ReflectDemo04 {
            public static void main(String[] args) throws Exception {
                Class personClass = Person.class;

                // 获取指定名称的方法
                Method eat_method = personClass.getMethod("eat");
                Person p = new Person();
                // 执行方法
                eat_method.invoke(p);   // eating...

                Method eat_method2 = personClass.getMethod("eat", String.class);
                eat_method2.invoke(p,"apple");  // eating...apple
                System.out.println("----------------");

                //获取所有public修饰的方法
                Method[] methods = personClass.getMethods();
                for(Method method:methods){
                    System.out.println(method); // 除了Person的方法，还有Object里的方法
                //method.setAccessible(true);
                }
            }
        }
        ```
    4. 获取类名  
        - String getName()  

### 案例  
- 需求:可以创建任意类的对象，并且执行其中任意的方法。  
- 实现：  
    1. 配置文件  
    2. 反射  
- 步骤：  
    1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中  
    2. 在程序中加载读取配置文件  
    3. 使用反射技术来加载类文件进内存  
    4. 创建对象  
    5. 执行方法  
配置文件（pro.properties）：注意：配置文件放在src目录下
    `className=com.demo.classDemo.Person`  
    `methodName=eat`

```java
    public class ReflectTest {
        public static void main(String[] args) throws Exception {
            /*
                前提：不能改变改类的任何代码，可以创建任意的对象，可以执行任意方法
            */
            // 1. 加载配置文件
            // 1.1 创建Properties对象
            Properties pro = new Properties();
            // 1.2 加载配置文件，转换为一个集合
            // 1.2.1 获取class目录下的配置文件
            ClassLoader classLoader = ReflectTest.class.getClassLoader();
            InputStream is = classLoader.getResourceAsStream("pro.properties");
            pro.load(is);

            // 2. 获取配置文件中定义的数据
            String className = pro.getProperty("className");
            String methodName = pro.getProperty("methodName");

            // 3.加载该类进内存
            Class cls = Class.forName(className);
            // 4.创建对象
            Object obj = cls.newInstance();
            //5.获取方法对象
            Method method = cls.getMethod(methodName);
            //6.执行方法
            method.invoke(obj);

        }
    }
```  
  
## 注解  
- 概念：说明程序的。给计算机看的。  
- 使用：`@注解名称`  
- 分类：
    1. 生成javadoc文档  
    2. 编译检查（如`@Override`）  
    3. 代码分析（用反射）  
- JDK中预定义的一些注解  
    - `@Override`:检测被该注解标注的方法是否继承自父类（接口）的  
    - `@Deprecated`：该注解标注的内容表示已过时  
    - `@SuppressWarnings("all")`：压制警告  
- 自定义注解  
    - 格式：
        - 元注解  
        - `public @interface 注解名称{ 属性列表; }`  
    - 本质：注解本质上是一个接口，该接口默认继承Annotation接口  
    `public interface Myanno extends java.lang.annotation.Annotation {}`  
    - 属性：接口中的抽象方法  
        - 要求：
            1. 属性的返回值类型有以下取值
                - 基本数据类型  
                - 字符串  
                - 枚举  
                - 注解  
                - 以上类型的数组  
            2. 定义了属性，在使用时需要给属性赋值。  
                - 定义属性时可以使用**default**设置默认值。  
                - If只有一个属性需要赋值，并且属性的名称是**value**，则**value**可以省略。  
                - 数组赋值时，值使用{ }。如果数组中只有一个值，可省略{ }。  
    - 元注解：用于描述注解的注解。  
        - `@Target`：描述注解能够作用的位置
            - ElementType取值：
                - TYPE：可以作用于类上  
                - METHOD：可以作用于方法上  
                - FIELD：可以作用于成员变量上  
        - `@Retention`：描述注解被保留的阶段  
            - `@Retention(RetentionPolicy.RUNTIME)`：当前被描述的注解，会保留到Class字节码文件中，并被JVM读取到。
        - `@Documented`：描述注解是否被抽取到API文档中  
        - `@Inherited`：描述注解是否被子类继承  

- 在程序使用（解析）注解：获取注解中定义的属性值  
    1. 获取注解定义的位置（类、方法等等）的对象。（Class、Method、Field）  
    2. 获取指定的注解。`getAnnotation(Class)`  
    3. 使用注解中的抽象方法获取配置的属性值。  
      
    注解：
    ```java
        import java.lang.annotation.ElementType;
        import java.lang.annotation.Retention;
        import java.lang.annotation.RetentionPolicy;
        import java.lang.annotation.Target;

        /**
        * 描述需要执行的类名和方法名
        */
        @Target({ElementType.TYPE}) // 作用在类上面
        @Retention(RetentionPolicy.RUNTIME)
        public @interface Pro {

            String className();
            String methodName();
        }
    ```  
      
    Demo:
    ```java
        @Pro(className = "com.demo.annotation.China",methodName = "show")
        public class ReflectTest {
            public static void main(String[] args) throws Exception {
            /*
                前提：不能改变改类的任何代码，可以创建任意的对象，可以执行任意方法
            */

                // 1. 解析注解
                // 1.1 获取该类的字节码文件对象
                Class<ReflectTest> reflectTestClass = ReflectTest.class;
                // 2. 获取上面的注解对象
                // 在内存中生成了一个该注解接口的子类实现对象
                /*
                    public class ProImpl implements Pro{
                        public String className(){
                            return "com.demo.annotation.China";
                        }
                        public String methodName(){
                            return "show";
                        }
                    }
                */
                Pro annotation = reflectTestClass.getAnnotation(Pro.class);
                // 3. 调用注解对象中定义的抽象方法，获取返回值
                String className = annotation.className();
                String methodName = annotation.methodName();
                System.out.println(className);  // com.demo.annotation.China
                System.out.println(methodName); // show
            }
        }
    ```  

