# Day 1
--
## 基础语法入门 (001.py)
***1) urlopen()***
表示向网站发起请求并获取响应对象，如下所示：
```py
urllib.request.urlopen(url,timeout)
urlopen() 有两个参数，说明如下：
url：表示要爬取数据的 url 地址。
timeout：设置等待超时时间，指定时间内未得到响应则抛出超时异常。
```
***2) Request()***
该方法用于创建请求对象、包装请求头，比如重构 User-Agent（即用户代理，指用户使用的浏览器）使程序更像人类的请求，而非机器。重构 User-Agent 是爬虫和反爬虫斗争的第一步。在下一节会做详细介绍。
```py
urllib.request.Request(url,headers)
```
参数说明如下：
url：请求的URL地址。
headers：重构请求头。
***3) html响应对象方法***
```py
bytes = response.read() # read()返回结果为 bytes 数据类型
string = response.read().decode() # decode()将字节串转换为 string 类型
url = response.geturl() # 返回响应对象的URL地址
code = response.getcode() # 返回请求时的HTTP响应码
```
***4) 编码解码操作***
```py
#字符串转换为字节码
string.encode("utf-8") 
#字节码转换为字符串
bytes.decode("utf-8") 
```
**代码实现演示**
```py
#导包,发起请求使用urllib库的request请求模块
import urllib.request
# urlopen()向URL发请求,返回响应对象,注意url必须完整
response=urllib.request.urlopen('http://www.baidu.com/')
print(response)
#提取响应内容
html = response.read().decode('utf-8')
#打印响应内容
print(html)
```
## User-Agent(002.py)
下面，通过向 HTTP 测试网站<http://httpbin.org/> 发送 GET 请求来查看请求头信息，从而获取爬虫程序的 UA。代码如下所示：
```py
#导入模块
import urllib.request
#向网站发送get请求
response=urllib.request.urlopen('http://httpbin.org/get')
html = response.read().decode()
print(html)
```

程序运行后，输出的请求头信息如下所示：
```py
{
  "args": {},
  #请求头信息
  "headers": {
    "Accept-Encoding": "identity",
    "Host": "httpbin.org",
    "User-Agent": "Python-urllib/3.7", #UserAgent信息包含在请求头中！
    "X-Amzn-Trace-Id": "Root=1-6034954b-1cb061183308ae920668ec4c"
  },
  "origin": "121.17.25.194",
  "url": "http://httpbin.org/get"
}
```
从输出结果可以看出，User-Agent 竟然是 Python-urllib/3.7，这显然是爬虫程序访问网站。因此就需要重构 User-Agent，将其伪装成“浏览器”访问网站。

**为什么和001.py一样的程序会出现你一样的结果结构呢？因为这个网站就是用来测试的捏**

*注意：<httpbin.org> 这个网站能测试 HTTP 请求和响应的各种信息，比如 cookie、IP、headers 和登录验证等，且支持 GET、POST 等多种方法，对 Web 开发和测试很有帮助。*
### user编辑
```py
from urllib import request
# 定义变量：URL 与 headers
url = 'http://httpbin.org/get' #向测试网站发送请求
#重构请求头，伪装成 Mac火狐浏览器访问，可以使用上表中任意浏览器的UA信息
headers = {
'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10.12; rv:65.0) Gecko/20100101 Firefox/65.0'}
# 1、创建请求对象，包装ua信息
req = request.Request(url=url,headers=headers)！！url=可以省略，其他要带着，龟腚吧
# 2、发送请求，获取响应对象
res = request.urlopen(req)
# 3、提取响应内容
html = res.read().decode('utf-8')
print(html)
```
## uers池

**构建代理池的方法也非常简单，在您的 Pycharm 工作目录中定义一个 ua_info.py~~ *003.py*~~  文件，并将以下 UA 信息以列表的形式粘贴到该文件中，如下所示：**
```py
ua_list = [
    'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Maxthon 2.0',
    'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_0) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11',
    'User-Agent:Opera/9.80 (Windows NT 6.1; U; en) Presto/2.8.131 Version/11.11',
    'Mozilla/5.0 (Windows NT 6.1; rv:2.0.1) Gecko/20100101 Firefox/4.0.1',
    'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0)',
    'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-us) AppleWebKit/534.50 (KHTML, like Gecko) Version/5.1 Safari/534.50',
    'Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0',
    ' Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1',
    'Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1',
    ' Mozilla/5.0 (Macintosh; Intel Mac OS X 10.6; rv:2.0.1) Gecko/20100101 Firefox/4.0.1',
]
```
模块随机获取UA
您也可以使用专门第三方的模块来随机获取浏览器 UA 信息，不过该模块需要单独安装，安装方式如下：
`pip install fake-useragent` *直接cmd输入即可*
***004.py***演示
```py
from fake_useragent import UserAgent
#实例化一个对象
ua=UserAgent()
#随机获取一个ie浏览器ua
print(ua.ie)
print(ua.ie)
#随机获取一个火狐浏览器ua
print(ua.firefox)
print(ua.firefox)
```
# Day2
## 编码与解码

***下表对 URL 中部分保留字符和不安全字符进行了说明：***

URL特殊字符编码
字符  含义  十六进制值编码
+ URL 中 + 号表示空格 %2B
空格  URL中的空格可以编码为 + 号或者 %20  %20
/ 分隔目录和子目录  %2F
? 分隔实际的 URL 和参数 %3F
% 指定特殊字符  %25
# 表示书签  %23
& URL 中指定的参数间的分隔符 %26
= URL 中指定参数的值 %3D
```
下面简单总结一下，哪些字符需要编码，分为以下三种情况：
ASCII 表中没有对应的可显示字符，例如，汉字。
不安全字符，包括：# ”% <> [] {} | \ ^ ` 。
部分保留字符，即 & / : ; = ? @ 。
```
***编码urlencode() ***

将https://www.baidu.com/s?wd=爬虫编码
006.py
```py
#导入parse模块
from urllib import parse
#构建查询字符串字典
query_string = {
'wd' : '爬虫'
}
#调用parse模块的urlencode()进行编码
result = parse.urlencode(query_string)
#使用format函数格式化字符串，拼接url地址
url = 'http://www.baidu.com/s?{}'.format(result)
print(url)
```
结果http://www.baidu.com/s?wd=%E7%88%AC%E8%99%AB
同样可以运行
除了使用 urlencode() 方法之外，也可以使用 quote(string) 方法实现编码，代码如下：
007.py
```py
from urllib import parse
#注意url的书写格式，和 urlencode存在不同
url = 'http://www.baidu.com/s?wd={}'
word = input('请输入要搜索的内容:')
#quote()只能对字符串进行编码
query_string = parse.quote(word)
print(url.format(query_string))
```
输出结果007.py

**注意：quote() 只能对字符串编码，而 urlencode() 可以直接对查询字符串字典进行编码。因此在定义 URL 时，需要注意两者之间的差异。方法如下：**

# urllib.parse
urllib.parse.urlencode({'key':'value'}) #字典
urllib.parse.quote(string) #字符串
***解码***
```py
from urllib import parse
string = '%E7%88%AC%E8%99%AB'
result = parse.unquote(string)
print(result)
```
输出008.py

***3) URL地址拼接方式***
```py
# 1、字符串相加
  baseurl = 'http://www.baidu.com/s?'
  params='wd=%E7%88%AC%E8%99%AB'
  url = baseurl + params
# 2、字符串格式化（占位符）
  params='wd=%E7%88%AC%E8%99%AB'
  url = 'http://www.baidu.com/s?%s'% params
# 3、format()方法
  url = 'http://www.baidu.com/s?{}'
  params='wd=%E7%88%AC%E8%99%AB'
  url = url.format(params)
```

## 以上知识点练习

***练习002源码及文件***
