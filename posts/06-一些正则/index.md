# 06-一些正则

  
### 常用的匹配规则
  
[菜鸟教程-正则](https://www.runoob.com/regexp/regexp-syntax.html)

### match
  
传入正则表达式，以及字符串，就能返回是否匹配。如果匹配，就返回匹配成功的结果；不匹配，就返回None。注意：match方法是从字符串的开头开始匹配的！！
  
用match方法得到了匹配到的字符串内容，如果我们要从匹配到的字符串中提取一些内容，可以用()将要提取的部分括起来。然后调用group方法传入分组的索引就可以了。示例如下：
  
```python
import re

content = 'Hello 1234567 World_This is a Regex Demo'
print(len(content))
result = re.match('^Hello\s(\d+)\sWorld', content)
print(result)
print(result.group(1))
print(result.span())
```
结果如下：
```
40
<re.Match object; span=(0, 19), match='Hello 1234567 World'>
1234567
(0, 19)
```
  
### 通用匹配
  
.*可以表示通用匹配。其中.可以匹配任意字符（除了换行符）， *代表匹配前面的字符无数次。示例如下：
  
```python
import re

content = 'Hello 1234567 World_This is a Regex Demo'
print(len(content))
result = re.match('^Hello.*Demo$', content)
print(result)
print(result.group())
print(result.span())
```
结果如下：
```
40
<re.Match object; span=(0, 40), match='Hello 1234567 World_This is a Regex Demo'>
Hello 1234567 World_This is a Regex Demo
(0, 40)
```
   
### 贪婪与非贪婪
  
```python
import re

content = 'Hello 1234567 qwertyuiop haha'
print(len(content))
result = re.match('^Hello.*(\d+).*haha$', content)
print(result)
print(result.group(1))
```
上面例子的结果如下：
```
29
<re.Match object; span=(0, 29), match='Hello 1234567 qwertyuiop haha'>
7
```
只得到了数字7。上面其实是贪婪匹配，.\*会匹配尽量多的字符，因此将13456都匹配了，所以(\d+)最后只匹配到一个7。非贪婪匹配应该使用.\*?。示例如下：
```python
import re

content = 'Hello 1234567 qwertyuiop haha'
print(len(content))
result = re.match('^Hello.*?(\d+).*haha$', content)
print(result)
print(result.group(1))
```
结果如下：
```
29
<re.Match object; span=(0, 29), match='Hello 1234567 qwertyuiop haha'>
1234567
```
  
### 修饰符
  
如果字符串中有换行符，使用.\*?匹配会出错,需要加一个修饰符re.S。
  
`result = re.match('^He.*?(\d+),*?Demo$', content, re.S)`
  
这样就能匹配字符串中的换行符了。
  
还有一些修饰符如表所示：
  
| <div style="width:50pt">修饰符 </div>| 描述 |
| :---:  | ---- |
| re.l | 使匹配对大小写不敏感 |
| re.L | 做本地化识别(locale-aware)匹配 |
| re.M | 多行匹配，影响^和$ |
| re.S | 使匹配包括换行在内的所有字符 |
| re.U | 根据Unicode字符集解析字符。影响\w,\W,\b,\B |
| re.X | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解 |
  

### 转义字符
  
和其他的差不多，就是在前面加 \ 就行。如：
`result = re.match('\(啊啊\)www\.baidu\.com', content)`
  
### search
  
match方法是从开头匹配的。search方法会扫描整个字符串，返回第一个成功匹配的结果。
  
### findall
  
search方法是返回第一个匹配到的内容。findall就是返回正则表达式匹配到的所有内容。感觉在匹配HTML时可以用到。返回结果是列表类型。
  
### sub
  
可以修改字符串的内容。第一个参数用来匹配原串，第二个字符是替换成的字符串，第三个参数是原字符串。示例如下：
  
```python
import re
content = '55adf2adf2a6d5f8a61daf16'
content = re.sub('\d+', '', content)
print(content)
```
结果如下：  
`adfadfadfadaf`去掉了原串的数字。
  
### compile
  
可以将正则字符串编译成正则表达式对象，以便在后面的匹配中复用。示例如下：
  
```python
import re

content1 = '2020年4月13日20:39:28'
content2 = '2020年4月14日20:39:43'
content3 = '2020年4月15日20:39:53'
pattern = re.compile('\d{2}:\d{2}:\d{2}')
result1 = re.sub(pattern, '', content1)
result2 = re.sub(pattern, '', content2)
result3 = re.sub(pattern, '', content3)
print(result1, result2, result3)
```
去掉了三个原串中的小时分钟秒，保留了日期。结果如下：  
`2020年4月13日 2020年4月14日 2020年4月15日`


  

