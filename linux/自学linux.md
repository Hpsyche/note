# linux目录结构

* /:根目录，一般根目录下只存放目录，不要存放文件

  /etc,/bin,/dev,/lib,/sbin应该放在同一个分区中

* /bin:存放二进制可执行文件

* /sbin:存放二进制可执行文件，只有root能访问

* /etc:存放系统配置文件

* /usr:用于存放共享的系统资源

* /home:存放用户文件的根目录

* /root:超级用户目录

* /dev:用于存放设备文件

* /lib:存放根文件系统中的程序运行所需要的共享库及内核模块

* /mnt:系统管理员安装临时文件系统的安装点

* /boot:存放用于系统引导时使用的各种文件

* /tmp:用于存放各种临时文件

* /var:用于存放运行时需要改变数据的文件



# 命令

## 查看目录结构

ll:查看当前目录下的内容

ls -al:列出当前目录下的文件信息（包含隐藏文件，特殊目录）

ll /bin/:列出根目录下bin目录下的文件信息

## 切换目录命令cd

touch 1.txt：在当前目录创建一个文件1.txt

clear：清除屏幕

cd ~：当前用户目录

cd /：根目录

cd - ：上一次访问的目录

cd ..：上一级目录

cd ：却省当前用户目录

pwd:打印当前目录

## 创建目录

mkdir a:当前目录下创建文件夹a

mkdir /root/b:在根目录下的root下创建目录b

mkdir -pv /root/c/e/d:在根目录下的root下创建目录结构c/e/d

## 文件浏览命令

cat命令：如果文件中的内容很少，一页之内可以显示完毕

cat  /root/文件名称  

more命令: 如果文件中内容比较多,一页之内显示不完

more  文件名称

less 命令: 如果文件中内容比较多,一页之内显示不完

less -mN 文件名称

less比more多了搜索功能

tail 命令: 查看文件末尾内容

tail -10 /root/install.log     查看install.log文件中后10行内容

## 文件管理

**复制文件**

cp   install.log  install02.log

cp -b  install.log  install02.log  :如果覆盖文件时,可以将源文件做一个备份

cp  ./install.log   ../   : 将当前目录下的install.log复制到父级目录

cp  /root/install.log   /bin/1.txt  

   :将/root下的install.log文件复制到根目录下的bin目录下的1.txt中

 **复制目录**(无论是单层目录还是多层目录都可以复制):

cp  -r  ./a  ./b

cp  -r  /root/a  /root/z

**mv剪切文件**

mv  /root/install.log  /root/a/

<font color=red>mv**重命名文件**</font>

mv  /root/install.log  /root/test.log

**删除文件**

rm 文件名称（删除文件）

rm -f  文件名称   （强制删除文件，不提示是否确认删除）

rm -f  /root/文件名称

**删除目录**

rm -rf 目录名称

rm -rf  /root/目录名称

 **在某个目录下搜索文件/目录**

find  /root/   -name   ‘in*’  :

  在根目录下的root目录下寻找名称中包含in而且是以in开头的文件/目录

## Vim编辑器

vim /root/1.txt  :利用vim编辑器打开文件

 录入 i  (从一般模式转变为插入模式)

录入各种信息 

esc(从插入模式转换为一般模式)

:wq(保存并退出)

## 系统命令

grep: 过滤任务 经常和其他命令联合使用

grep -i 名称

cat  /root/install.log

cat  /root/install.log  |  grep  -i   mysql :查看install.log文件中包含mysql内容信息

ps -ef :相当于任务管理器,对进程的一次查看

ps -ef  | grep -i crond  : 查看系统中crond的进程信息

 

管道命令: 命令的一种使用方式

命令1  |  命令2

命令1的输出是命令2的输入

cat  /root/install.log  |  grep  -i   mysql 

ps -ef | grep -i crond

 

lee杀死进程  kill -9 进程id

查看IP: ifconfig

查看机器是否连接通畅:  ping 对方机器ip



## 解压缩命令

  tar  -zcvf  /root/xxx.tar   /root/a/

  tar  -zxvf  ./xxx.tar

关机/重启:   reboot  halt



## 更改目录/文件用户权限

chmod  u-rwx   ./1.txt   取消当前用户对1.txt的”读写执行”权限

chmod  g-rwx   ./1.txt   取消当前组用户对1.txt的”读写执行”权限

chmod  o-rwx   ./1.txt   取消其他用户对1.txt的”读写执行”权限

chmod  777  ./1.txt  设置当前用户,当前组用户以及其他所有用户对文件1.txt的权限

以数字形式更改目录/文件权限的规则如下:

0:不具有任何权限    1: 可以执行    2:可以写　３(1+2):可以执行,可以写

4:可以读  5:(1+4):可以执行,可以读   6:(2+4):可以写可以读 7:(1+2+4):可以读,写,执行



## 如何查看各个命令的参数

Linux命令

  内部命令:Linux系统核心命令 , 等价于windows<===>dir

  外部命令:后来加入的命令  , 等价于windows<===>java

如果查看内部命令帮助文档方式:  help + 命令

如果查看外部命令帮助文档方式:  man + 命令