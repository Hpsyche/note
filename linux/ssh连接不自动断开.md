# 解决步骤

1.通过vim/vi进入sshd_config文件

vim /etc/ssh/sshd_config

2.找到这两行代码

\#ClientAliveInterval 0

\#ClientAliveCountMax 3

改成如下所示

ClientAliveInterval 30    #客户端每隔多少秒发送一次响应到服务器（s）

ClientAliveCountMax 120   #客户端未响应时间（s）

3.重启sshd服务

#service sshd restart