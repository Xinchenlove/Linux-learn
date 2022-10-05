[来源：博客园]: https://www.cnblogs.com/zhujingzhi/p/9706664.html	"iptables"



## 一、什么是iptables

　iptables是Linux的防火墙管理工具而已，真正实现防火墙功能的是Netfilter，我们配置了iptables规则后Netfilter通过这些规则来进行防火墙过滤等操作

　　Netfilter模块：

　　它是主要的工作模块，位于内核中，在网络层的五个位置（也就是防火墙四表五链中的五链）注册了一些钩子函数，用来抓取数据包；把数据包的信息拿出来匹配各个各个链位置在对应表中的规则：匹配之后，进行相应的处理ACCEPT、DROP等等。

　　下面这张图很明了的说明了Netfilter和iptables之间的关系

![img](Iptables.assets/1479220-20180926145432701-774427524.png)

## 二、四表五链

链就是位置：共有5个

进路由（PREROUTING）

进系统（INPUT）

转发 （FORWARD）

出系统（OUTPUT）

出路由（POSTROUTING）

表就是存储的规则；数据包到了该链处，会去对应表中查询设置的规则，然后决定是否放行、丢弃、转发还是修改等等操作。



#### 1.具体的四张表

filter表——过滤数据包

Nat表——用于网络地址转换（IP、端口）

Mangle表——修改数据包的服务类型

Row表——决定数据包是否被状态跟踪机制处理

#### 2.具体的五链

- INPUT链——进来的数据包应用此规则链中的策略
- OUTPUT链——外出的数据包应用此规则链中的策略
- FORWARD链——转发数据包时应用此规则链中的策略
- PREROUTING链——对数据包作路由选择前应用此链中的规则（所有的数据包进来的时侯都先由这个链处理）
- POSTROUTING链——对数据包作路由选择后应用此链中的规则（所有的数据包出来的时侯都先由这个链处理）

##### iptables四表五链的关系

链是放在表中的，总共有四个表，每个表有不同的优化级：raw>mangle >nat> filter

一个表有若干个链，如下所示：

|             | raw  | mangle | nat  | filter |
| ----------- | ---- | ------ | ---- | ------ |
| prerouting  | Y    | Y      | Y    |        |
| input       |      | Y      |      | Y      |
| output      | Y    | Y      | Y    | Y      |
| forward     |      | Y      |      | Y      |
| postrouting |      | Y      | Y    |        |



##  三、Iptables语法参数

#### iptables 语法格式

```shell
iptables [-t 表名] 选项 [链名] [条件] [-j 控制类型]
```

#### iptables常用参数

```shell
-P 设置默认策略:iptables
 
-P INPUT (DROP|ACCEPT)
 
-F 清空规则链
 
-L 查看规则链
 
-A 在规则链的末尾加入新规则 #
 
-I num 在规则链的头部加入新规则
 
-D num 删除某一条规则
 
-s 匹配来源地址IP/MASK，加叹号"!"表示除这个IP外。
 
-d 匹配目标地址
 
-i 网卡名称 匹配从这块网卡流入的数据#
 
-o 网卡名称 匹配从这块网卡流出的数据#
 
-p 匹配协议,如tcp,udp,icmp
 
--dport num 匹配目标端口号
 
--sport num 匹配来源端口号
```

## **iptables 的常用实例**

```shell
1. 删除已有规则
在开始创建iptables规则之前，你也许需要删除已有规则。命令如下：
iptables -F
(or)
iptables –flush
查看已有规则
iptables -nL
 
 
2.设置链的默认策略
链的默认政策设置为”ACCEPT”（接受），若要将INPUT,FORWARD,OUTPUT链设置成”DROP”（拒绝），命令如下：
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP
当INPUT链和OUTPUT链都设置成DROP时，对于每一个防火墙规则，我们都应该定义两个规则。例如：一个传入另一个传出。在下面所有的例子中，由于我们已将DROP设置成INPUT链和OUTPUT链的默认策略，每种情况我们都将制定两条规则。
当然，如果你相信你的内部用户,则可以省略上面的最后一行。例如：默认不丢弃所有出站的数据包。在这种情况下,对于每一个防火墙规则要求,你只需要制定一个规则——只对进站的数据包制定规则。
 
 
3. 阻止指定IP地址
例：丢弃来自IP地址x.x.x.x的包
BLOCK_THIS_IP="x.x.x.x"
iptables -A INPUT -s "$BLOCK_THIS_IP" -j DROP
注：当你在log里发现来自某ip地址的异常记录，可以通过此命令暂时阻止该地址的访问以做更深入分析
例：阻止来自IP地址x.x.x.x eth0 tcp的包
iptables -A INPUT -i eth0 -s "$BLOCK_THIS_IP" -j DROP
iptables -A INPUT -i eth0 -p tcp -s "$BLOCK_THIS_IP" -j DROP
 
 
4. 允许所有SSH的连接请求
例：允许所有来自外部的SSH连接请求，即只允许进入eth0接口，并且目标端口为22的数据包
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
 
 
5. 仅允许来自指定网络的SSH连接请求
例：仅允许来自于192.168.100.0/24域的用户的ssh连接请求
iptables -A INPUT -i eth0 -p tcp -s 192.168.100.0/24 --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
 
 
6.允许http和https的连接请求
例：允许所有来自web - http的连接请求
iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
例：允许所有来自web - https的连接请求
iptables -A INPUT -i eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT
 
 
7. 使用multiport 将多个规则结合在一起
允许多个端口从外界连入，除了为每个端口都写一条独立的规则外，我们可以用multiport将其组合成一条规则。如下所示：
例：允许所有ssh,http,https的流量访问
iptables -A INPUT -i eth0 -p tcp -m multiport --dports 22,80,443 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp -m multiport --sports 22,80,443 -m state --state ESTABLISHED -j ACCEPT
 
 
8. 允许从本地发起的SSH请求
iptables -A OUTPUT -o eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
请注意,这与允许ssh连入的规则略有不同。本例在OUTPUT链上，我们允许NEW和ESTABLISHED状态。在INPUT链上，我们只允许ESTABLISHED状态。ssh连入的规则与之相反。
 
 
9. 仅允许从本地发起到一个指定的网络域的SSH请求
例：仅允许从内部连接到网域192.168.100.0/24
iptables -A OUTPUT -o eth0 -p tcp -d 192.168.100.0/24 --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
 
 
10. 允许从本地发起的HTTPS连接请求
下面的规则允许输出安全的网络流量。如果你想允许用户访问互联网，这是非常有必要的。在服务器上，这些规则能让你使用wget从外部下载一些文件
iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT
注：对于HTTP web流量的外联请求，只需要将上述命令中的端口从443改成80即可。
 
 
11. 负载平衡传入的网络流量
使用iptables可以实现传入web流量的负载均衡，我们可以传入web流量负载平衡使用iptables防火墙规则。
例：使用iptables nth将HTTPS流量负载平衡至三个不同的ip地址。
iptables -A PREROUTING -i eth0 -p tcp --dport 443 -m state --state NEW -m nth --counter 0 --every 3 --packet 0 -j DNAT --to-destination 192.168.1.101:443
iptables -A PREROUTING -i eth0 -p tcp --dport 443 -m state --state NEW -m nth --counter 0 --every 3 --packet 1 -j DNAT --to-destination 192.168.1.102:443
iptables -A PREROUTING -i eth0 -p tcp --dport 443 -m state --state NEW -m nth --counter 0 --every 3 --packet 2 -j DNAT --to-destination 192.168.1.103:443
 
 
12. 允许外部主机ping内部主机
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT
 
 
13. 允许内部主机ping外部主机
iptables -A OUTPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-reply -j ACCEPT
 
 
14. 允许回环访问 例：在服务器上允许127.0.0.1回环访问。
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT
 
 
15. 允许内部网络域外部网络的通信
防火墙服务器上的其中一个网卡连接到外部，另一个网卡连接到内部服务器，使用以下规则允许内部网络与外部网络的通信。此例中，eth1连接到外部网络(互联网)，eth0连接到内部网络(例如:192.168.1.x)。
iptables -A FORWARD -i eth0 -o eth1 -j ACCEPT
 
 
16. 允许出站的DNS连接
iptables -A OUTPUT -p udp -o eth0 --dport 53 -j ACCEPT
iptables -A INPUT -p udp -i eth0 --sport 53 -j ACCEPT
 
 
17. 允许NIS连接
如果你使用NIS管理用户帐户，你需要允许NIS连接。如果你不允许NIS相关的ypbind连接请求，即使SSH连接请求已被允许，用户仍然无法登录。NIS的端口是动态的，先使用命令rpcinfo –p来知道端口号，此例中为853和850端口。
rpcinfo -p | grep ypbind
例：允许来自111端口以及ypbind使用端口的连接请求
iptables -A INPUT -p tcp --dport 111 -j ACCEPT
iptables -A INPUT -p udp --dport 111 -j ACCEPT
iptables -A INPUT -p tcp --dport 853 -j ACCEPT
iptables -A INPUT -p udp --dport 853 -j ACCEPT
iptables -A INPUT -p tcp --dport 850 -j ACCEPT
iptables -A INPUT -p udp --dport 850 -j ACCEPT
注：当你重启ypbind之后端口将不同，上述命令将无效。有两种解决方案：
1）使用你NIS的静态IP
2）编写shell脚本通过“rpcinfo - p”命令自动获取动态端口号,并在上述iptables规则中使用。
 
 
18. 允许来自指定网络的rsync连接请求
例：允许来自网络192.168.101.0/24的rsync连接请求
iptables -A INPUT -i eth0 -p tcp -s 192.168.101.0/24 --dport 873 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 873 -m state --state ESTABLISHED -j ACCEPT
 
 
19. 允许来自指定网络的MySQL连接请求
很多情况下，MySQL数据库与web服务跑在同一台服务器上。有时候我们仅希望DBA和开发人员从内部网络（192.168.100.0/24）直接登录数据库，可尝试以下命令：
iptables -A INPUT -i eth0 -p tcp -s 192.168.100.0/24 --dport 3306 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 3306 -m state --state ESTABLISHED -j ACCEPT
 
 
20. 允许Sendmail, Postfix邮件服务
Sendmail和postfix都使用了25端口，因此我们只需要允许来自25端口的连接请求即可。
iptables -A INPUT -i eth0 -p tcp --dport 25 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 25 -m state --state ESTABLISHED -j ACCEPT
 
 
21. 允许IMAP和IMAPS 例：允许IMAP/IMAP2流量，端口为143
iptables -A INPUT -i eth0 -p tcp --dport 143 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 143 -m state --state ESTABLISHED -j ACCEPT
例：允许IMAPS流量，端口为993
iptables -A INPUT -i eth0 -p tcp --dport 993 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 993 -m state --state ESTABLISHED -j ACCEPT
 
 
22. 允许POP3和POP3S 例：允许POP3访问
iptables -A INPUT -i eth0 -p tcp --dport 110 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 110 -m state --state ESTABLISHED -j ACCEPT
例：允许POP3S访问
iptables -A INPUT -i eth0 -p tcp --dport 995 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 995 -m state --state ESTABLISHED -j ACCEPT
 
 
23. 防止DoS攻击
iptables -A INPUT -p tcp --dport 80 -m limit --limit 25/minute --limit-burst 100 -j ACCEPT
上述例子中： -m limit: 启用limit扩展 –limit 25/minute: 允许最多每分钟25个连接（根据需求更改）。 –limit-burst 100: 只有当连接达到limit-burst水平(此例为100)时才启用上述limit/minute限制。
 
 
24. 端口转发 例：将来自422端口的流量全部转到22端口。
这意味着我们既能通过422端口又能通过22端口进行ssh连接。启用DNAT转发。
iptables -t nat -A PREROUTING -p tcp -d 192.168.102.37 --dport 422 -j DNAT --to 192.168.102.37:22
除此之外，还需要允许连接到422端口的请求
iptables -A INPUT -i eth0 -p tcp --dport 422 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 422 -m state --state ESTABLISHED -j ACCEPT
 
 
25. 记录丢弃的数据表 第一步：新建名为LOGGING的链
iptables -N LOGGING
第二步：将所有来自INPUT链中的数据包跳转到LOGGING链中
iptables -A INPUT -j LOGGING
第三步：为这些包自定义个前缀，命名为”IPTables Packet Dropped”
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables Packet Dropped: " --log-level 7
第四步：丢弃这些数据包
iptables -A LOGGING -j DROP
 
 
26. ip映射(NAT)
假设有一家ISP提供园区Internet接入服务，为了方便管理，该ISP分配给园区用户的IP地址都是伪IP，但是部分用户要求建立自己的WWW服务器对外发布信息。
我们可以再防火墙的外部网卡上绑定多个合法IP地址，然后通过ip映射使发给其中某一 个IP地址的包转发至内部某一用户的WWW服务器上，然后再将该内部WWW服务器响应包伪装成该合法IP发出的包。
 
我们假设以下情景:
该ISP分配给A单位www服务器的ip为:
伪ip:192.168.1.100
真实ip:202.110.123.100
该ISP分配给B单位www服务器的ip为:
伪ip:192.168.1.200
真实ip:202.110.123.200
linux防火墙的ip地址分别为:
内网接口eth1:192.168.1.1
外网接口eth0:202.110.123.1
然后我们将分配给A、B单位的真实ip绑定到防火墙的外网接口，以root权限执行以下命令:
ifconfig eth0 add 202.110.123.100 netmask 255.255.255.0
ifconfig eth0 add 202.110.123.200 netmask 255.255.255.0
 
首先，对防火墙接收到的目的ip为202.110.123.100和202.110.123.200的所有数据包进行目的NAT(DNAT):
iptables -A PREROUTING -i eth0 -d 202.110.123.100 -j DNAT --to 192.168.1.100
iptables -A PREROUTING -i eth0 -d 202.110.123.200 -j DNAT --to 192.168.1.200
 
其次，对防火墙接收到的源ip地址为192.168.1.100和192.168.1.200的数据包进行源NAT(SNAT):
iptables -A POSTROUTING -o eth0 -s 192.168.1.100 -j SNAT --to 202.110.123.100
iptables -A POSTROUTING -o eth0 -s 192.168.1.200 -j SNAT --to 202.110.123.200
 
这样，所有目的ip为202.110.123.100和202.110.123.200的数据包都将分别被转发给192.168.1.100和192.168.1.200;而所有来自192.168.1.100和192.168.1.200的数据包都将分 别被伪装成由202.110.123.100和202.110.123.200，从而也就实现了ip映射。
```

### 



















### iptables脚本



**可根据实际情况更改**

```shell
#!/bin/bash
/sbin/iptables -F
/sbin/iptables -X
/sbin/iptables -F -t nat
/sbin/iptables -X -t nat
/sbin/iptables -P INPUT ACCEPT
/sbin/iptables -P OUTPUT ACCEPT
/sbin/iptables -A INPUT -i lo -j ACCEPT
/sbin/iptables -A INPUT -s 192.168.0.0/24 -j ACCEPT
/sbin/iptables -A INPUT -s 192.168.0.0/24  -m state --state NEW -m tcp -p tcp --dport 10050 -j ACCEPT
/sbin/iptables -A INPUT -s 172.17.0.0/16 -j ACCEPT
/sbin/iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
/sbin/iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
/sbin/iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
 
/sbin/iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 10240 -j ACCEPT
/sbin/iptables -t nat -A PREROUTING -i em1 -d 123.59.32.113 -p tcp --dport 10240 -j DNAT --to-destination 192.168.0.186:5011
 
/sbin/iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 808 -j ACCEPT
 
/sbin/iptables -A INPUT -s 123.120.0.0/16 -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
/sbin/iptables -A INPUT -s 123.120.0.0/16 -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
/sbin/iptables -A INPUT -s 114.248.0.0/16 -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
/sbin/iptables -A INPUT -s 221.222.0.0/16 -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
/sbin/iptables -A INPUT -s 192.168.0.0/24 -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
 
IPLIST="117.122.219.0/24 61.164.149.0/24 122.228.208.0/24 221.222.0.0/16 114.248.0.0/16 123.120.0.0/16 111.201.0.0/16"
for ip in ${IPLIST};do
        /sbin/iptables -A INPUT -s ${ip} -p tcp -m state --state NEW -m tcp --dport 5000:5100 -j ACCEPT
        /sbin/iptables -A INPUT -s ${ip} -p tcp -m state --state NEW -m tcp --dport 5600:5700 -j ACCEPT
done
nginx=`docker inspect --format '{{ .NetworkSettings.IPAddress }}' synginx`
/sbin/iptables  -t nat -A PREROUTING -d 123.59.32.113 -p tcp -m tcp --dport 80 -j DNAT --to-destination ${nginx}:80
/sbin/iptables -t nat -A POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE
/sbin/iptables -A INPUT -p ICMP  -j ACCEPT
/sbin/iptables -A INPUT -j DROP
```





