# 一些Requests

  
  
## 基本用法

### 安装
  
`pip install requests`
  
### 基本实例
  
```python
import requests
r = requests.get('https://lele16.me')
print(r.text)
```
  
输出结果为网页的html源代码。
  
### GET请求
实例网站的URL为 [http://httpbin.org/get](http://httpbin.org/get),客户端发起GET请求，该网站会返回相应的信息。
  
```python
import requests
r = requests.get('http://httpbin.org/get')
print(r.text)
```
  
输出结果如下：
```
{
  "args": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.23.0", 
    "X-Amzn-Trace-Id": "Root=1-5e91861e-c034a92c0e69b7ca80598480"
  }, 
  "origin": "139.180.133.191", 
  "url": "http://httpbin.org/get"
}
```
  
GET请求中，我们的URL后可以带有参数，比如[http://httpbin.org/get?name=germey&age=25](http://httpbin.org/get?name=germey&age=25)。我们可以这样传参：
  
```python
import requests

data = {
    'name': 'lele',
    'age': '21'
}
r = requests.get('http://httpbin.org/get', params=data)
print(r.text)
```
  
输出如下：
```
{
  "args": {
    "age": "21", 
    "name": "lele"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.23.0", 
    "X-Amzn-Trace-Id": "Root=1-5e9186ff-8284f4a1c8d000e00d060626"
  }, 
  "origin": "139.180.133.191", 
  "url": "http://httpbin.org/get?name=lele&age=21"
}
```
  
网页的返回类型为str，如果想得到JSON格式的数据，可以调用json方法。
  
```
print(type(r.text))
print(r.json())
print(type(r.json()))
```
结果如下：
```
<class 'str'>
{'args': {'age': '21', 'name': 'lele'}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.23.0', 'X-Amzn-Trace-Id': 'Root=1-5e91878f-d04fe06dd8954b89ed601549'}, 'origin': '139.180.133.191', 'url': 'http://httpbin.org/get?name=lele&age=21'}
<class 'dict'>
```
  
### 抓取二进制数据
  
网页中的图片、音频、视频等文件，其实都是二进制码。我们可以这样抓取。
  
```python
import requests

r = requests.get('https://lele16.me/favicon.ico')
print(r.content)
```
  
输出如下：
```
b'\x00\x00\x01\x00\x01\x00  \x00\x00\x01\x00 \x00\xa8\x10\x00\x00\x16\......
```
  
b表示bytes类型。如果要将图保存，可以：
```python
with open('favicon.ico', 'wb') as f:
    f.write(r.content)
```
  
open方法第一个参数为文件名，第二个参数表示以二进制形式打开，可以向文件写入二进制数据。
  
### headers
  
发起HTTP请求时，会有Request Headers。可以直接配置headers参数就行了。
```python
...
headers = {
    'User-Agent': 'xxx;xxx....'
}
r = requests.get('https//lele16.me', headers=headers)
...
```
  
### POST请求
```python
import requests

data = {'name': 'lele', 'age': '21'}
r=requests.post("http://httpbin.org/post", data=data)
print(r.text)
```
结果如下：
  
```
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    "age": "21", 
    "name": "lele"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "16", 
    "Content-Type": "application/x-www-form-urlencoded", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.23.0", 
    "X-Amzn-Trace-Id": "Root=1-5e918c55-ca7e553841ce63a87c71e184"
  }, 
  "json": null, 
  "origin": "112.23.143.139", 
  "url": "http://httpbin.org/post"
}
```
   
form中就是提交的data。
  
### 响应
  
```python
import requests

r = requests.get('https://lele16.me/')
print(type(r.status_code), r.status_code)
print(type(r.headers), r.headers)
print(type(r.cookies), r.cookies)
print(type(r.url), r.url)
print(type(r.history), r.headers)
```
结果如下：
```
<class 'int'> 200
<class 'requests.structures.CaseInsensitiveDict'> {'Connection': 'keep-alive', 'Content-Length': '3509', 'Server': 'GitHub.com', 'Content-Type': 'text/html; charset=utf-8', 'Last-Modified': 'Tue, 07 Apr 2020 14:51:55 GMT', 'ETag': 'W/"5e8c938b-2fe0"', 'Access-Control-Allow-Origin': '*', 'Expires': 'Sat, 11 Apr 2020 09:35:52 GMT', 'Cache-Control': 'max-age=600', 'Content-Encoding': 'gzip', 'X-Proxy-Cache': 'MISS', 'X-GitHub-Request-Id': '3EE2:3EAC:135C99:18FD95:5E918D1F', 'Accept-Ranges': 'bytes', 'Date': 'Sat, 11 Apr 2020 09:25:52 GMT', 'Via': '1.1 varnish', 'Age': '0', 'X-Served-By': 'cache-hkg17933-HKG', 'X-Cache': 'MISS', 'X-Cache-Hits': '0', 'X-Timer': 'S1586597152.951829,VS0,VE214', 'Vary': 'Accept-Encoding', 'X-Fastly-Request-ID': '466a0ffc086b0b08112f54d6e26d6f870cbe6652'}
<class 'requests.cookies.RequestsCookieJar'> <RequestsCookieJar[]>
<class 'str'> https://lele16.me/
<class 'list'> []
```
  
headers和cookies的类型分别为CaseInsensitiveDict和RequestsCookieJar。
  
状态码200表示成功响应。requests还提供了内置的状态码查询对象requests.codes。示例：
```python
...
exit() if not r.status_code == requests.codes.ok else print('Request Successfully)
...
```
![](/images/requests_code.png)
  
## 高级用法
  
### 文件上传
   
```python
...
files = {'file': open('favicon.ico', 'rb')}
r = requests.post('http://httpbin.org/post', files=files)
...
```
  
响应中会包含一个files字段，form是空的。所以文件上传会有一个单独的files字段来表示。
  
### Cookies
  
```python
import requests

r = requests.get('https://www.baidu.com')
print(r.cookies)
for key, value in r.cookies.items():
    print(key + '=' + value)
```
结果：
`<RequestsCookieJar[<Cookie BDORZ=27315 for .baidu.com/>]>
BDORZ=27315`
   
### Session维持
  
在 requests 中，如果直接利用 get 或 post 等方法的确可以做到模拟网页的请求，但是这实际上是相当于不同的 Session，相当于你用两个浏览器打开了不同的页面。
  
设想这样一个场景，第一个请求利用post方法登录了某个网站，第二次想获取成功登录后的自己的个人信息，你又用了一次get方法去请求个人信息页面。实际上，这相当于打开了两个浏览器，是两个完全不相关的Session，能成功获取个人信息吗？当然不能。
  
解决这个问题的主要方法就是维持同一个Session，相当于打开一个新的浏览器选项卡而不是新开一个浏览器。但我又不想每次设置Cookies，那该怎么办呢？这时候就有了新的利器——Session对象。示例如下：
```python
import requests

s = requests.Session()
s.get('http://httpbin.org/cookies/set/number/123456789')
r = s.get('http://httpbin.org/cookies')
print(r.text)
```
结果如下：
```
{
  "cookies": {
    "number": "123456789"
  }
}
```
  
### SSL证书验证
  
现在很多网站要求使用HTTPS协议，但是有些网站没有设置好HTTPS证书，或者因为其他原因，导致这些网站会出现SSL证书错误的提示。如Chrome会提示：您的连接不是私密连接。
  
如果我们用request请求这些网站会报SSLError错误。
  
我们可以使用verify参数来控制是否验证证书。
  
`response = requests.get('https://xxx', verify=False)`
  
这样可能在输出时会显示警告，我们可以如下设置来忽略警告。
`urllib3.disable_warnings()`或者 `logging.captureWarnings(True)`
  
### 超时设置
  
`r = requests.get('https://xxxx', timeout=1)`
  
这样就设置了超时时间为1s，如果1s没响应就会抛出异常。
  
实际上请求分连接和读取两个阶段。上面的1s表示连接和读取的总和。在设置timeout可以分别指定：
`r = requests.get('https://xxxx', timeout=(5, 30))`
永久等待可以设为None
  
### 身份验证
可以通过auth参数来设置。
`r = requests.get('https://xxxx', auth=('admin', 'admin')`
  
### 代理设置
  
为了防止网站直接封禁ip，导致一段时间无法访问，我们可以使用代理，使用proxies参数。
```python
import requests

proxies = {
    'http': 'http://6.6.6.6:9999',
    'https': 'https://6.6.6.6:9000'
}
requests.get('https://xxx', proxies=proxies)
```
（:point_up_2:上面代码里的ip瞎写的）

