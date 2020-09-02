# Hugo博客搭建

  
[hugo github](https://github.com/gohugoio/hugo)  
[hugo官方themes](https://themes.gohugo.io/)  

# 1.安装hugo
- 在[hugo的github](https://github.com/gohugoio/hugo)
找到 *releases* 中最新版的 *zip* ，下载（很慢很慢。。。。）记得vpn。  
- 下载完后解压，将路径配置环境变量（放到path里）。
- 然后就ok了，在cmd里验证。  
`hugo veision`  
  
![](/images/hugoversion.png)

# 2.用hugo创建个人博客
`hugo new site myblog`

# 3.下载并设定主题
- 在[themes](https://themes.gohugo.io)中找一个主题。。
- 主题里一般有git命令，直接克隆到themes文件夹。 
- 然后需要配置myblog/config.toml  
- 将默认主题改为下载的主题 theme = "xxx"（主题的说明文档里一般有配置的说明）  
{{< admonition type=tip title="tip:" details=true >}}
如果clone github上的仓库很慢，可以先将github仓库导入码云，再从码云上clone到本地
{{< /admonition >}}

# 4.本地启动命令
`hugo server -t LoveIt --buildDrafts`  
- 访问：[http://localhost:1313/](http://localhost:1313/)

# 5.部署到github  
- 在github里新建一个仓库，仓库名和github的用户名一样  
`hugo --theme=LoveIt --baseUrl="https://xxx.github.io" --buildDrafts`  
（xxx为github用户名）  
- 然后会在myblog下生成一个public文件夹  
- 最后只要将public文件夹push到github上（git init...add...commit...push!）



