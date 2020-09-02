# C-sharp随笔

  
---
## out  
1. 如果在一个方法中想返回*多个相同类型*的值，可以考虑返回一个数组。
2. 如果返回*多个不同类型*的值，数组就不能用了，可以考虑用out参数。
  
注意:首先在形参中，用out写出要多余返回的值。然后out参数要求在方法的内部必须为其赋值（因为要返回啊）
```c#
using System;

namespace basic01
{
    
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("请输入用户名");
            string userName = Console.ReadLine();
            Console.WriteLine("请输入密码");
            string userPwd = Console.ReadLine();
            string msg;
            bool b = IsLogin(userName, userPwd, out msg);
            Console.WriteLine("登录结果：{0}", b);
            Console.WriteLine("登录信息：{0}", msg);
        }
        public static bool IsLogin(string name, string pwd, out string msg)
        {
            if(name=="admin" && pwd == "123456")
            {
                msg = "登陆成功";
                return true;
            }
            else if (name == "admin")
            {
                msg = "密码错误";
                return false;
            }
            else if (pwd == "123456")
            {
                msg = "用户名错误";
                return false;
            }
            else
            {
                msg = "未知错误";
                return false;
            }
        }
    }
}

```
  
## ref参数  
可以将一个变量带入一个方法中进行改变，完成后带出方法（类似c指针）。  
  
## params  
  
就是将实参列表中，跟可变参数数组类型一致的元素，都当成数组元素去处理。

{{< admonition  title="注意:" >}}
params可变参数必须是形参列表的最后一个。(为了和其他参数区分开~)
{{< /admonition >}}
`public 返回类型 方法名称( params 类型名称[] 数组名称 )`
  
```c#
using System;

namespace basic01
{
    
    class Program
    {
        static void Main(string[] args)
        {
            Test("张三", 95, 110, 75, 70);
            Console.ReadKey();
        }
        public static void Test(string name, params int[] score)
        {
            int sum = 0;
            for(int i = 0; i < score.Length; i++)
            {
                sum += score[i];
            }
            Console.WriteLine("{0}考试总成绩为{1}", name, sum);
        }
    }
}

```
  
## 属性  
属性的作用是保护字段、对字段的赋值和取值进行限定。  
属性的本质就是两个方法。get()和set()。
```c#
using System;

namespace basic01
{
    public class Person
    {
        private string _name;
        public string Name
        {
            get { return _name; }
            set { _name = value; }
        }
        private int _age;
        public int Age
        {
            get { return _age; }
            set 
            {
                if(value<0 || value > 100)
                {
                    value = 0;
                }
                _age = value; 
            }
        }
        public void Say()
        {
            Console.WriteLine("我是{0},我今年{1}岁！",this._name, this._age);
        }

    }
    class Program
    {
        static void Main(string[] args)
        {
            Person p1 = new Person();
            p1.Name = "张三";
            p1.Age = 18;
            p1.Say();
            Console.ReadKey();
        }

    }
}

```  
## 构造函数  
帮助我们初始化对象时，给对象的每个属性一次赋初值。
{{< admonition  title="注意:" >}}
1. 构造函数没有返回值，void也不行。
2. 构造函数的名称必须和类名一样。
{{< /admonition >}}
  
## 析构函数  
当程序结束时，析构函数才执行。帮助我们释放资源。  
`~类名(){...}`
  
## 委托  
将一个方法作为参数，传递给另一个方法。  
我们知道，传参时都需要有类型，比如int、string等等。那么方法作为参数时，他的类型就是委托。
  
```c#
using System;

namespace basic01
{
    //声明一个委托
    public delegate int DelCompare<T>(T o1, T o2);
    class Program
    {
        // int型数组最大值
        public static T GetMax<T>(T[] nums, DelCompare<T> del)
        {
            T max = nums[0];
            for (int i= 0;i< nums.Length; i++)
            {
                if (del(max,nums[i])<0)
                {
                    max = nums[i];
                }
            }
            return max;
        }

        static void Main(string[] args)
        {
            int[] o1 = { 1, 2, 5, 3, 11, 23 };
            string[] o2 = { "abc", "defghijk", "lmnopqrstuvw", "xyz" };
            int result1 = GetMax<int>(o1, Compare1);
            string result2 = GetMax<string>(o2, Compare2);
            Console.WriteLine("{0}", result1);
            Console.WriteLine("{0}", result2);
            Console.ReadKey();
        }

        public static int Compare1(int o1, int o2)
        {
            int n1 = (int)o1;
            int n2 = (int)o2;
            return n1 - n2;
        }
        public static int Compare2(string o1, string o2)
        {
            string s1 = (string)o1;
            string s2 = (string)o2;
            return s1.Length - s2.Length;
        }
    }
}

```
