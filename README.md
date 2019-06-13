# 安装及配置说明

## 开发环境

### Web开发环境

* 编译环境：Intelij IDEA
* 操作系统：Windows 10

### 后台开发环境

- 编译环境：Intelij IDEA
- 操作系统：Windows 10
- jsp引擎：tomcat-9.0.10
- 云服务器：阿里云centOS7.3
- 数据库：MySQL-5.7
- API工具：swagger

## 数据库配置准备

* 打开Navicat，按如下操作新建数据库

  ![1560075297184](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1560075297184.png)

* 建立完数据库，按如下操作，运行附录中sql文件：

  ![1560075321696](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1560075321696.png)

  即可完成对数据库的配置工作。

## 项目配置过程

1. 解压bokeblog.rar

2. 打开intelij iead，导入项目，如下所示：

   ![](C:\Users\Administrator\Desktop\微信截图_20190609181016.png)

3. 选择解压后文件夹后，即可完成项目的导入；

4. 启动tomcat即可访问此项目（注意：需要先配置好jdk和tomcat）；
   启动tomcat，浏览器访问<http://localhost:8080/login.jsp>，发现成功进入博客登录界面，如下所示：

   ![1560075164210](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1560075164210.png)

5. 此时可输入管理员账密（测试账号，用户名：123，密码：123），即可访问后台管理系统主页：

   ![1560075223432](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1560075223432.png)

   



