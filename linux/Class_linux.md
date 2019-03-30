* root用户锁定用户user01后：

  passwd -l user01

  发现不可以连接进入user01,会报账密错误：
  passwd authentication failed.Please verify that the.....

  此时查看文件/etc/shadow：

  发现user01:!!$fifhi3423....，即前面多了两个“！！”，说明用户被锁定；

* 在root下解锁用户user01:

  passwd -u user01

  此时查看文件/etc/shadow

  发现user01:$fifhi3423....，说明用户状态正常；

* 将project目录的拥有者改为user02，组拥有者改为stuff，如何操作？

  chown user02 project
  chgrp stuff project

  