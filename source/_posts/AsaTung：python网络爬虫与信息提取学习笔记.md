---
title: AsaTung：python网络爬虫与信息提取学习笔记
date: 2019-04-18 18:48:25
tags: python
---
# 一. Requests库
## 1. requests库的安装： “以管理者身份”运行cmd，执行以下代码：

`python -m pip install requests`

## 2.HTTP协议

HTTP是一个基于“请求与响应”模式的无状态的应用层协议，采用URL作为定位网络资源的标识。URL是通过HTTP协议存取资源的Internet路径，一个URL对应一个数据资源。

HTTP协议可以对资源进行操作，有如下方法：

+ GET    -请求获取URL位置的资源
+ HEAD   -请求获取URL位置资源的响应消息报告，即获得该资源的头部信息
+ POST   -请求向URL位置的资源后附加新的数据
+ PUT    -请求向URL位置存储一个资源，覆盖原URL位置的资源
+ PATCH  -请求局部更新URL位置的资源，即改变该处资源的部分内容
+ DELETE -请求删除URL位置存储的资源

通过URL和命令管理资源，操作都是**独立无状态**。

## 3.Requests库的7个主要方法

### (1)requests.request(method, url, *kwargs*)

构造一个请求，此方法为requests库的基本方法，其余六种方法都以该方法支撑。

+ method: 请求方式，对应get/put/post等7种
+ url: 拟获取页面的url链接
+ *kwargs*: 控制访问的参数，为可选参数，共13个(可自行查看requests库文档)


### (2)requests.get(url, params=None, *kwargs*)

获取HTML网页的主要方法，对应于HTTP的GET。

+ url: 拟获取页面的url链接
+ params: url中的额外参数，字典或字节流格式，可选
+ *kwargs*: 12个控制访问的参数，为可选参数

基础代码为：

    import requests
    r = requests.get(url)

其中，url为一个Request对象，r为Response类的对象，包含爬虫返回的所有信息，也包含请求的Request信息。Response对象的属性：

#### <1> r.status_code
HTTP请求的返回状态，200则返回成功 
#### <2> r.text
url对应的页面信息（字符串格式）
#### <3> r.encoding
从HTTP header中猜测的响应内容编码方式
#### <4> r.apparent_encoding
从内容中分析出的响应内容编码方式
#### <5> r.content
HTTP响应内容的二进制形式

对于<3>和<4>两种编码，如果header中不存在charset，则认为编码为ISO-8859-1，r.text根据r.encoding显示网页内容，但往往显示的内容不是我们易懂的，所以通常执行以下代码：

    r.encoding = r.apparent_encoding
    r.text

这样，r.text就会返回我们更熟悉的内容

### (3)requests.head(url, ***kwargs***)

获取HTTP网页头信息的方法，对应于HTTP的HEAD。

+ url: 拟获取页面的url链接
+ *kwargs*: 12个控制访问的参数，为可选参数

### (4)requests.post(url, data=None, json=None, *kwargs*)

向HTML网页提交POST请求的方法，对应于HTTP的POST。

+ url: 拟获取页面的url链接
+ data: 字典、字节序列或文件，Request的内容
+ json: JSON格式的数据，Request的内容
+ *kwargs*: 12个控制访问的参数，为可选参数

### (5)requests.put(url, data=None, *kwargs*)

向HTML网页提交PUT请求的方法，对应于HTTP的PUT。

+ url: 拟获取页面的url链接
+ data: 字典、字节序列或文件，Request的内容
+ *kwargs*: 12个控制访问的参数，为可选参数

### (6)requests.patch(url, data=None, *kwargs*)

向HTML网页提交局部修改请求，对应于HTTP的PATCH。

+ url: 拟获取页面的url链接
+ data: 字典、字节序列或文件，Request的内容
+ *kwargs*: 12个控制访问的参数，为可选参数

### (7)requests.delete(url, ***kwargs***)

向HTML网页提交删除请求，对应于HTTP的DELETE。

+ url: 拟获取页面的url链接
+ *kwargs*: 12个控制访问的参数，为可选参数

## 4.爬取网页的通用代码框架
如果url请求连接失败，则会产生异常。

代码如下：

    def getHTMLText(url):
        try:
            r = requests.get(url)
            r.raise_for_status() #在方法内部判断r.status_code是否等于200
            r.encoding = r.apparent_encoding
        except:
            return "产生异常"


# 二. Robots协议
网络爬虫可能会引发许多问题，如性能骚扰、法律风险、隐私泄露等。因此，许多网站对网络爬虫有限制，有以下两种：

+ 来源审查：判断User-Agent进行限制

检查来访HTTP协议头的User-Agent域，只影响浏览器或友好爬虫的访问

+ 发布公告：Robots协议

告知所有爬虫的爬取策略，要求爬虫遵守

## Robots协议，Robots Exclusion Standard ，网络爬虫排除标准
作用：网站告知网络爬虫哪些页面可以抓取，哪些不行。形式：在网站根目录下的robots.txt文件。

Robots协议基本语法
    
    # 注释，* 代表所有， /代表根目录
    User-Agent: *
    Disallow: /

# 三. Beautiful Soup库

## 1. Beautiful Soup库的安装： “以管理者身份”运行cmd，执行以下代码：

`python -m pip install BeautifulSoup4`

## 2. Beautiful Soup的基本元素
HTML可表示为如下的标签树

    <html>

       <body>

          <p class="title">...</p>

       </body>

    </html>
beautifulSoup就是解析、遍历、维护“标签树”的功能库。
    
    import requests
    from bs4 import BeautifulSoup #B和S大写
    
    r = requests.get(url)
    demo = r.text

    soup = BeautifulSoup(demo, "html.parser")
    
以上代码得到一个BeautifulSoup类对象，存储在soup中，对应一个HTML/XML文档的全部内容

"html.parser"表示是bs4的HTML解析器，条件是安装bs4库，还有以下的解析器

+ lxml的HTML解析器，BeautifulSoup(mk, 'lxml'), 条件为pip install lxml
+ lxml的XML解析器，BeautifulSoup(mk, 'xml'), 条件为pip install lxml
+ html5lib解析器，BeautifulSoup(mk, 'html5lib'), 条件为pip install html5lib

BeautifulSoup类的基本元素

    <p class="title">...</p>
+ Tag, 标签，最基本的信息组织单元，分别用<>和</>表明开头和结尾 
+ Name, 标签的名字，&lt;p&gt;...&lt;/p&gt;的名字是'p', 格式：<tag>.name
+ Attributes, 标签的属性，字典形式组织，格式：<tag>.attr
+ NavigableString, 标签内非属性字符串，<>...</>中的字符串，格式：<tag>.string
+ Comment, 标签内字符串的注释部分，一种特殊的Comment类型

任何存在于HTML语法中的标签都可以用soup.<tag>访问获得，当HTML文档中存在多个相同<tag>对应内容时，soup.<tag>返回第一个。

比如title标签和a标签
   
    >>>soup.title
    >>>soup.a

每个<tag>都有自己的名字，通过<tag>.name获取，字符串类型

    >>>soup.a.name
    >>>soup.a.parent.name
    >>>soup.a.parent.parent.name

一个<tag>可以有0个或多个属性，字典类型

    >>>tag = soup.a
    >>>tag.attrs

NavigableString可以跨越多个层次，Comment是一种特殊类型。

    >>>newsoup = BeautifulSoup("<b><!--This is a comment--></b><p>This is not a comment</p>", "html.parser")
    >>>newsoup.b.string
    >>>type(newsoup.b.string)
    >>>newsoup.p.string
    >>>tyoe(newsoup.p.string)
    
输出结果为

    'This is a comment' #Comment类型的字符串开头有一个!
    <class 'bs4.element.Comment'>
    'This is not a comment'
    <class 'bs4.element.NavigableString'>






