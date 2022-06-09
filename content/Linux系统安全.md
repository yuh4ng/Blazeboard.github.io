+++
title = "Linux系统安全"
date = 2021-06-23

[taxonomies]
categories = ["安全"]
+++

# Linux系统安全知识体
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624467435499-cf4590b9-792e-4e9d-a80f-8ceb458d7755.png#align=left&display=inline&height=504&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1008&originWidth=1694&size=557128&status=done&style=none&width=847)
# 一·账户安全
在Linux系统中，提供了多种机制来确保用户账号的正确，安全使用。合理地规划用户账号，并合理地分配权限，是保证Linux系统安全的第一步。
<!-- more -->
## 1·账户的基本概念
### a·账户的相关概念

- 用户：用户是能够获取系统资源的权限的集合。
- 组：组是权限的容器。
- 用户和组的特性：
   - 用户必须属于某个组或多个组中。
   - 用户和组为多对多关系。
   - root用户为最高权限用户
- 用户的类型：
   - 超级用户：用户名为root或UID为0的用户，可以修改系统中所有的文件。
   - 系统用户：Linux系统正常工作所必须的内建的用户，不能用来登陆，如：bin, adm, lp等。
   - 普通用户：一般使用的用户，为了业务而生。
### b·账户的相关文件
![截屏2021-06-24 上午12.59.44.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624467688451-2d704116-0d70-4b2e-b994-5415858a8937.png#align=left&display=inline&height=321&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-24%20%E4%B8%8A%E5%8D%8812.59.44.png&originHeight=742&originWidth=640&size=235247&status=done&style=none&width=277)

- 用户信息文件：
   - /etc/passwd
   - /etc/shadow 
- 组信息文件：
   - /etc/group
   - /etc/gshadow
#### /etc/passwd
系统识别用户的文件，所有用户的信息都在文件中记录。
```
admin:x:500:500:admin ad:/home/admin:/bin/bash

第一字段：用户名（也被称为登录名）。
第二字段：口令；上例为x，实际上密码已经被映射到/etc/shadow文中。
第三字段：UID；用户ID。
第四字段：GID；用户组ID。
第五字段：用户名全称，可选，可以不设置。
第六字段：用户的家目录所在的位置。
第七字段：用户所用shell的类型。
```

#### /etc/shadow
passwd的影子文件，两文件互补
```
admin:$1$VE.Bq2Xf$2ccQi7EQ9dp8GKF8gH7PB1:13072:0:99999:7:::

第一字段：用户名（也被称为登录名）。
第二字段：密码（已被加密）。
第三字段：上次修改口令的时间（从1970年1月1日起的天数）。
第四字段：两次修改口令间隔最少的天数。
第五字段：两次修改口令间隔最多的天数。
第六字段：提前多少天警告用户口令将过期。
第七字段：在口令过期之后多少天禁用此用户。
第八字段：用户过期日期。
第九字段：保留字段，目前为空。
```

#### /etc/group
具有共同特性的用户集合起来就是用户组
```
root:x:0:root,linuxsir,baz

第一字段：用户组名称。
第二字段：用户组密码。
第三字段：GID。
第四字段：用户列表，每个用户之间用英文逗号分割。
```

#### /etc/gshadow
/etc/group的加密文件
```
groupname:password:admin,admin,...:member,member,...

第一字段：用户组。
第二字段：用户组密码。
第三字段：用户组管理者。
第四字段：用户组成员。
```

### c·账户认证方式
本地认证、远程认证。

- 用户名+密码（SSH、Telnet、FTP）。
- 基于证书。
- PAM（Pluggable Authentication Modules）

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624467766983-210233f5-7bc9-479a-a1ec-6254bc374f72.png#align=left&display=inline&height=254&margin=%5Bobject%20Object%5D&name=image.png&originHeight=508&originWidth=810&size=262688&status=done&style=none&width=405)
### d·账户访问文件权限分配
```
drwxr-xr-x 2 root root 637 Sep 19 03:23 config

d：文件类型：d文件夹，-文件。
rwx：文件拥有者的权限（U）。
r-x：文件拥有者所在组其他用户的权限（G）。
r-x：系统中其他用户的权限（O）。
2：链接数。
root：文件拥有者UID。
root：文件拥有者GID。
673：文件大小。
Sep 19 03:23：最后修改时间。
config：文件名。
```

#### 权限表示方式
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624467829710-9211d308-68bd-4e07-9ac4-52a37d54d2fa.png#align=left&display=inline&height=254&margin=%5Bobject%20Object%5D&name=image.png&originHeight=508&originWidth=1318&size=532634&status=done&style=none&width=659)<br />r、w、x分别表示读、写、执行。<br />r、w、x分别可以用4、2、1表示。<br />例如：
```bash
chmod 777 config    #给文件设置权限
chmod u=rwx config    #给文件设置权限
```

特殊权限：<br />SUID/SGID/SBIT<br />/usr/bin/passwd这个文件的权限状态是：“-rwsr-xr-x”
## 2·账户风险与安全策略
### a·身份鉴别
#### 特权用户的排查：
```bash
awk -F: '($3==0){print$1}' /etc/passwd
```

结果中不能出现root以外的UID为0的用户。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624467859366-ef649a28-830e-4f37-8c7e-1e7d7684017f.png#align=left&display=inline&height=57&margin=%5Bobject%20Object%5D&name=image.png&originHeight=114&originWidth=916&size=64258&status=done&style=none&width=458)<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624467877021-00313415-d9cb-4ceb-ac26-4f20c134b1f2.png#align=left&display=inline&height=245&margin=%5Bobject%20Object%5D&name=image.png&originHeight=490&originWidth=896&size=137764&status=done&style=none&width=448)
#### 账号密码生命周期
对于采用静态口令认证的服务器，账户密码生命周期应小于90，且拒绝弱口令以及空口令。
```bash
more /etc/login.defs    #检查相关参数
```

建议设置如下内容：
```
PASS_MAX_DAYS 90    #新建用户的密码最长使用天数
PASS_MIN_DAYS 0    #新建用户的密码最短使用天数
PASS_WARN_AGE 7    #新建用户的密码到期提前提醒天数
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624467916584-bdbee4b1-98a0-4c04-9329-f9862b477149.png#align=left&display=inline&height=155&margin=%5Bobject%20Object%5D&name=image.png&originHeight=310&originWidth=1146&size=59605&status=done&style=none&width=573)
#### 密码强度
对于采用静态口令认证技术的设备，口令长度至少8位，并包括数字、小写字母、大写字母和特殊符号4类中至少2类。<br />/etc/pam.d/system-auth文件中配置：
```
password requisite pam_cracklib.so difok=3 minlen=8 ucredit=-1 lcredit=-1 dcredit=-1
```

至少8位，包含一位大写字母，一位小写字母和一位数字
#### 用户锁定
对于采用静态口令认证技术的设备，应配置当用户连续认证失败次数超过10次，锁定该用户使用的账号。<br />设置连续输错10次密码，账号锁定5分钟，使用如下命令修改配置文件
```bash
vi /etc/pam.d/system-auth
```

添加
```
auth required pam_tally.so onerr=fail deny=10 unlock_time=300
```

注：解锁用户：
```bash
faillog -u <用户名> -r
```

### b·访问控制
#### umask安全配置
检查umask的值是否是027:
```bash
vi /etc/login.defs
```

默认情况下umask的值是022（能够用umask命令查看）。<br />此时建立的文件默认权限是644（6-0，6-2，6-2），建立的文件夹默认权限是755（7-0，7-2，7-2）。<br />022表示默认创建新文件夹权限为755 也就是 rxwr-xr-x（所有者全部权限，属组读写，其它人读写）<br />027表示默认创建新文件夹权限为750 也就是rxwr-x---(所有者全部权限，属组读写，其它人无)
#### 重要目录权限
执行以下命令检查目录和文件的权限设置情况：
```bash
ls -l /etc/
ls -l /etc/rc.d/init.d/
ls -l /tmp/
ls -l /etc/inetd.conf
ls -l /etc/passwd
ls -l /etc/shadow
ls -l /etc/group
ls -l /etc/security
ls -l /etc/sevices
ls -l /rc*.d
```

对于重要目录，建议执行如下操作：
```bash
chmod -R 750 /etc/rc.d/init.d/*
```

这样只有root可以读、写和执行这个目录下的脚本。
#### 查看未授权的SUID/SGID
SUID/SGID的程序在运行时，将有效用户ID改变为该程序的所有者（组）ID。因而可能存在一定的安全隐患，经常性的对比SUID/SGID文件列表，以便能够及时发现可疑的后门程序。<br />找出系统中所有含S位的程序，把不必要的S位去掉，或者把根本不用的直接删除，这样可以防止用户滥用及提升权限的可能性，命令如下：
```bash
FIND / -TYPE F PERM -4000 -LS    #查找SUID可执行程序
FIND / -TYPE F PERM -2000 -LS    #查找SGID程序
FIND / -TYPE F PERM -6000 -LS    #查找SUID/SGID/两个都有
```

### c·安全审计
#### -syslog登陆事件记录
必须要记录所有的登陆事件，执行 `vim /etc/rsyslog.conf` ，查看是否有以下配置
```bash
kern.warning;*.err;authpriv.none\t@loghost
*.info;mail.none;authpriv.none;cron.none\t@loghost
*.emerg\t@loghost
local7.*\t@loghost
```
#### -设置history时间戳
在问题定位时，我们有时需要查看history命令记录，看是否有删除/移动文件、修改配置等误操作 为history命令添上时间记录，可便于分析、定位问题。修改方法如下：
```bash
vim /etc/profile
export HISTTIMEFORMAT=“%F %T ‘whoami’”
执行source /etc/profile生效
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624468412637-4ccae778-f3a3-4948-8e4a-329319925159.png#align=left&display=inline&height=108&margin=%5Bobject%20Object%5D&name=image.png&originHeight=216&originWidth=1684&size=370091&status=done&style=none&width=842)
### d·资源控制
#### -登陆超时
使用命令“vi /etc/profile”修改配置文件，添加“TMOUT=”行开头的注释，<br />建议设置为“TMOUT=180”，即超时时间为3分钟。
#### -限制登录

- 禁止ROOT用户远程登陆
   - #CAT /ETC/SSH/SSHD_CONFIG
   - 确保PERMITROOTLOGIN为NO
- 限定信任主机
   - #CAT /ETC/HOSTS.EQUIV
   - #CAT /$HOME/.RHOSTS
   - 查看上述两个文件中的主机，删除其中不必要的主机，防止存在多余的信任主机
   - 或直接关闭所有R系列远程服务
      - RLOGIN
      - RSH
      - REXEC
- 禁用TELNET,使用SSH进行管理
   - 开启SSH服务：#SERVICE SSHD START
- 限制能够登录本机的IP地址
   - #VI /ETC/SSH/SSHD_CONFIG
   - 添加（或修改）：
   - ALLOWUSERS [MARGIN@10.1.1.3](mailto:MARGIN@10.1.1.3)
      - 允许用户MARGIN通过地址10.1.1.3来登录本机
   - ALLOWUSERS *@10.1.*.*
      - 仅允许10.1.0.0/16网段所有用户通过SSH访问。
#### -修改SSH端口

- 编辑/etc/ssh/sshd_config

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624469059169-91e3369d-a152-48d4-89b0-f4aa2a633468.png#align=left&display=inline&height=147&margin=%5Bobject%20Object%5D&name=image.png&originHeight=294&originWidth=916&size=229463&status=done&style=none&width=458)

- 修改banner信息
   - 修改sshd_config
      - /etc/ssh/sshd_config
      - 如存在，则将banner字段设置为NONE
   - 查看修改motd：
      - /etc/motd
      - 查看该文件内容，更新成自己想要添加的内容
### e·入侵防范

- Linux下的防火墙框架iptables
   - 包过滤
   - NAT
   - 数据包处理
- Iptables 语法
   - Iptables [-t table] command [match][target]
- Iptables 应用
   - iptables -I INPUT -s 192.168.1.0/24 -p tcp --dport 22 -j ACCEPT
# 二·文件系统安全
## 1·文件系统格式

- Linux文件系统

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624470033494-5c79344d-b0b1-4dd6-aaff-2c48510ab7c4.png#align=left&display=inline&height=279&margin=%5Bobject%20Object%5D&name=image.png&originHeight=558&originWidth=1224&size=227402&status=done&style=none&width=612)

- 文件系统类型
| **文件系统类型** | **文件系统说明** |
| --- | --- |
| Ext | 第一个专门针对liunx的文件系统 |
| Ext2 | 为解决ext文件系统缺陷设计的高性能、可扩展的文件系统 |
| Ext3 | 日志文件系统，ext2的升级版 |
| Ext4 | Ext4提供了更为可靠性的功能 |
| swap | Linux的交换分区 |
| NFS | Linux的网络文件系统 |
| smb | 支持smb协议的网络文件系统 |
| vfat | 与windows系统兼容的linux文件系统 |
| ntfs | windowsNT所采用的独特的文件系统结构 |
| proc | Linux操作系统中的一种基于内存的伪文件系统 |
| xfs | 由SGI开发的一个全64位、快速、安全的日志文件系统 |


## 2·安全访问和权限设置

- 文件和目录安全

确保重要目录的权限设置，限制用户访问

   - 排查任何人都有写权限的目录
```bash
find / -xdev -type f \( -perm -0002 -a ! -perm -1000 \) –print
find / -xdev -type d \( -perm -0002 -a ! -perm -1000 \) -print
```

   - 配置默认的umask值：027
   - 检查未授权的SGID/SUID
```bash
find / -type f -perm -6000 -ls
```

- 文件目录权限

目录权限设置：chmod [-cfvR] filename<br />DEMO：chmod 750 /home/margin/config<br />更改所有者和所有组：chown [-cfhvR] user[:group] filename<br />DEMO：chown margin:margingroup /home/margin/config
# 三·日志分析
## 1·系统日志的分类-Linux日志

- 日志文件系统已经成为linux系统不可缺失的部分。对于日常服务器的运行状态是否正常、遭受攻

击时如何查找被攻击的痕迹等情况，linux日志系统都提供了相应的解决方案。

- 日志系统可以记录当前系统中发生的各种事件，比如登录日志记录每次登录的来源和时间、系统 每次启动 和关闭的情况、系统错误等。

日志的主要用途:

1. 系统审计：记录登录系统的用户和日常行为
1. 监测追踪：系统遭受到攻击时如何追踪溯源到攻击者
1. 分析统计：系统的性能、错误等统计
- /var/log/messages
   - 这是Linux最核心的日志文件，假若某个服务没有定义日志文件，那么该服务产生的日志文件就会记录到这个文件中， 该日志每周归档一次，默认只保留5次。归档的方法都是由“/etc/logrotate.conf”这个文件来控制的
- /var/log/wtmp
   - 查看用户的登录，注销信息，同时记录系统的启动，重启，关机等事件。不用能cat直接查看，必须用last查看
- /var/log/btmp
   - 和wtmp类似，也不能用cat直接查看，用lastb查看，记录用户登录无效的历史
- /var/log/maillog
   - 用来记录邮件相关的日志，比如发给哪个，是否发出去
- /var/log/cron
   - 记录了系统定时任务相关的日志
- /var/log/cpus/
   - 记录了cpu信息的日志
- /var/log/lastlog
   - 记录所有用户的最后一次登录时间，只可能通过lastlog命令查看
- /var/log/sudo.log
   - 记录使用sudo发出的命令，需要先配置/etc/sudoers 的 logfile=/var/log/sudo.log
- /var/log/maillog
   - 用来记录邮件相关的日志，比如发给哪个，是否发出去
## 2·日志文件管理

- 日志文件中的信息可能比较繁琐，但有时特别重要。比如，硬件故障，错误配置，网络入侵等 情况发生时，通过查看日志文件可以很轻松地定位问题所在。
- 你必须经常清空日志文件，去掉旧信息，以免磁盘占满。正确管理日志文件，可使我们需要日志文件时能够访问到。清空日志文件时另作备分可使你获得更多审计信息。
- 这些管理工作看起来很枯燥无味， logrotate可以帮助我们。
## 3·日志分析工具

- Logrotate
   - logrotate 是操作系统用来管理日志的工具，支持日志的切割、压缩、清理以及邮件报警等，通过 crontab 服务定时运行，也可以用这个工具来管理我们自己的服务日志，只需要简单配置下 配置文件即可
- Logrotate使用方法
   1. 默认配置文件路径：/etc/logrotate.conf（也可自定义配置文件，启动时加载即可）
   1. 单次执行测试：logrotate -f /etc/logrotate.conf
   1. 定时执行：/etc/crontab */5 * * * * root /sbin/logrotate /etc/logrotate.conf 5分钟执行一次
- Logrotate配置
   - logrotate的配置可分为系统默认配置文件和自定义配置文件，默认配置文件的路径： /etc/logrotate.conf  如果要自定义，可以将配置文件写到/etc/logrotate.d/中，/etc/logrotate.conf会引入。
- Logrotate配置参数

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624471408443-0fea71ec-ede1-4174-86e1-e78cb24e3b22.png#align=left&display=inline&height=183&margin=%5Bobject%20Object%5D&name=image.png&originHeight=366&originWidth=1104&size=142243&status=done&style=none&width=552)

rotate:保留多少个历史文件 Weekly:每周执行  Missingok:忽略文件缺失信息<br />notifempty:如果文件为空，不切分文件<br />delaycompress:转存的文件下一次转存时压缩<br />sharedscripts:postrotate脚本只执行一次<br />postrotate/endscript:切割后执行的命令
## 4·日志格式
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624471580433-27dd120f-b443-4555-85d6-b41f303249e1.png#align=left&display=inline&height=106&margin=%5Bobject%20Object%5D&name=image.png&originHeight=212&originWidth=2011&size=496701&status=done&style=none&width=1005.5)<br />对于文本类型的日志，每一行表示一个消息，由4部分的固定格式组成

1. 记录时间:表示消息日期和时间
1. 主机名:表示生成消息的服务器的名字
1. 生成消息的子系统的名字:来自进程标识进程，方括号中表示进程的PID
1. 消息:剩下的部分表示消息的内容
## 5·日志分析
![截屏2021-06-24 上午2.09.30.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624471866242-057c6ea0-7905-4c4c-90be-fbf403b26aaf.png#align=left&display=inline&height=101&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-24%20%E4%B8%8A%E5%8D%882.09.30.png&originHeight=188&originWidth=1574&size=102593&status=done&style=none&width=847)

- 查看系统中的用户：cat /etc/passwd | grep /bin/bash
- 登录用户和登录时候用的IP w
- 查询历史命令 history
- 分析登录IP，在wtmp中
- 分析历史命令，在history中
- 另外定时任务，sudo.log都要排查

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624471958804-9f688cb8-8b6b-41e0-85fa-06184daaadf2.png#align=left&display=inline&height=229&margin=%5Bobject%20Object%5D&name=image.png&originHeight=457&originWidth=1136&size=502075&status=done&style=none&width=568)
