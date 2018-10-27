# 打war包

ide打出war包后，

通过FileZilla上传到服务器

移动到tomcat下的webapp下，

即可访问

## 如果mysql可远程访问情况下，连接不到数据库

看下jdbc四大参数配置，都正确的话，看下，**<font color=red>useSSL=false</font>**

