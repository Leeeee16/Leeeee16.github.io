# ASP.NET Core 随笔


## Singleton、Transient和Scoped
  
1. Singlenton：在整个web项目的生命周期里，只会出现一个web实例。  
2. Transient: 每次有其他的类、其他的方法请求服务的时候，他都会建立一个新的实例。
3. Scoped: 每次HTTP请求，会生成一个实例。在web请求期间，如果多次请求，他还是使用同一个service。
  
注册服务：在Startup.cs中的ConfigureServices方法中。  
`services.AddScoped<IxxxService,xxxService>();`
  
## 管道和中间件
  
如何处理HTTP请求？  
中间件决定怎样处理请求，中间件可以有很多，每个中间件只负责一小部分内容。  
比如：一个请求*POST/Product*。
Logger中间件：记录各种请求的信息，记录之后传给下一个中间件。  
授权中间件：找一个特定的cookie，如果找到这个cookie，则允许这个请求继续前进~如果没找到，则返回登录页，或者异常页面。  
路由中间件：看请求的url，即**POST/Product。然后解析路由，找到可以响应这个请求的玩意儿，它可以响应请求，并返回一个JSON或者HTML。  
因为管道是双向设计的，响应就会原路返回。方向和请求进来的方向相反。  
路由->授权->Logger。  
其他中间件：静态文件、MVC等等。
  
## 页面传值
  
页面传值时，前段向后端传值时，有些不需要输入的字段，可以用input标签的hidden，定义一个隐藏域，将值传过去。
  
## 传图片
  
form标签中添加enctype="multipart/form-data"，表示编码类型可以为二进制，可以完整地传递文件数据，包括图片等等。
  
## 连接数据库字符编码相关
  
我写的时候用的EF core。在连接字符串中加入CharSet=utf8，用EF core自动生成数据库和表时，编码好像还是不对。所以在迁移之前，先手动创建数据库，设置编码为utf8，然后再migration自动创建表和字段。
