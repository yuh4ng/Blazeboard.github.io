+++
title = "Google Hacking"
date = 2020-09-04

[taxonomies]
categories = ["安全"]
+++

# Google Hacking

` +`相当于逻辑与

`|`相当于逻辑或
<!-- more -->

`-`相当于逻辑非（排除）

`*`通配符，匹配任意字符

`~`包含同义词或类似单词

`.`匹配单个字符进行查询

`“ ”`精准匹配双引号内容

`@`用于搜索社交媒体的字体前可以加上@

`..`在数字之间加上 .. 表示在数字范围内搜索



`site:`指定域名搜索相关内容

`intitle:`搜索标题中存在特定关键词的网页

`allintitle:`搜索标题中存在特定关键词的网页

`inurl:`搜索url中存在特定关键词的网页

`allinurl:`搜索url中存在特定关键词的网页

`intext:`搜索正文存在特定关键词的网页

`filetype:`指定文件类型

`link:`显示链接到指定URL的网页列表

`inanchor`搜索网站锚点（https://zhuanlan.zhihu.com/p/28679912、https://www.cnblogs.com/littlek1d/p/9548075.html）



【问】asp, jsp, php 是什么？



`related:`搜索与关键词相似的网站

`cache:`

`define:`





【综合应用】

搜索网站后台

搜索可遍历目录的网站文件

查找网站特殊目录





https://www.hacking8.com/uploadfile/wLHK-Discuz%E6%BC%8F%E6%B4%9E%E6%95%B4%E7%90%86.pdf

https://www.cvedetails.com/

https://packetstormsecurity.com/

http://www.jeepxie.net/article/165709.html





敏感信息（简历、邮箱、用户名、密码等）

敏感目录（后台地址、备份文件、日志文件等）

摄像头

包含漏洞的地址

使用特定软件特定版本的站点





事件性漏洞

通用性漏洞





如何防御：

- 禁止访问未授权的目录或文件
  - 给目录添加密码保护或者设置严格的读取权限
    - .htaccess可以用来防止目录的内容未授权的访问，但是不当的配置还会让这个文件可见甚至可读
    - 在Apache的服务器上可以通过httpd.conf文件中的单词indexs前添加一个连字符或者减号来禁止目录列表
- 添加robots.txt
- 添加NOARCHIVE
- 使用NOSNIPET
- 加密敏感信息
- 向Google申诉



【问】robots.txt的作用？

【问】与robots.txt有关的问题是什么？







```
常用 shodan 库函数 
shodan.Shodan(key) ：初始化连接API 
Shodan.count(query, facets=None)：返回查询结果数量 
Shodan.host(ip, history=False)：返回一个IP的详细信息 
Shodan.ports()：返回Shodan可查询的端口号 
Shodan.protocols()：返回Shodan可查询的协议 
Shodan.services()：返回Shodan可查询的服务 
Shodan.queries(page=1, sort='timestamp', order='desc')：查询其他用户分享的查询规则 
Shodan.scan(ips, force=False)：使用Shodan进行扫描，ips可以为字符或字典类型 
Shodan.search(query, page=1,limit=None, offset=None, facets=None, minify=True)：查询Shodan数据
```





另：zoomeye   https://www.zoomeye.org/doc?channel=api

​		FOFA      https://fofa.so/help	https://fofa.so/rules/library   https://fofa.so/library





```
网站模块
pagespeed， mod_bwlimited， mod_auth_passthrough， mod_auth_pam， mod_fastcgi， mod_jk， mod_perl， mod_python， mod_rack， mod_rails， mod_ssl， mod_wsgi， mod_antiloris，
广告联盟
doubleclick_ad， baidu广告联盟， Google_AdSense， Open_AdStream， adinfinity， adriver，
反向代理服务器
squid， kangle反向代理， Varnish， Aicache， SJSWPS_ OiWPS， HAProxy_Report，
脚本语言
PHP， ASP.NET， python， ruby， jsp， perl， nodejs， ASP，
前端库
jquery， bootstrap， d3， jquery-ui， yui， AlloyUI，
分享模块
jiathis， ujian， 百度分享， joomla-facebook， addthis， hellobar，
路由器
百为路由， 锐捷NBR路由器， mikrotik， 中兴路由器， h3c路由器， jcg无线路由器， D-Link_VoIP_Wireless_Router， arrisi_Touchstone， ZyXEL， Ruckus， Motorola_SBG900， Wimax_CPE， Cisco_Cable_Modem， Scientific-Atlanta_Cable_Modem， rap， ZTE_MiFi_UNE， DI-804HV， HuaweiHomeGateway， TP-LINK无线路由器，
云评论
uyan， disqus，
统计模块
google-analytics， cnzz， baidu统计， 51la， CNZZ统计， awstats_misc_tracker， advancedwebstats， hubspot， 51yes， Piwik，
开发框架
ThinkPHP， Swiftlet， Aspnetmvc， CodeIgniter， ColdFusion， WebObjects， cakephp， Django， zikula_framework， NetteFramework， Fat-FreeFramework， Play-Framework， Restlet-Framework， Kohana-Framework， flow_framework， SIMIT_framework， axis2-web，
其他
mongodb， elasticsearch， phpMyadmin， MVB2000， GPSweb， Tumblr， phpinfo， AD_RS设备， FrontPageServerExtension， 认证页面， TCN协议， sharepoint， MS-Author-Via， 3COM_NBX， wspx， rack-cache， Dnnoutputcache， Iisexport， Oraclea-DMS， P3p_enabled， X-72e-Nobeian-Transfer， ManagedFusion， Adblock， Blackboard， LiquidGIS， Cocoon， Wp-Super-Cache， pagespeed， Alternate-Protocol， unbouncepages， lemis管理系统， OpenSSL， mod_bwlimited， mod_auth_passthrough， FreeboxOS， SMA_Sunny_Webbox， Wimax_CPE， Aethra_Telecommunications_Operating_System， Scientific-Atlanta_Cable_Modem， rap， ZTE_MiFi_UNE， 用友商战实践平台， moosefs， AirLink_modem， CCProxy， APC_Management， HuaweiHomeGateway， TP-LINK无线路由器， 蓝盾BDWebGuard， 护卫神网站安全系统， phpDocumentor， ColdFusion， Adobe_ CQ5， Adobe_GoLive， Adobe_RoboHelp， Amaya， PHP-CGI， Synology_NAS， OpenMas， __VIEWSTATE， Chromelogger， Communique， recaptcha， IPFire， TerraMaster， 创星伟业校园网群， 正方教务管理系统， UFIDA_NC， 北创图书检索系统， Facebook_insights， sq
```

