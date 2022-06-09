+++
title = "Linux系统服务"
date = 2020-08-03

[taxonomies]
categories = ["安全"]
+++

常见Linux服务

SSH服务

查看ssh服务有没有开启：ps -e \|grep ssh

如果服务没有运行，通过命令 service ssh start、/etc/init.d/ssh start启动
<!-- more -->

如果没有安装，通过命令apt install openssh-server来安装

如果安装成功，服务也开启成功，通过在客户端输入 ssh <username@x.x.x.x>来登录

如果登录不成功，需要修改服务端ssh的配置

Vi /etc/ssh/sshd_config

将 \#permitRootLogin 取消掉注释，并设置为yes

完成之后重启ssh服务

/etc/init.d/ssh restart

然后再去使用客户端连接。





FTP服务

安装FTP服务（vsftpd）

apt install vsftpd

开启vsftpd服务

查看vsftpd服务有没有启动

在home路径下创建一个文件夹ftpfile，用来进行FTP的上传下载

Mkdir /home/ftpfile

创建好之后，修改文件夹权限为777

Chmod -R 777 /home/ftpfile

Ls -l查看权限

修改FTP配置

Vi /etc/vsftpd.conf，有些语句修改的时候只需要去掉注释

listen=YES

local_enable=YES 是否允许本地用户登录ftp

anonymous_enable=YES 匿名用户是否允许登录ftp

chroot_local_user=YES

local_root=/home/ftpfile/

write_enable=YES

local_umask=022

anon_upload_enable=YES

anon_mkdir_write_enable=YES

local_root=DIR 本地用户访问时默认工作目录

allow_writeable_chroot=YES

注释掉 listen_ipv6

重启服务进行登录

可以测试一下，使用匿名用户登录





apache服务：

最流行的web服务端的软件之一，适用于中小型网站，开源的网页服务。

安装：apt install apache2

查看服务状态：/etc/init.d/apache2 status

启动服务：/etc/init.d/apache2 start

查看进程：ps -e | grep apache2

测试：浏览器输入kali的IP，如果能看到apache2的页面，就说明安装成功

（因为之前用过浏览器的代理，所以需要先关闭代理）

apache2的安装路径在 /etc/apache2

apache2.conf：全局配置文件

envvars：存放环境变量（一般不需要修改）

ports.conf：HTTP服务的端口

httpd.conf：用户的配置文件

默认情况下，apache会把网站的文件存放在/etc/www目录下

需要将html的文档放在/var/www/html/，然后通过浏览器访问

如果没有test.html，就创建一个，并添加下面内容



nginx服务：开源的支持高性能高并发的www服务器

安装、开启服务、测试步骤同上

需要注意一点，因为之前安装了apache2，所以在/var/www/html目录下已经生成了一个apache2的index.html文件，nginx会调用apache2的index.html文件，显示的是apache2的测试页，如果要查看nginx的测试页，浏览器里输入：IP/index.nginx-debian.html

安装好nginx后，我们的前端文件要放在/var/www/html目录下，nginx的所有配置文件都在/etc/nginx下，程序文件在/usr/sbin/nginx，日志文件放在/var/log/nginx下



tomcat服务：免费开源的web应用服务，中小型网站

要使用tomcat要使用java环境，一般情况下kali自带java环境，通过命令java -version来查看

tomcat的安装：

先在官网下载后缀为tar.gz的安装包，之后在kali的/usr路径下创建文件夹tomcat，然后把安装包移动到/usr/tomcat下，然后修改tomcat文件夹的权限，然后进入目录/usr/tomcat/，解压安装包tar -zxvf + 安装包，解压之后就会出现一个arache-tomcat-8.5.57的目录，进入该目录，会有一个bin文件夹，其中有startup.sh的文件，编辑这个文件，在最后一行加入：

#set java environment
export JAVA_HOME=/usr/local/lib/jdk1.8.0_231
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

#tomcat
export TOMCAT_HOME=/usr/tomcat/apache-tomcat-8.5.54

注意jdk的版本要和自己的电脑匹配

最后运行startup.sh，命令是sudo ./startup.ssh

测试：在浏览器中，输入http://IP:8080

关闭tomcat，需要先在文件shutdown.sh对应位置，添加信息：

#set java environment
export JAVA_HOME=/usr/local/lib/jdk1.8.0_231
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

#tomcat
export TOMCAT_HOME=/usr/tomcat/apache-tomcat-8.5.54

注：vsftpd的配置文件/etc/vsftpd.conf中"listen=YES"和"listen_ipv6=YES"是不能同时开启的。若要求同时支持ipv4和ipv6，只需要保留listen_ipv6即可。

然后执行命令sudo ./shutdown.sh

如果要打开html页面，要在webapps目录下创建一个目录（这个目录可以理解成一个网站的项目），然后再把test.html放进目录中，最后在浏览器中输入：
http://IP:8080/test/test.html





MySQL服务：是最流行的关系型数据库管理系统，再web方面mysql是最好的应用软件之一。

特点：

开源

支持大型的数据库，可以处理上千万条记录的数据库

使用的是标准的SQL数据语言

跨平台支持，可以定制

MySQL安装：

在kali中，数据库使用的是mariaDB，其属于MySQL的一个分支

apt install mariadb-server

启动mariadb服务：/etc/init.d/mysql start

查看是否启动成功：netstat -tap | grep mysql  ，如果是listen状态，那就说明启动成功

接下来需要连接数据库：mysql -u root -p

因为安装过程中没有设置密码，所以密码默认为空







Linux安全配置

1. 禁用用户和组

   当某些用户或者组不使用的时候，可以直接在passwd或者group文件中注释掉

   /etc/passwd            /etc/group

2. 禁用某些服务

   service xxx stop         /etc/init.d/xxx stop

3. iptables设置(kali不可以直接使用iptables)

   可以当作Linux系统的防火墙，管理员可以通过设置规则，对进出iptables的数据包进行过滤（放行、拒绝、丢弃）

   先查看系统是否安装防火墙

   which iptables

   whereis iptables

   如果没有的话需要安装：sudo apt-get install ufw

   开启iptables：ufw enable

   关闭iptables：ufw disable

   

   配置防火墙策略：
   先打开kali 的apache2或者nginx服务，然后再本地使用浏览器访问web服务器，如果能访问成功，接下来再kali的ufw添加策略，拒绝掉http的流量。如果访问不成功，可能是ufw影响，所以先关闭ufw，则能正常访问，然后再开启ufw，设置http的放行策略。

   拒绝http流量，通过ufw deny http       ufw delete allow http

   放行http流量，通过ufw allow http

   

   ufw deny proto tcp from 源 to 目的 port 80

   ufw deny proto tcp from any to IP port 80 

   

   重置所有规则

   ufw reset




4. 登录设置

   登录超时设置

   用户在5分钟内无操作则超时端口连接，需要在/etc/profile添加

   export TMOUT=300

   readonly TMOUT



​	   禁止root直接远程登录

​		vim ./etc/ssh/sshd_config

​		permitRootLogin no



​		限制登录失败次数并锁定

​		在/etc/pam.d/login后添加

​		Auth required pam_tally2.so deny=6 unlock_time=180 even_deny_root 

​		root_unlock_time=180



​		登录IP限制

​		要与某一段固定IP或IP段绑定，更严格的限制容许SSH的用户和来源IP

​		Vim /etc/ssh/sshd.config

​		## allowed ssh users sysmgr

​		AllowUsers sysmgr@172.29.73.*

​		DenyUsers [sysmgr@172.29.73.*](mailto:sysmgr@172.29.73.*)



​		使用的密钥文件登录

​		极大提高安全性

 

​		减少history命令记录

​		

5. 防止一般网络攻击

    禁ping

    防止IP欺骗

    防止DOS攻击

6. 修复已知安全漏洞

7. 定期日志安全检查