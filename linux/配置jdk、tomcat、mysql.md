# 配置jdk和tomcat

## 配置jdk

1，下载安装java（这里使用的是yum软件包管理器，他会自动配置好环境变量）

yum –y install java

## 安装tomcat

2,安装tomcat和appache

此时tomcat在  usr/share/tomcat下

yum –y install tomcat tomcat-admin-webapps tomcat-docs-webapp tomcat-javadoc tomcat-webapps
 yum –y install httpd

3,配置腾讯云的安全组，如果已经配置成默认安全组放通全部端口，就不用更改了。如果仅仅放通一个端口，需要新建安全组放通8080端口和80端口，注意出站和入站都要配置。

4，CentOS的防火墙问题：可通过systemctl status firewalld.service 命令查看状态。为了测试方便可先关闭防火墙，systemctl stop firewalld.service。如果想在防火墙开启的状态下访问服务器，可用防火墙相关命令打开80和8080端口。

开启80端口：

firewall-cmd –-zone=public –-add-port=80/tcp –-permanent

开启8080端口：

firewall-cmd –-zone=public -–add-port=8080/tcp –-permanent

重启防火墙：

systemctl restart firewalld.servic

5，开启tomcat和apache服务

Systemctl start tomcat.service
 Systemctl start httpd

6,打开本地浏览器输入云服务器公网IP可打开apache的首页，输入IP:8080打开tomcat的首页。

## 安装sql（yum安装）

### 安装依赖

wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql57-community-release-el7-10.noarch.rpm

### 安装mysql

yum -y install mysql-community-server

### complete后，启动服务器

systemctl start  mysqld.service

### 查看mysql运行状态

systemctl status mysqld.service

### 查找root用户密码

grep "password" /var/log/mysqld.log

最后几位几位密码，记住它

### 进入数据库

mysql -uroot -p     # 回车后会提示输入密码

输入初始密码后，此时不能做任何事情，需要修改密码

### 修改密码

alter user 'root'@'localhost' IDENTIFIED BY 'new password';

### 注意（设置密码问题）

show variables like 'validate_password%'; 

查看 密码规则

若密码设置失败，则修改

set global validate_password_policy=0;

set global validate_password_length=1;

### 重启mysql即可

systemctl restart mysqld

### 验证方式

如果navicat可以远程连接数据库，即成功！

mysql -h 111.111.111.11 -u root -p







1.yum –y install java

2.上传tomcat

3.注意 阿里云需要 配置8080端口