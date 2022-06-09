+++
title = "Shodan Hacking"
date = 2020-09-07

[taxonomies]
categories = ["安全"]
+++

# Shodan Hacking

Shodan是搜索联网设备的搜索引擎

搜索web服务器、防火墙、路由交换机、摄像头、打印机等

支持chrome和Firefox插件

支持命令行

pip3 install shodan
<!-- more -->





Shodan采集的基本数据单位是banner

banner是描述设备所运行服务的标志性文本信息。banner的内容因服务类型的不同而不同。



除了banner信息外，Shodan还可以搜索设备元数据（地理位置、主机名、操作系统等）。此外，Shodan现在也开始搜集ipv6有关的在线设备了。



Shodan的爬虫是全天候工作的，并且分布于世界各地。



### 应用场景

- 特定的网络设备
- 自己用0day批量攻击
- 拥有特定数据特征的后门







### 基本使用

`country:`country:US 基于国家搜索

​                                 基于服务搜索

​                                 基于服务/协议搜索

​                                 基于设备指纹搜索

`product`搜索特定的产品/服务

`version`搜索特定的版本号

`port`搜索特定的端口号

```
hostname：搜索指定的主机或域名，例如 hostname:"google"
port：搜索指定的端口或服务，例如 port:"21"
country：搜索指定的国家，例如 country:"CN"
city：搜索指定的城市，例如 city:"Hefei"
org：搜索指定的组织或公司，例如 org:"google"
isp：搜索指定的ISP供应商，例如 isp:"China Telecom"
product：搜索指定的操作系统/软件/平台，例如 product:"Apache httpd"
version：搜索指定的软件版本，例如 version:"1.6.2"
geo：搜索指定的地理位置，参数为经纬度，例如 geo:"31.8639, 117.2808"
before/after：搜索指定收录时间前后的数据，格式为dd-mm-yy，例如 before:"11-11-15"
net：搜索指定的IP地址或子网，例如 net:"210.45.240.0/24"
category 现有的分类：ics, malware 例如 category:"malware"
os 操作系统 例如 os:"Windows 7 or 8"

country:CN product:"HUAWEI"
os:"windows" port:3389 net:"107.160.1.0/24"
```

【问】常见的物联网设备有哪些？

【问】这些物联网设备的默认密码是什么？