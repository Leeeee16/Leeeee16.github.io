# 04-一些多进程

  
  
* * *
最近在看拉钩上崔大的爬虫课，稍微整理一些知识点。:wink:
  
[课程链接](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=46#/content)
* * *

### Python多进程的实现
  
Python中的multiprocessing提供了一些组件：Process、Queue、Semaphore、Pipe、Lock、Pool等。
  
### 使用Process类
  
`Process([group [, target [, name [, args [, kwargs]]]]])`

参数说明：
- target: 调用对象，可以传入方法的名字  
- args: 表示被调用对象的位置参数元组，比如 target 是函数 func，他有两个参数 m，n，那么 args 就传入 [m, n] 即可。
- kwargs:表示调用对象的字典  
- name: 别名，给进程取一个名字。  
- group： 分组。
  
```python
import multiprocessing

def process(index):
    print(f'Process: {index}')

if __name__ == '__main__':
    for i in range(5):
        p = multiprocessing.Process(target=process, args=[i])
        p.start()
```
输出：  
```
Process: 0    
Process: 1    
Process: 2    
Process: 3    
Process: 4
```  

multiprocess还提供了几个方法，cpu_count可以获取当前机器的CPU的核心数量，active_children可以获取当前还在运行的所有进程。
  
```python
import multiprocessing
import time

def process(index):
    time.sleep(index)
    print(f'Process: {index}')

if __name__ == '__main__':
    for i in range(5):
        p = multiprocessing.Process(target=process, args=(i,))
        p.start()

    print(f'CPU number: {multiprocessing.cpu_count()}')
    for p in multiprocessing.active_children():
        print(f'Child process name: {p.name} id: {p.pid}')
    print('Process Ended')

```
输出结果：  
```
CPU number: 8  
Child process name: Process-4 id: 23724  
Child process name: Process-3 id: 16492  
Child process name: Process-1 id: 18096  
Child process name: Process-2 id: 17516  
Child process name: Process-5 id: 16928  
Process Ended  
Process: 0  
Process: 1  
Process: 2  
Process: 3  
Process: 4 
```
  
### 继承Process类
  
```python
from multiprocessing import Process
import time

class MyProcess(Process):
    def __init__(self, loop):
        Process.__init__(self)
        self.loop = loop

    def run(self):
        for count in range(self.loop):
            time.sleep(1)
            print(f'Pid: {self.pid} LoopCount: {count}')

if __name__ == '__main__':
    for i in range(2, 5):
        p = MyProcess(i)
        p.start()
```
构造了MyProcess类继承了Process，声明了一个loop参数，用来表示循环次数。在run方法中，使用loop，即循环loop次并打印当前的进程号和循环次数。  
注：这里进程的执行逻辑在run方法中实现，启动进程需要调用start方法。执行结果如下：
```
Pid: 9552 LoopCount: 0
Pid: 13400 LoopCount: 0
Pid: 18972 LoopCount: 0
Pid: 9552 LoopCount: 1
Pid: 13400 LoopCount: 1
Pid: 18972 LoopCount: 1
Pid: 13400 LoopCount: 2
Pid: 18972 LoopCount: 2
Pid: 18972 LoopCount: 3
```

### 进程等待
  
主进程要等子进程都执行结束后，再结束。可以使用join方法。  
但如果子进程陷入死循环，那么主进程则会一直等待，因此可以设置主进程等待子进程的最长时间。
```python
if __name__ == '__main__':
    processes = []
    for i in range(2, 5):
        p = MyProcess(i)
        processes.append(p)
        p.daemon = True
        p.start()
    for p in processes:
        p.join(1)
```
注：daemom表示是否将进程设置为守护进程，守护进程是指当在父进程结束后，子进程会被自动终止。  


### 终止进程
  
可以使用terminate方法来终止某个子进程。另外，可以通过is_alive方法判断进程是否还在运行。
  
### 进程互斥锁
  
可以避免多个进程抢占临界区资源。可以通过multiprocess的Lock来实现。  
在设计进程逻辑时，可以这样实现：  
```python  
    self.lock.acquire()
    ...    使用临界资源
    self.lock.release()
```
  
### 信号量
  
可以限制能访问的共享资源的进程的数量。它可以控制临界资源的数量，实现多进程同时访问共享资源，限制进程的并发量。  
可以使用multiprocess的Semaphore来实现。
  
### 进程池
  
可以提供指定数量的进程，供用户调用，当有新的请求提交到pool中时，如果pool是满的，请求就会等待，直到pool中有进程结束；如果pool中不满，就会创建一个新的进程来执行该请求。
```python
from multiprocessing import Pool
import time


def function(index):
    print(f'Start process: {index}')
    time.sleep(3)
    print(f'End process {index}', )


if __name__ == '__main__':
    pool = Pool(processes=3)
    for i in range(4):
        pool.apply_async(function, args=[i])

    print('Main Process started')
    pool.close()
    pool.join()
    print('Main Process ended')
```
该例子中pool大小为3，通过processes参数来指定。如果不指定，则会自动根据处理器内核来分配进程数。接着使用apply_async方法将进程添加进去，args可以用来传递参数。  
输出：
```
Main Process started  
Start process: 0  
Start process: 1  
Start process: 2  
End process 0  
Start process: 3  
End process 1  
End process 2  
End process 3  
Main Process ended
```  
  
上述例子可以使用map方法进行简化。  
map的第一个参数就是要启动的进程对应的执行方法，第二个参数是一个可迭代对象，其中的每个元素会被传递给这个执行方法。  
现在我们有一个 list，里面包含了很多 URL，另外我们也定义了一个方法用来抓取每个 URL 内容并解析，那么我们可以直接在 map 的第一个参数传入方法名，第 2 个参数传入 URL 数组。
```python
from multiprocessing import Pool
import urllib.request
import urllib.error


def scrape(url):
    try:
        urllib.request.urlopen(url)
        print(f'URL {url} Scraped')
    except (urllib.error.HTTPError, urllib.error.URLError):
        print(f'URL {url} not Scraped')



if __name__ == '__main__':
    pool = Pool(processes=3)
    urls = [
        'https://www.baidu.com',
        'http://www.meituan.com/',
        'http://blog.csdn.net/',
        'http://xxxyxxx.net'
    ]
    pool.map(scrape, urls)
    pool.close()

```  
定义了一个scrape方法，可以接手一个url，这里就是请求了一下这个链接，然后输出爬取成功的信息，如果发生错误，则会输出爬取失败的信息。
初始化一个进程数为3的pool，然后调用map方法。
结果如下：
```python
URL https://www.baidu.com Scraped  
URL http://xxxyxxx.net not Scraped  
URL http://www.meituan.com/ Scraped  
URL http://blog.csdn.net/ Scraped  
```
