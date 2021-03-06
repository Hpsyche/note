# 网络层提供的两种服务

（虚数）

## 虚电路服务

即面向连接的通信方式

![](D:\Work\TyporaNotes\note\计算机网络\pict\虚电路服务.png)

## 数据包服务

尽最大努力交付

![](D:\Work\TyporaNotes\note\计算机网络\pict\数据报服务.png)

网络层向上只提供简单灵活的、无连接的、尽最大努力交付的数据报服务。数据报即“分组”；

网络层不提高服务质量的承诺，若要可靠，由运输层负责

## 虚电路服务与数据报服务的对比

![](D:\Work\TyporaNotes\note\计算机网络\pict\虚电路服务与数据报服务的对比.png)



# 网际协议IP

与IP协议配套使用的还有三个协议：

* 地址解析协议ARP
* 网际控制报文协议ICMP
* 网际组管理协议IGMP

这一层中，ARP画在最下面，因为IP经常要使用这个协议。ICMP和IGMP画在这一层的上面，因为它们要使用IP协议。

![](D:\Work\TyporaNotes\note\计算机网络\pict\网际协议IP及其配套协议.png)

TCP/IP体系中的网络层常常被称为网际层或IP层

## 虚拟互连网络

没有一种单一的网络能够适应所有用户的需求

因此，将网络互连起来需要使用一些中间设备（中间系统、中继系统），如：

* <u>物理层使用的中间设备为：转发器</u>

* 数据链路层使用的中间设备为：网桥或桥接器（交换机）

* 网络层使用的中间设备为：路由器

* 在网络层以上使用的中间设备为：网关

* 网桥和路由器的混合物：桥路器（三层交换机）

由于参加互连的计算机网络都使用相同的网际协议IP，因此可以把互连以后的计算机网络看成一个虚拟互连网络，即逻辑互连网络，使这些性能各异的网络在网络层上看起来好像是一个统一的网络。

![](D:\Work\TyporaNotes\note\计算机网络\pict\互连网络.png)

![](D:\Work\TyporaNotes\note\计算机网络\pict\虚拟互连网络.png)

如下例，互联网中的源主机H1要把一个IP数据报发送给目的主机H2，有如下过程：

![](D:\Work\TyporaNotes\note\计算机网络\pict\分组在互联网中的传送.png)

图中的协议栈中的数字1-5分别表示物理层、数据链路层、网络层、运输层和运用层

## 分类的IP地址

### IP地址及其表示方法	

一个IP地址在整个互联网范围内是唯一的

可以记为：

​	IP地址 :: = {<网络号>,<主机号>}

:: = 表示 ”定义为“，包括A、B、C、D、E五大类地址，其中A、B、C类地址都是单播地址（一对一通信），是最常用的。

#### A类地址0

A类地址：第一个网络号：1，最后一个网络号：126 （最大主机数2^24-2）

![](D:\Work\TyporaNotes\note\计算机网络\pict\A类地址.png)

#### B类地址10

B类地址：第一个网络号：128.1，最后一个网络号：191.25（最大主机数2^16-2）

![](D:\Work\TyporaNotes\note\计算机网络\pict\B类地址.png)

#### C类地址110

C类地址：第一个网络号：192.0.1，最后一个网络号：223.255.255（最大主机数2^8-2）

![](D:\Work\TyporaNotes\note\计算机网络\pict\c类地址.png)

#### D类地址1110

D类地址是多播地址

![](D:\Work\TyporaNotes\note\计算机网络\pict\D类地址.png)

#### E类地址1111

E类地址保留为今后使用

![](D:\Work\TyporaNotes\note\计算机网络\pict\E类地址.png)

A、B、C类地址的网络号字段分别为1个、2个和3个字节长；

A、B、C类地址的主机号字段分别为3个、2个和1个字节长；

对32位二进制代码采用点分十进制来书写；

### 常用的三种类别的IP地址

#### A类地址

网络号为2^7-2，原因：网络号字段为全0的IP地址是个保留地址，意思是“本网络”；网络号为127（01111111）保留作为本地软件环回测试本主机的进程之间的通信之用。

全0的主机号字段表示该IP地址时“本主机”所连接到的单个网络地址，如一台主机的IP地址为5.6.7.8，则该主机的网络号为5.0.0.0；

全1的主机号字段表示该网络上的所有主机

例如，<u>B类地址128.7.255.255表示“在网络128.7.0.0上的所有主机”，而A类地址0.0.0.35则表示“在这个网络上主机号为35的主机”；</u>

#### B类地址

B类地址中128.0.0.0是不可以指派的，而可以指派的B类最小网络地址为128.1.0.0

#### C类地址

C类地址中192.0.0.0是不可以指派的，而可以指派的C类最小网络地址为192.0.1.0

![](D:\Work\TyporaNotes\note\计算机网络\pict\IP地址的使用范围.png)

用网桥（它只在链路层工作）互联的网段仍然是一个局域网，只能有一个网络号

路由器总是具有两个或两个以上的IP地址。即路由器的每一个接口都有一个不同网络号的IP地址

当两个路由器直接相连时（例如通过一条租用线路），在连线两端的接口处，可以分配也可以不分配IP地址，如指明 IP 地址，则这一段连线就构成了一种只包含一段线路的特殊“网络” 。现在常不指明 IP 地址。通常把这样的特殊网络叫做<u>无编号网络或无名网络</u>。

## IP地址与硬件地址

物理地址是数据链路层和物理层使用的地址，而IP地址是网络层和以上各层使用的地址，是一种逻辑地址。

使用IP地址的IP数据报一旦交给了数据链路层，就被封装成MAC帧了。

<center>从<font color=red>协议栈层次</font>看数据的流动</center>

![](D:\Work\TyporaNotes\note\计算机网络\pict\从协议栈层次看数据的流动.png)

<center>从<font color=red>虚拟的IP层</font>看IP数据的流动</center>

![](D:\Work\TyporaNotes\note\计算机网络\pict\从虚拟的IP层上看IP数据的流动.png)

<center>在<font color=red>链路上</font>看 MAC 帧的流动</center>

![](D:\Work\TyporaNotes\note\计算机网络\pict\从链路上看MAC帧的流动.png)

<center><font color=red>IP层抽象的互联网上只能看到IP数据报</center>

![](D:\Work\TyporaNotes\note\计算机网络\pict\IP层抽象的互联网上只能看到IP数据报.png)

注意：

* 在IP层抽象的互联网上只能看到IP数据报，图中的数据报写的“从IP1到IP2”就表示前者是源地址而后者是目的地址。数据报中间经过的两个路由器的IP地址并不能出现在IP数据报的首部中
* 路由器只根据目的站的IP地址的网络号进行路由选择
* 在局域网的链路层，只能看见MAC帧
* IP层抽象的互联网屏蔽了下层这些复杂的细节，只要我们在网络层上讨论问题，就能够使用统一的、抽象的IP地址研究主机和主机或路由器之间的通信。

## 地址解析协议ARP

ARP（Address Resolution Protocol）作用：找出某个IP地址对应的硬件地址

![](D:\Work\TyporaNotes\note\计算机网络\pict\ARP协议的作用.png)

由于是IP协议使用了ARP协议，因此通常把ARP协议划归于网络层，但也可以划分在数据链路层；

RARP：逆地址解析协议，通过硬件地址找到IP地址，但已经被现在的DHCP协议包含进了

每一个主机都设有一个ARP高速缓存，当主机A要向本局域网的某台主机B发送IP数据包时：

* 先在其ARP高速缓存中查看有无主机B的IP地址

* 如有，就在ARP高速缓存中查出其对应的硬件地址，再把这个硬件地址写入MAC帧，然后通过局域网把该MAC帧发往此硬件地址

* 如没有

  * ARP进程在本局域网广播发送一个ARP请求分组

  * 在本局域网上的所有主机上运行的ARP进程都收到此ARP请求分组

  * 主机B的IP地址与ARP请求分组中要查询的IP地址相一致，就收入这个ARP请求分组，并向主机A发送ARP响应分组，同时在这个ARP响应分组中写入自己的硬件地址。

    注意：虽然ARP请求分组是广播发送的，但ARP响应分组是普通的单播，即从一个源地址发送到一个目的地址

  * 主机A收到主机B的ARP响应分组后，就在其ARP高速缓存中写入主机B的IP地址到硬件地址的映射

![](D:\Work\TyporaNotes\note\计算机网络\pict\地址解析协议ARP的工作原理.png)

*注意：ARP是解决<font color=red>同一个局域网上</font>的主机或路由器的IP地址或硬件地址的映射问题*

使用ARP的四种典型情况：

1. 发送方是主机，要把IP数据报发送到同一个网络上的另一台主机，这时H1发送ARP请求分组，找到目的主机H2的硬件地址
2. 发送方是主机，要把IP数据报发送到另一个网络上的一台主机，这时H1发送ARP请求分组，找到网1上的一个路由器R1的硬件地址。剩下的工作由路由器R1来完成以下的3和4
3. 发送方是路由器，要把IP数据报转发到与R1连接在同一个网络上的主机，这时路由器发送ARP请求分组，找到目的主机H3的硬件地址
4. 发送方是路由器，要把IP数据报转发到另一个网络上的一台主机。这时路由器发送ARP请求分组，找到连接在网2上的一个路由器R2的硬件地址。剩下的工作由这个路由器R2来完成

### 为什么我们还要用抽象的IP地址，不直接使用硬件地址进行通信？

由于全世界存在着各式各样的网络，它们使用不同的硬件地址，要使这些异构网络能够互相通信就必须进行非常复杂的硬件地址转换工作，因此由用户或用户主机来完成这项工作几乎是不可能的事。但IP编址把这个复杂问题解决了。连接到互联网的主机只需要各自拥有一个唯一的IP地址，它们之间的通信就像连接在同一个网络上那样简单方便，因为调用ARP的复杂过程都是由计算机软件自动进行的，对用户来说是看不见这种调用过程的。

因此，在虚拟的IP网络上用IP地址进行通信给广大的计算机用户带来很大的方便。

## IP数据报的格式

* 一个IP数据报由首部和数据两部分组成
* 首部的前一部分是固定长度，共20字节，是所有IP数据报必须具有的
* 在首部的固定部分的后面是一些可选字段，其长度是可变的



# 划分子网和构造超网

## 划分子网

划分依据：子网掩码

### 从两级IP地址到三级IP地址

划分子网已成为因特网的正式标准协议。

格式如下：

​	     IP地址 ::= {<网络号>, <子网号>, <主机号>}

*注意：划分子网后，对外仍是一个网络*

### 子网掩码

如何判断网络是否进行了子网的划分？

子网掩码！使用子网掩码可以找出 IP 地址中的子网部分。

(IP 地址) AND (子网掩码) =子网的网络地址，如

IP地址为：145.13.3.10，三级子网掩码为255.255.255.0，得子网的网络地址为142.13.3.0

#### 默认子网掩码

* A类地址：255.0.0.0
* B类地址：255.255.0.0
* C类地址：255.255.255.0

子网掩码是一个网络或一个子网的重要属性。

#### 例

* 已知IP地址是141.14.72.24，子网掩码是255.255.192.0，试求网络地址

由于  (IP 地址) AND (子网掩码) = 网络地址 ，得

网络地址为 141.14.64.0

* 若子网掩码改为255.255.224.0，试求网络地址

得网络地址为141.14.64.0

可得：同样的IP地址和不同的子网掩码可以得出相同的网络地址。但是，不同的子网掩码的效果是不同的，如例1中，子网号是2位，主机号是14位；例2中，子网号是3位，主机号是13位；

## 无分类编址CIDR（构造超网）

<u>变长子网掩码VLSM（Variable Length Subnetwork Mask）---->无分类域间路由选择CIDR（Classless Inter-Domain Routing）</u>

格式：

​	IP地址 ::= {<网络前缀>, <主机号>} 

​	使用“斜线记法”或称为CIDR记法

特点：

* CIDR 消除了传统的 A 类、B 类和 C 类地址以及划分子网的概念，因而可以更加有效地分配 IPv4 的地址空间。

* CIDR把网络前缀都相同的连续的IP地址组成一个"CIDR地址块"

  例如，已知IP地址128.14.35.7/20是某CIDR地址块中的一个地址，现在把它写成二进制表示，其中前20位是网络前缀，后12位是主机号：

  ​	128.14.35.7/20 = <u>10000000 00001110 0010</u>0011 0000111

  这个地址所在的地址块中的最小地址和最大地址为：

  | 最小地址 | <u>10000000 00001110 0010</u>0000 00000000 | 128.14.32.0   |
  | -------- | ------------------------------------------ | ------------- |
  | 最大地址 | <u>10000000 00001110 0010</u>1111 11111111 | 128.14.47.255 |

例如，地址192.199.170.82/27不仅表示IP地址是192.199.170.82，而且有

192.199.170.82/27 = <u>11000000 11000111 1.......  010</u>.....

则地址块中的最小地址为：11000000 11000111 1....... 01000000=192.199.170.64

地址块中的最大地址为：11000000 11000111 1....... 01011111=192.199.170.95



# 网际控制报文协议ICMP

为了更有效地转发IP数据报和提高交付成功的机会，在网际层使用了网际控制报文协议ICMP（Internet Control Message Protocol）

ICMP允许主机或路由器报告差错情况和提供有关异常情况的报告。

ICMP不是高层协议（看起来是高层协议，因为ICMP报文是装在IP数据报中，作为其中的数据部分），而是IP层的协议。

ICMP报文作为IP层数据报的数据，加上数据报的首部，组成IP数据报发送出去。

![](D:\Work\TyporaNotes\note\计算机网络\pict\ICMP报文的格式.png)

## ICMP的种类

(查询)

* ICMP差错报告报文
* ICMP询问报文

## ICMP差错报告报文

（中原十厂改）

* 终点不可达
* 源点抑制
* 时间超过
* 参数问题
* 改变路由（重定向）

![](D:\Work\TyporaNotes\note\计算机网络\pict\ICMP差错报告报文的数据字段的内容.png)

## ICMP询问报文（回时）

* 回送请求和回答
* 时间戳请求和回答

## ICMP的应用举例

* 分组网间探测PING（Packet InterNet Groper）
* Traceroute



# 互联网的路由选择协议

* 静态
* 动态
  * 内部网关协议
    * RIP
    * OSPF

## 静态路由配置

### 静态路由配置过程

* 为网络中的每个接口配置IP地址
* 为每个路由器的非直连链路配置静态路由

### 示例

![](D:\Work\TyporaNotes\note\计算机网络\pict\静态路由配置示例.png)

注意：ip route 目标地址的网络号 目标地址的子网掩码 下一个路由端口地址

## 引入动态路由协议

![](D:\Work\TyporaNotes\note\计算机网络\pict\引入动态路由1.png)

![](D:\Work\TyporaNotes\note\计算机网络\pict\引入动态路由2.png)

![](D:\Work\TyporaNotes\note\计算机网络\pict\引入动态路由3.png)



## 有关路由选择协议的几个基本概念

### 理想的路由算法

* 算法必须是正确的和完整的。 
* 算法在计算上应简单。 
* 算法应能适应通信量和网络拓扑的变化，这就是说，要有自适应性。 
* 算法应具有稳定性。 
* 算法应是公平的。 
* 算法应是最佳的。

### 分层次的路由选择协议

由于因特网的规模非常大，且许多单位不愿意外界了解自己单位网络的布局细节和本部门所采用的路由选择协议（这属于本部门内部的事情），但同时还希望连接到因特网上，故出现了自治系统 AS。

#### 自制系统AS

在单一的技术管理下的一组路由器，而这些路由器使用一种 AS 内部的路由选择协议和共同的度量以确定分组在该 AS 内的路由。

一个 AS 对其他AS 表现出的是一个单一的和一致的路由选择策略。

在目前的互联网中，一个大的ISP就是一个自治系统。这样，互联网就把路由选择协议划分为两大类，即：

* 内部网关协议IGP

  用来在同一个自治系统内部交换路由信息。

  目前这类路由选择协议使用得最多，如RIP和OSPF协议

* 外部网关协议EGP

  用来在不同的自治系统间交换路由信息，如BGP

![](D:\Work\TyporaNotes\note\计算机网络\pict\自治系统和内部网关协议、外部网关协议.png)

## 内部网关协议RIP

### 工作原理

路由信息协议RIP是内部网关协议IGP中最先得到广泛使用的协议。

RIP是一种<font color=red>分布式的基于距离向量</font>的路由选择协议。

RIP协议要求网络中的每一个路由器都要维护从它自己到其他每一个目的网络的距离记录。

### 距离定义

从一路由器到直接连接的网络的距离定义为1

从一个路由器到非直接连接的网络的距离定义为所经过的路由器数加1

RIP协议中的”距离“也称为”跳数“，因为每经过一个路由器，跳数就加1

这里的“距离”实际上指的是“最短距离”

RIP认为一个好的路由就是它通过的路由器的数目少，即“距离短”

RIP允许一条路径最多只能包含15个路由器

“距离”的最大值是16时即相当于不可达，可见RIP只适用于小型互联网

RIP不能在两个网络之前同时使用多条路由。RIP选择一个具有最少路由器的路由（即最短路由），哪怕还存在另一台高速（低时延）但路由器较多的路由。

### RIP协议的三个要点

* **仅和相邻路由器交换信息**
* 交换的信息是当前本路由器所知道的**全部信息**，即自己的路由表（信息：到所有网络的距离和下一跳路由器）
* 按固定的时间间隔交换路由信息，例如，每隔30秒

### 路由表的建立

路由器在刚刚开始工作时，只知道直连路由的网络的距离（此距离定义为1）

以后，每一个路由器也只和数目非常有限的相邻路由器交换并更新路由信息

经过若干次更新后，所有的路由器最终都会知道到达本自治系统中任何一个网络的最短距离和下一跳路由器的地址

RIP协议的收敛过程较快，即在自治系统中所有的结点都得到正确的路由选择信息的过程

虽然所有的路由器最终都拥有了整个自治系统的全局路由信息，但由于每一个路由器的位置不同，它们的路由表当然也应当是不同的。

"1,1,直接”表示”到网1的距离是1，直接交付“

"1,2,R1"表示“到网1的距离是2，下一跳经过R1"

### RIP协议的报文格式

RIP协议使用运输层的用户数据报UDP进行运送

### RIP协议的优缺点

RIP协议的最大优点就是实现简单，开销较小。

RIP存在一个问题是：当网络出现故障时，要经过比较长的时间才能将此信息传送到所有的路由器，即“好消息传播得快，而坏消息传播得慢”。

RIP限制了网络的规模，它能使用的最大距离为15（16表示不可达）

路由器之间交换的路由信息是路由器中的完整路由表，因而随着网络规模的扩大，开销也就增加。

### RIP协议的应用

对于规模较大的网络就应当使用OSPF协议，而在规模较小的网络中，使用RIP协议的仍占多数

## 内部网关协议OSPF

OSPF:开放最短路径优先（Open Shortest Path First）

### OSPF协议的基本特点

* “开放”表明 OSPF 协议不是受某一家厂商控制，而是公开发表的。
* “最短路径优先”是因为使用了 Dijkstra 提出的最短路径算法SPF
* OSPF 只是一个协议的名字，它并不表示其他的路由选择协议不是“最短路径优先”。
* OSPF最主要的特征就是使用是分布式的链路状态协议。 

### OSPF三个要求

* 向本自治系统中**所有路由器**发送信息，这里使用的方法是洪泛法。

* 发送的信息就是**与本路由器相邻的所有路由器的链路状态，但这只是路由器所知道的部分信息**。

  链路状态：说明本路由器都和哪些路由器相邻，以及该链路的”度量“/”代价“；

  度量：是评价一条路由条目的优劣程度

  * 度量可表示费用、距离、时延、带宽等

  * 相同路由协议生成到相同目标网络的路由条目时，根据度量确定哪条路由条目显示在路由表中
  * <u>如果多条路由条目度量相同则启用负载均衡</u>

* **只有当链路状态发生变化时**，路由器才用洪泛法向所有路由器**发送**此信息。 

### 链路状态数据库

由于各路由器之间频繁地交换链路状态信息，因此所有的路由器最终都能建立一个链路状态数据库，这个数据库实际上就是全网的拓扑结构图，它在全网范围内是一致的（这称为链路状态数据库的同步）。(RIP协议的每一个路由器虽然知道所有的网络的距离以及下一跳路由器，但却不知道全网的拓扑结构)

OSPF 的链路状态数据库能较快地进行更新，使各个路由器能及时更新其路由表。

OSPF 的更新过程收敛得快是其重要优点。 

### OSPF的区域

为了使OSPF能够用于规模很大的网络，OSPF将一个自治系统再划分为若干个更小的范围，叫做区域。

每一个区域都有一个32位的区域标识符（用点分十进制表示）

区域也不能太大，在一个区域内的路由器最好不超过200个

### 划分区域

划分区域的好处就是把利用洪泛法交换链路状态信息的范围局限于每一个区域而不是整个的自治系统，这就减少了整个网络上的通信量。在一个区域内部的路由器只知道本区域的完整网络拓扑，而不知道其他区域的网络拓扑的情况。

为了使每一个区域能够和本区域以外的区域进行通信，OSPF使用层次结构的区域划分。在上层的区域叫做主干区域，主干区域的标识符规定为0.0.0.0，主干区域的作用是用来连通其他在下层的区域。

![](D:\Work\TyporaNotes\note\计算机网络\pict\OSPF划分为两种不同的区域.png)

从其他区域来的信息都由区域边界路由器，每一个区域至少应当有一个区域边界路由器，如R3,R4,R7

在主干区域内的路由器叫做主干路由器，如R3、R4、R5、R6、R7

一个主干路由器可以同时是区域边界路由器，如R3,R4,R7

采用分层次划分区域的方法虽然使交换信息的种类增多了，同时也使OSPF协议更加复杂了，但使得一个区域内部交换路由信息的通信量大大减小，因而使OSPF协议能够用于规模很大的自治系统中。

OSPF不用UDP而是直接用IP数据报传送

### 路由协议的比较

| 路由协议     | RIPv1 | RIPv2 | OSPF | BGP  |
| ------------ | ----- | ----- | ---- | ---- |
| 距离矢量     | √     | √     |      | √    |
| 链路状态     |       |       | √    |      |
| 有类路由协议 | √     |       |      |      |
| 无类路由协议 |       | √     | √    | √    |
| 变长子网掩码 |       | √     | √    | √    |
| 路由自动汇总 | √     | √     |      | √    |
| 路由手动汇总 |       | √     | √    | √    |
| 收敛速度     | 慢    | 慢    | 快   | 慢   |

| 参数       | RIPv1               | OSPF                |
| ---------- | ------------------- | ------------------- |
| 协议       | UDP520（端口号）    | IP89（协议字段值）  |
| 服务类型   | 尽力而为            | 1-to-1 Windows      |
| 管理距离值 | 120                 | 110                 |
| 更新内容   | 路由表              | 链路和接口状态      |
| 更新方式   | <u>***广播包***</u> | <u>***组播包***</u> |
| 利用方式   | 周期更新            | 触发更新            |
| 缺点       | 有环路产生          | 技术相对复杂        |
| 路由特点   | 完全信任邻居        | 独自计算路径        |
| 实际需求   | 极少                | 很多                |

## 路由器的构成

### 路由概念

什么是路由？

<u>路由是指导IP数据包发送的路径信息</u>

什么是路由器？

<u>路由器是工作在第三层，提供路由功能的设备</u>

什么是路由表？

<u>在路由器中维护的路由条目，是路径选择的依据</u>

路由选路原则？

按精确度逐减顺序尽量做到最精确匹配

默认路由

目标网络和掩码均是0，当路由表中所有表目均无法匹配目标网络时，将匹配默认路由条目

### 路由分类

#### 根据路由的目的地不同

* 子网路由
* 主机路由

#### 根据目的地与该路由器是否直连

* 直连路由
* 间接路由

```
C    192.168.10.0/24 is directly connected, FastEthernet 0/0	//直连路由
C    192.168.10.1/32 is local host. 
C    192.168.20.0/24 is directly connected, FastEthernet 0/1	 //直连路由
C    192.168.20.1/32 is local host. 	
S    192.168.30.0/24 [1/0] via 192.168.10.2		//负载均衡
	 192.168.30.0/24 [1/0] via 192.168.20.2		//负载均衡
S*	ip route 0.0.0.0 0.0.0.0 192.168.10.2 		//默认路由

```

### 路由协议分类

#### 按照<font color=red>路由表的生成方式</font>

* 静态路由协议（手不小简）

  又称非自适应路由选择，特点如下：

  * 静态路由的路由表中的条目是网络管理员手工添加的
  * 不需要占用路由器的CPU与带宽资源
  * 适用于中小型网络和特殊环境
  * 配置简单、维护复杂

* 动态路由协议

  网络中的路由器之间相互通信，传递路由信息，利用收到的路由信息更新和维护路由表的过程，又称自适应路由选择，特点如下：

  * 动态路由的路由表中的条目是路由协议自动学习的
  * 需要占用路由器的CPU与带宽资源
  * 适用于大型网络
  * 配置复杂、维护简单

#### 按照路由学习方式

* 距离矢量路由协议

  特点：每台路由器在路由信息上都依赖于自己的相邻路由器，而它的相邻路由器又是通过自它们自己的相邻路由器那里学习路由

  典型例子：RIP

* 链路状态路由协议

  特点：运行链路状态协议的路由器把路由器分成区域，收集区域的所有的路由器的链路状态信息，根据状态信息生成网络拓扑结构，每一个路由器再根据拓扑结构计算出路由。

  典型例子：OSPF

# IPv6

位数：128位

IPv6的地址使用冒号十六进制记法

IPv6的首部长度是固定的

   

# 本章的重要概念

* 中继器----物理层

  网桥----数据链路层

  路由器----网络层

  桥路器（三层交换机）----数据链路层+网络层

  网关----应用层

* 在互联网上的交付有两种：在本网络上的直接交付（不经过路由器）和到其他网络的间接交付（经过至少一次路由器，但最后一次一定是直接交付）

* 由于一个路由器至少应当连接两个网络，因此一个路由器至少应当有两个不同的IP地址

* 路由聚合（把许多前缀相同的地址用一个来代替）有利于减少路由表中的项目，减少路由器之间的路由选择信息的交换，从而提高了整个互联网的性能

* “转发”和“路由选择”区别。“转发”是单个路由器的动作，“路由选择”是许多路由器共同协作的过程，这些路由器相互交换信息，目的是生成路由表，再从路由表导出转发表。若采用自适应路由选择算法， 则当网络拓扑变化时，路由表和转发表都能自动更新。在许多情况下，可以不考虑转发表和路由表的区别，而都使用路由表这一名词。

