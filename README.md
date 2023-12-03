# Computer Networking: A Top-Down Approach Eight edition

## chapter1
### 1.1

所有因特网设备被称为**主机**或**端系统**

端系统通过通信链路和分组交换机的网络链接到一起

端系统通过因特网服务提供商（ISP）接入因特网

每个ISP自身就是一个由多台分组交换机和多段通信链路组成的网络

因特网标准规定了所有因特网协议的标准，称为请求评论（RFC）

> 到底什么是因特网？

因特网为应用程序提供服务的基础设施

> ​	协议定义了在两个或多个对等通信实体之间交换的报文的格式和顺序，以及报文发送/接受或其他时间所采取的操作

以太网是局域网技术：物理 + 协议

### 1.3

交换机主要有路由器和链路层交换机

- 存储转发传输：在交换机开始向输出电路传输该分组的第一个比特之前，必须接收到整个分组
- 排队时延和分组丢失：对于每条相连的链路，该分组交换机都有一个输出缓存（输出队列），用于存储路由器准备发往那条链路的分组。
- 转发表和路由协议
  - 转发表用于将目的地址映射输出为输出链路
  - 转发局部，路由全局

通过网络链路和交换机移动数据有两种方法：电路交换和分组交换

- 电路交换：预留了端系统间沿路径通信所需要的资源（缓存，链路传输速率）
  - 频分复用（FDM）
  - 时分复用（TDM）
  - 波分复用（WDM）
  - 码分复用（CDM）
- 分组交换：不预留，因特网尽最大努力以及时交付分组，但它不做任何保证

### 1.4

分组交换网中的时延：

- 节点处理时延：检查分组字段决定导向何处
- 排队时延：分组在链路上等待传输
- 传输时延：将所有分组的比特推向链路的时间，路由器推出分组的时间
- 传播时延：一个比特从链路起点到下一个节点的时间，分组从一个路由器到下一个路由器的时间

吞吐量：

- 瞬时吞吐量是主机B接收到该文件的速率

### 1.5 协议分层及其服务模型

| 协议层 | 传输信息 | 常见协议      |
| :----: | :------: | ------------- |
| 应用层 |   报文   | HTTP,FTP,SMTP |
| 运输层 |  报文段  | TCP,UDP       |
| 网络层 |  数据报  | IP            |
| 链路层 |    帧    |               |
| 物理层 |   比特   |               |

- 应用层是网络应用程序及他们的应用层协议存留的地方
- 运输层提供流量控制和拥塞控制机制
- 网络层包括了网际协议和一些路由选择协议
  - 网际协议（IP）定义了在数据报中的各个字段以及端系统和路由器如何作用与这些字段
- 链路层服务取决于特定的链路层协议，负责将整个帧从当前网络元素移动至路径上的下一个网络元素
- 物理层将链路层帧的一个个比特从一个节点移动到下一个节点

### 1.6 网络攻击

- 拒绝服务攻击（DOS），分布式拒绝服务攻击（DDOS）

  - 弱点攻击

  - 带宽洪泛

  - 链接洪泛

- 分组嗅探

- IP哄骗





# 运输层

TCP：

- 传输控制协议
- 报文段

UDP：

- 用户数据包协议

- 数据报

将主机间交付扩展到进程间交付被称为运输层的**多路复用**与**多路分解**

- 将运输层报文段中的数据交付道正确的套接字的工作称为多路分解
- 将来自源主机的不同数据块收集起来，并为每个数据块封装上首部信息从而生成报文段，然后将报文段传递至网络层称为多路复用

多路复用要求：

- 套接字有唯一标识符
- 每个报文段有特殊字段来指示该报文段要交付的套接字
  - 源端口号和目的端口号
  - 端口号16比特
  - 每个运输层报文段最起码包括目的端口号和源端口号

多路分解服务：

- 在主机上的每个套接字能够分配一个端口号，当报文段到达主机时，运输层检查目的端口号并将其定位到相应的套接字

一个UDP套接字是一个二元组（目的IP，目的端口号）；两个UDP报文段有不同的源IP或源端口，指向相同的目的IP和目的端口，那么两个报文段将通过相同的目的套接字被定向到**相同**的进程。

一个TCP套接字是一个四元组（源IP，源端口，目的IP，目的端口）；两个TCP报文段有不同的源IP或源端口，指向相同的目的IP和目的端口，那么两个报文段将通过相同的目的套接字被定向到**两个不同**的进程。

### 无连接运输：UDP

- UDP只在最基础的运输层需要提供的服务基础之上添加了一点差错检测服务
  - 无需连接建立
  - 无连接状态
  - 分组首部开销小
  - 关于发送什么数据以及何时发送控制更精细
- 可以在应用层构建可靠性，从而避免了TCP协议的拥塞控制和流量控制

#### UDP报文段结构

源端口号，目的端口号，长度，校验和分别16比特

校验和：发送方UDP报文段中所有16比特字的和进行反码求和时遇到的所有溢出都会回卷，得到的结果。

UDP提供差错检测是因为不能保证从源和目的之间的所有链路都有差错检验

### 可靠数据传输原理

发送方需要发送：

- rdt_send()
- udt_send()

接收方需要:

- deliver_data()
- rdt_rcv()

rdt的接收方和发送方需要往返交换控制分组，调用`udt_send()`

#### rdt 1.0

- 假设底层信道完全可靠
- 发送方使用rdt_send发送之后只需等待上层调用
- 接收方使用rdt_rcv接受之后只需等待下层调用

<img src="https://img-blog.csdn.net/20161113211132315" width=60%>

#### rdt 2.0

- 此时分组中的比特可能会受损
- 发送方
  - 自动重传请求协议（ARQ）
    - 差错检测
    - 接收方反馈：ACK和NAK
    - 重传
  - 发送方并不能从`上层`获取更多的数据，rdt_send不会出现，发送方不会发送新的数据；停等协议

- 接收方
  - 接受数据，进行差错检验，根据结果返回ACK或NAK

<img src="https://img-blog.csdn.net/20161113220707328" width="60%">

#### rdt 2.1

- 传送过程中ACK或者NAK受损
- 当发送方接收到含糊不清的ACK或NAK分组时重传，但是接收方并不知道这次的分组是新的还是一次重传，**冗余分组**
- 发送方对数据分组进行编号，将数据分组的序号放在该字段

#### rdt 2.2

- 在`rdt 2.1`的基础上去掉了NAK
- 接收方和发送方需要附带分组序号

#### rdt 3.0（比特交替协议）

- 假设除了比特受损之外，底层信道还会丢包
- 定时重传，设置一个倒数计时器，发送方每发送一个分组就会启动一个倒数计时器

- 数据传输协议的要点：校验和、序号、定时器、肯定与否定确认

#### 流水线协议

- 停等协议浪费了大量资源，所以引入了流水线机制
- 引入流水线机制必须对RDT协议做出改变
  - 增加序号范围
  - 协议发送方和接收方需要缓存多个分组
  - 流水线的差错恢复：**回退N步**和**选择重传**
- 回退N步(GBN)
  - base:最先发出但是未被确认的分组的序号
  - nextseqnum:下一个未被使用的分组的序号
  - N最大长度:窗口最大长度
  - 一个分组的序号承载在分组首部的一个固定长度的字段重
- GBN发送方必须响应如下几种情况
  - 上层的调用:上层调用rdt_send(),GBN检查发送窗口是否已经满了;如果未满则更新变量,否则向上层反馈
  - 收到一个ACK:接收方已经正确接收到ACK为n以及之前的所有分组
  - 超时重传:一旦出现超时,GBN会重传所有已发送但是未被确认的分组
    - 发送方只有一个定时器,每当收到一个ACK并且还有已发送未被确认的分组,重启定时器
  - GBN发送方必须维护窗口的上下边界以及nextnum在该窗口中的位置
- GBN的接收方:
  - 接收方只需要维护一个下一个按序接受的分组序号,expectnum
  - 接收方正确收到一个序号为n的分组
    - 累计确认:接收方收到为n的正确按序分组意味着之前的分组也已经按序正确收到
    - 按序,接收方为分组n发送ACK,并将该分组中的数据交付到上层
    - 乱序,接收方丢弃该分组,并向发送方返回最近按序接受的分组ACK

#### 选择重传

>   在GBN协议中单个分组的差错就会引起大量GBN协议分组的重传,许多分组没必要重传,因此出现了选择重传(SR)

- 选择重传(SR)让发送方仅重传那些又可能出错的分组
- 这种个别的,按需的重传要求接收方逐个确认正确接收的分组
- SR接收方也引入了接收窗口,失序的分组将被缓存,直到所有的分组都被接受为止

- SR发送方:
  - 从上层接收到数据
  - 超时,每个分组都有自己的逻辑定时器,可以使用单个硬件定时器模拟多个逻辑定时器
  - 收到ACK,如果该ACK分组在SR发送窗口内,则标记为已确认分组;如果该ACK对应的是send_base则整个窗口向前移动到具有最小未被确认分组处;如果窗口移动期间有序号落在窗口内的未发送分组,则发送这些分组.
- SR接收方:
  - 序号在rcv_base~rcv_base+N-1内的分组被正确接受
    - 如果未被接受过,则该分组被缓存,并向发送方返回一个选择ACK
    - 如果是起始rcv_base序号,则按序向上层传送以该序号为起始的已经缓存的分组
  - 序号在rcv_base - N ~ rcv_base - 1 的序号,必须产生一个ACK,即使接收方之前确认过
  - 其余情况,忽略
- 对于SR协议而言,窗口长度必须小于或等于序号空间大小的一半
- 在分组被重新排序的情况下,我们必须确保一个序号不被重新使用,除非发送方确信任何先前发送的序号为x的分组都不在网络中为止,通过假设一个分组在网络中的存活时间不会超过某一个最大量来实现.

#### TCP

- tcp是面向连接的,三次握手
- TCP运行在端系统中,中间的网络元素不会维持TCP的链接状态,对于他们而言只是数据包
- TCP是全双工服务,Poin2Point
