# ubuntu 命令大全

发布于 2021年 07月 02日 17:08

 

## 前言

下面的命令大都需要在 控制台 / 终端 / shell 下输入。

控制台, 终端, 和 shell 意味着同样一件事 - 一个命令行界面， 他可以用来控制系统。

打开一个控制台:

应用程序 --> 附件 --> 终端

任何一个使用 'sudo' 作为前缀的命令都需要拥有管理员 (或 root) 访问权限。 所以你会被提示输入你自己的密码。

 



## 安装升级



### 查看软件xxx安装内容

```
dpkg -L xxx 
```



### 查找软件库中的软件

```
apt-cache search 正则表达式
或
aptitude search 软件包
```



### 显示系统安装包的统计信息

```
apt-cache stats
```



### 显示系统全部可用包的名称

```
apt-cache pkgnames
```



### 显示包的信息

```
apt-cache show k3b
```



### 查找文件属于哪个包

```
apt-file search filename
```



### 查看已经安装了哪些包

```
dpkg -l
```



### 查询软件xxx依赖哪些包

```
apt-cache depends xxx
```



### 查询软件xxx被哪些包依赖

```
apt-cache rdepends xxx
```



### 增加一个光盘源

```
sudo apt-cdrom add
```



### 系统升级

```
sudo apt-get update (这一步更新包列表)
sudo apt-get dist-upgrade (这一步安装所有可用更新)
或者
sudo apt-get upgrade (这一步安装应用程序更新，不安装新内核等)
```



### 清除所有已删除包的残馀配置文件

```
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P 
```

如果报如下错误，证明你的系统中没有残留配置文件了，无须担心。

------

dpkg: --purge needs at least one package name argument

Type dpkg --help for help about installing and deinstalling packages [*];
Use `dselect' or `aptitude' for user-friendly package management;
Type dpkg -Dhelp for a list of dpkg debug flag values;
Type dpkg --force-help for a list of forcing options;
Type dpkg-deb --help for help about manipulating *.deb files;
Type dpkg --license for copyright license and lack of warranty (GNU GPL) [*].

Options marked [*] produce a lot of output - pipe it through `less' or `more' !

 

------



### 编译时缺少h文件的自动处理

```
sudo auto-apt run ./configure
```



### 查看安装软件时下载包的临时存放目录

```
ls /var/cache/apt/archives
```



### 备份当前系统安装的所有包的列表

```
dpkg --get-selections | grep -v deinstall > ~/somefile
```



### 从上面备份的安装包的列表文件恢复所有包

```
dpkg --set-selections < ~/somefile
sudo dselect
```



### 清理旧版本的软件缓存

```
sudo apt-get autoclean
```



### 清理所有软件缓存

```
sudo apt-get clean
```



### 删除系统不再使用的孤立软件

```
sudo apt-get autoremove
```



### 查看包在服务器上面的地址

```
apt-get -qq --print-uris install ssh | cut -d/' -f2
```

 



### 彻底删除Gnome

```
apt-get --purge remove liborbit2
```



### 彻底删除KDE

```
apt-get --purge remove libqt3-mt libqtcore4
```



### 一键安装 LAMP 服务

```
sudo tasksel install lamp-server
```



### 删除旧内核

```
sudo aptitude purge ~ilinux-image-.*/(/!`uname -r`/)
```



### 导入ppa源的key值

```
#W: GPG签名验证错误： http://ppa.launchpad.net
 jaunty Release: 由于没有公钥，下列签名无法进行验证： NO_PUBKEY 5126890CDCC7AFE0
sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 5126890CDCC7AFE0 #5126890CDCC7AFE0替换为你需要导入的Key值
```



## 系统



### 查看内核

```
uname -r
```



### 查看Ubuntu版本

```
cat /etc/issue
或 cat /etc/lsb-release 
或 lsb_release -dsc
```



### 查看内核加载的模块

```
lsmod
```



### 查看PCI设备

```
lspci
```



### 查看USB设备

```
lsusb 

#加参数 -v 可以显示USB设备的描述表（descriptors）
lsusb -v
```



### 查看网卡状态

```
sudo ethtool eth0
```



### 激活网卡的 Wake-on-LAN

```
sudo apt-get install wakeonlan
或 sudo ethtool -s eth0 wol g
```



### 查看CPU信息

```
cat /proc/cpuinfo
```



### 显示当前硬件信息

```
sudo lshw
```



### 获取CPU序列号或者主板序列号

```
#CPU ID
sudo dmidecode -t 4 | grep ID
#Serial Number
sudo dmidecode  | grep  Serial
#CPU
sudo dmidecode -t 4
#BIOS
sudo dmidecode -t 0
#主板：
sudo dmidecode -t 2
#OEM:
sudo dmidecode -t 11
```



### 显示当前内存大小

```
free -m |grep "Mem" | awk '{print $2}'
```



### 查看硬盘温度

```
sudo apt-get install hddtemp
sudo hddtemp /dev/sda
```



### 显示系统运行时间

```
$ uptime
11:47:04 up 85 days,  2:10,  1 user,  load average: 2.40, 2.18, 2.34
```



## 硬盘



### 查看硬盘的分区

```
sudo fdisk -l
```



### 硬盘分区

```
#危险！小心操作。
sudo fdisk /dev/sda
```



### 硬盘格式化

```
#危险！将第一个分区格式化为 ext3 分区, mkfs.reiserfs mkfs.xfs mkfs.vfat
sudo mkfs.ext3 /dev/sda1
```



### 硬盘检查

```
#危险！检查第一个分区，请不要检查已经挂载的分区，否则容易丢失和损坏数据
sudo fsck /dev/sda1
```



### 分区挂载

```
sudo mount -t 文件系统类型 设备路经 访问路经 
#常用文件类型如下： iso9660 光驱文件系统, vfat fat/fat32分区, ntfs ntfs分区, smbfs windows网络共享目录, reiserfs、ext3、xfs Linux分区
#如果中文名无法显示尝试在最後增加 -o nls=utf8 或 -o iocharset=utf8 
#如果需要挂载後，普通用户也可以使用，在 -o 的参数後面增加 ,umask=022 如：-o nls=utf8,umask=022
```



### 分区卸载

```
sudo umount 目录名或设备名
```



### 只读挂载ntfs分区

```
sudo mount -t ntfs -o nls=utf8,umask=0 /dev/sdb1 /mnt/c
```



### 可写挂载ntfs分区

```
sudo mount -t ntfs-3g -o locale=zh_CN.utf8,umask=0 /dev/sdb1 /mnt/c
```



### 挂载fat32分区

```
sudo mount -t vfat -o iocharset=utf8,umask=0 /dev/sda1 /mnt/c
```



### 挂载共享文件

```
sudo mount -t smbfs -o  username=xxx,password=xxx,iocharset=utf8 //192.168.1.1/share /mnt/share
```



### 挂载ISO文件

```
sudo mount -t iso9660 -o loop,utf8 xxx.iso /mnt/iso
```



### 查看IDE硬盘信息

```
sudo hdparm -i /dev/hda
```



### 查看软raid阵列信息

```
cat /proc/mdstat
```



### 参看硬raid阵列信息

```
dmesg |grep -i raid
cat /proc/scsi/scsi
```



### 查看SATA硬盘信息

```
sudo hdparm -I /dev/sda
或
sudo apt-get install blktool
sudo blktool /dev/sda id
```



### 查看硬盘剩余空间

```
df
```

用法：df [选项]... [文件]...显示每个<文件>所在的文件系统的信息，默认是显示所有文件系统。

```
长选项必须用的参数在使用短选项时也是必需的。
  -a, --all             包括大小为 0 个块的文件系统
  -B, --block-size=大小 块以指定<大小>的字节为单位
  -h, --human-readable  以容易理解的格式印出文件系统大小 (例如 1K 234M 2G)
  -H, --si              类似 -h，但取 1000 的次方而不是 1024
  -i, --inodes          显示 inode 信息而非块使用量
  -k                    即 --block-size=1K
  -l, --local           只显示本机的文件系统
      --no-sync         取得使用量数据前不进行同步动作 (默认)
  -P, --portability     使用 POSIX 兼容的输出格式
      --sync            取得使用量数据前先进行同步动作
  -t, --type=类型       只印出指定文件系统为指定<类型>的信息
  -T, --print-type      印出文件系统类型
  -x, --exclude-type=类型   只印出文件系统不是指定<类型>信息
  -v                    (忽略)
      --help     显示此帮助信息并离开
      --version  显示版本信息并离开
```



### 查看目录占用空间

```
du -hs 目录名
```



### 优盘没法卸载

```
sync
fuser -km /media/usbdisk
```



### 使用文件来增加交换空间

```
#创建一个512M的交换文件 /swapfile
sudo dd if=/dev/zero of=/swapfile bs=1M count=512 
sudo mkswap /swapfile
sudo swapon /swapfile
#sudo vim /etc/fstab #加到fstab文件中让系统引导时自动启动
/swapfile swap swap defaults 0 0
```



### 查看硬盘当前读写情况

```
# 首先安装 sysstat 包
sudo apt-get install sysstat
#每2秒刷新一次
sudo iostat -x 2
```



## 进程



### 查看当前的内存使用情况

```
free
```

usage: free [-b|-k|-m|-g] [-l] [-o] [-t] [-s delay] [-c count] [-V]

```
  -b,-k,-m,-g show output in bytes, KB, MB, or GB
  -l show detailed low and high memory statistics
  -o use old format (no -/+buffers/cache line)
  -t display total for RAM + swap
  -s update every [delay] seconds
  -c update [count] times
  -V display version information and exit
```



### 连续监视内存使用情况

```
watch  -d free
# 使用 Ctrl + c 退出
```



### 动态显示进程执行情况

```
top
top指令运行时输入H或？打开帮助窗口，输入Q退出指令。
```



### 查看当前有哪些进程

```
ps -AFL
```



### 查看目前登入用户运行的程序

```
w
```



### 统计程序的内存耗用

```
ps -eo fname,rss|awk '{arr[$1]+=$2} END {for (i in arr) {print i,arr[i]}}'|sort -k2 -nr
```



### 按内存从大到小排列进程

```
ps -eo "%C  : %p : %z : %a"|sort -k5 -nr
```



### 按cpu利用率从大到小排列进程

```
ps -eo "%C  : %p : %z : %a"|sort  -nr
```



### 查看当前进程树

```
pstree
```



### 中止一个进程

```
kill 进程号(就是ps -A中的第一列的数字)
或者 killall 进程名
```



### 强制中止一个进程(在上面进程中止不成功的时候使用)

```
kill -9 进程号
或者 killall -9 进程名
```



### 图形方式中止一个程序

```
xkill 出现骷髅标志的鼠标，点击需要中止的程序即可
```

 



### 查看进程打开的文件

```
lsof -p 进程的pid
```



### 显示开启文件abc.txt的进程

```
lsof abc.txt 
```



### 显示22端口现在运行什么程序

```
lsof -i :22 
```



### 显示nsd进程现在打开的文件

```
lsof -c nsd
```



### 在後台运行程序，退出登录後，并不结束程序

```
nohup 程序 &
#查看中间运行情况　tail nohup
```



### 在后台运行交互式程序，退出登录后，并不结束程序

```
sudo apt-get install screen
screen vim a.txt
#直接退出后使用 
screen -ls   # 1656.pts-0.ubuntu   (Detached)
screen -r 1656  #恢复
#热键，同时按下Ctrl和a键结束后，再按下功能键
C-a ?	#显示所有键绑定信息
C-a w	#显示所有窗口列表
C-a C-a	#切换到之前显示的窗口
C-a c	#创建一个新的运行shell的窗口并切换到该窗口
C-a n	#切换到下一个窗口
C-a p	#切换到前一个窗口(与C-a n相对)
C-a 0..9	#切换到窗口0..9
C-a a	#发送 C-a到当前窗口
C-a d	#暂时断开screen会话
C-a k	#杀掉当前窗口
```



### 详细显示程序的运行信息

```
strace -f -F -o outfile <cmd>
```



### 增加系统最大打开文件个数

```
echo 4096 > /proc/sys/fs/file-max
#或者
ulimit -n 4096
```



### 清除僵死进程

```
ps -eal | awk '{ if ($2 == "Z") {print $4}}' | xargs sudo kill -9
```



### 将大于120M内存的php-cgi都杀掉

```
ps -eo pid,fname,rss|grep php-cgi|grep -v grep|awk '{if($3>=120000) print $1}' | xargs sudo kill -9
```



### Linux系统中如何限制用户进程CPU占用率

```
renice +10 `ps aux | awk '{ if ($3 > 0.8 && id -u $1 > 500) print $2}'` 
#或直接编辑/etc/security/limits.conf文件。
```



## ADSL



### 配置 ADSL

```
sudo pppoeconf
```



### ADSL手工拨号

```
sudo pon dsl-provider
```



### 激活 ADSL

```
sudo /etc/ppp/pppoe_on_boot
```



### 断开 ADSL

```
sudo poff
```



### 查看拨号日志

```
sudo plog
```



### 如何设置动态域名

```
#首先去 http://www.3322.org
 申请一个动态域名
#然後修改 /etc/ppp/ip-up 增加拨号时更新域名指令
sudo vim /etc/ppp/ip-up
#在最後增加如下行
w3m -no-cookie -dump 'http://username:password@members.3322.org/dyndns/update?system=dyndns&hostname=yourdns.3322.org'
```



## 网络



### 根据IP查网卡地址

```
arping IP地址
```



### 根据IP查电脑名

```
nmblookup -A IP地址
```



### 查看当前IP地址

```
ifconfig eth0 |awk '/inet/ {split($2,x,":");print x[2]}'
```



### 查看当前外网的IP地址

```
w3m -no-cookie -dump www.123cha.com|grep -o '[0-9]/{1,3/}/.[0-9]/{1,3/}/.[0-9]/{1,3/}/.[0-9]/{1,3/}'
w3m -no-cookie -dump ip.loveroot.com|grep -o '[0-9]/{1,3/}/.[0-9]/{1,3/}/.[0-9]/{1,3/}/.[0-9]/{1,3/}'
```



### 查看当前监听80端口的程序

```
lsof -i :80
```



### 查看当前网卡的物理地址

```
ifconfig eth0 | head -1 | awk '{print $5}'
```



### 同一个网卡增加第二个IP地址

```
#在网卡eth0上增加一个1.2.3.4的IP：
sudo ifconfig eth0:0 1.2.3.4 netmask 255.255.255.0
#删除增加的IP：
sudo ifconfig eth0:0 down
```



### 立即让网络支持nat

```
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
sudo iptables -t nat -I POSTROUTING -j MASQUERADE
```



### 查看路由信息

```
netstat -rn
sudo route -n
```



### 手工增加一条路由

```
sudo route add -net 192.168.0.0 netmask 255.255.255.0 gw 172.16.0.1
```



### 手工删除一条路由

```
sudo route del -net 192.168.0.0 netmask 255.255.255.0 gw 172.16.0.1
```



### 修改网卡MAC地址的方法

```
sudo ifconfig eth0 down #关闭网卡
sudo ifconfig eth0 hw ether 00:AA:BB:CC:DD:EE #然后改地址
sudo ifconfig eth0 up #然后启动网卡
```

永久改地址方法

```
sudo gedit /etc/network/interfaces
```

在 iface eth0 inet static 后面添加一行：

```
pre-up ifconfig eth0 hw ether 01:01:01:01:01:01
```

配置文件应该像如下

```
iface eth0 inet static
pre-up ifconfig eth0 hw ether 01:01:01:01:01:01
address 192.168.1.10
netmask 255.255.255.0
gateway 192.168.1.1
```

最后是 logout 或者reboot



### 统计当前IP连接的个数

```
netstat -na|grep ESTABLISHED|awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -r -n
netstat -na|grep SYN|awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -r -n
```



### 统计当前20000个IP包中大于100个IP包的IP地址

```
tcpdump -tnn -c 20000 -i eth0 | awk -F "." '{print $1"."$2"."$3"."$4}' | sort | uniq -c | sort -nr | awk ' $1 > 100 '
```



### 屏蔽IPV6

```
echo "blacklist ipv6" | sudo tee /etc/modprobe.d/blacklist-ipv6
```



### 察看当前网络连接状况以及程序

```
sudo netstat -atnp
```



### 查看网络连接状态

```
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
```



### 查看当前系统所有的监听端口

```
nc -zv localhost 1-65535
```



### 查看网络的当前流量

```
#安装 ethstatus 软件
sudo apt-get install ethstatus
#查看 ADSL 的速度
sudo ethstatus -i ppp0
#查看 网卡 的速度
sudo ethstatus -i eth0
#或安装 bwm-ng 
sudo apt-get install bwm-ng
#查看当前网络流量
bwm-ng
```



### 查看域名的注册备案情况

```
whois baidu.cn
```



### 查看到某一个域名的路由情况

```
tracepath baidu.cn
```



### 重新从服务器获得IP地址

```
sudo dhclient
```



### 从当前页面开始镜像整个网站到本地

```
wget -r -p -np -k http://www.21cn.com

· -r：在本机建立服务器端目录结构；
· -p: 下载显示HTML文件的所有图片；
· -np：只下载目标站点指定目录及其子目录的内容；
· -k: 转换非相对链接为相对链接。
```



### 如何多线程下载

```
sudo apt-get install axel
axel -n 5 http://xxx.xxx.xxx.xxx/xxx.zip

或者
lftp -c "pget -n 5 http://xxx.xxx.xxx.xxx/xxx.zip“
```



### 如何查看HTTP头

```
w3m -dump_head http://www.example.com

或 curl --head http://www.example.com
```



### 快速使用http方式共享目录

```
#进入需要共享的目录后运行: 
python -m SimpleHTTPServer
#其它电脑使用http://ip:8000
 来访问
#自定义端口为8080: 
python -m SimpleHTTPServer 8080
```



## 安全



### 检查本地是否存在安全隐患

```
sudo apt-get install rkhunter
sudo rkhunter --checkall
```



### 如何安装杀毒软件

```
sudo apt-get install clamav
clamscan -r ~/
```



### Linux下可以使用的商业杀毒软件

```
卡巴斯基(deb)： http://www.kaspersky.com/productupdates?chapter=146274389

avast!(免费/deb)： http://www.avast.com/eng/download-avast-for-linux-edition.html

小红伞(gz)： http://www.avira.com/en/downloads/avira_antivir_professional.html

BitDefender（比特梵德/run）：http://download.bitdefender.com/SMB/Workstation_Security_and_Management/BitDefender_Antivirus_Scanner_for_Unices/Unix/Current/EN_FR_BR_RO/Linux/
```

申请比特梵德的KEY：[http://www.bitdefender.com/site/Products/ScannerLicense/](javascript:void())



## 服务



### 添加一个服务

```
sudo update-rc.d 服务名 defaults 99
```



### 删除一个服务

```
sudo update-rc.d 服务名 remove
```



### 临时重启一个服务

```
/etc/init.d/服务名 restart
```



### 临时关闭一个服务

```
/etc/init.d/服务名 stop
```



### 临时启动一个服务

```
/etc/init.d/服务名 start
```



## 用户管理



### 增加用户

```
sudo adduser 用户名
```



### 删除用户

```
sudo deluser 用户名
```



### 修改当前用户的密码　

```
passwd
```



### 修改用户密码　

```
sudo passwd 用户名
```



### 修改用户资料

```
sudo chfn userid
```



### 如何禁用/启用某个帐户

```
sudo usermod -L 用户名 #锁定用户
sudo usermod -U 用户名 #解锁
或
sudo passwd -l 用户名 #锁定用户
sudo passwd -u 用户名 #解锁
```



### 增加用户到admin组，让其有sudo权限

```
sudo usermod -G admin -a 用户名
```



### 如何切换到其他帐号(需要该用户的密码)

```
su 用户名
```



### 如何切换到root帐号

```
sudo -s
```



## 设置



### 配置默认Java使用哪个

```
sudo update-alternatives --config java
```



### 给apt设置代理

```
export http_proxy=http://xx.xx.xx.xx:xxx
```



### 修改系统登录信息

```
sudo vim /etc/motd
```



### 使用eclipse等其他自带java编译器的软件，换回sun的编译器方法

对于Java JDK6 (就是1.6，sun缩短Java的版本名字了):

```
sudo update-java-alternatives -s java-6-sun
```

对于Java JDK1.5

```
sudo update-java-alternatives -s java-1.5.0-sun
```



## 中文



### 切换输入法引擎

```
im-switch -c
```



### 转换文件名由GBK为UTF8

```
sudo apt-get install convmv
convmv -r -f cp936 -t utf8 --notest --nosmart *
```



### 批量转换src目录下的所有文件内容由GBK到UTF8

```
find src -type d -exec mkdir -p utf8/{} /;
find src -type f -exec iconv -f GBK -t UTF-8 {} -o utf8/{} /;
mv utf8/* src
rm -fr utf8
```



### 转换文件内容由GBK到UTF8

```
iconv -f gbk -t utf8 $i > newfile
```



### 批量转换文件内容由GBK到UTF8

```
for i in `find . *`; do if [ -f "$i" ]; then iconv -f gb2312 -t utf8 $i > "./converted/$i" fi ; done
```



### 转换 mp3 标签编码

```
sudo apt-get install python-mutagen
find . -iname '*.mp3' -execdir mid3iconv -e GBK {} /;
```

或者使用图形界面工具“[小K](javascript:void()) ”，具体请参考[解决文件名mp3标签和文本文件内容的乱码问题](javascript:void())



### 控制台下显示中文

```
sudo apt-get install zhcon
使用时，输入zhcon即可
更具体的输入：zhcon --utf8 --drv=vga
```

zhcon是个外挂的控制平台,也就是像US-DOS那样是额外安装的软件,装完后是需要驱动才能进去的，不然有可能死机; 当然驱动什么的在你sudo apt-get install zhcon的时候就已经安装了; 你所需要的是在进zhcon时要申明你所用的驱动,而zhcon在安装时,就装了3种驱动:vga,framebuffer,libggi,而我们一般都 是用的第一种驱动,因为比较方便简单,而那2钟驱动.很麻烦,我也就没改过.好了说了这么多该告诉各位怎么进入zhcon了； 运行时需输入：zhcon --utf8 --drv=vga



### lftp 登录远程Windows中文FTP　

```
lftp :~>set ftp:charset GBK
lftp :~>set file:charset UTF-8
```



### java6 的安装和中文设置

```
sudo apt-get install openjdk-6-jdk openjdk-6-doc openjdk-6-jre openjdk-6-jre-headless openjdk-6-jre-lib
sudo apt-get remove ttf-kochi-gothic ttf-kochi-mincho ttf-unfonts ttf-unfonts-core
sudo mkdir -p /usr/lib/jvm/java-6-openjdk/jre/lib/fonts/fallback
sudo ln -s /usr/share/fonts/truetype/arphic/*.ttc /usr/lib/jvm/java-6-openjdk/jre/lib/fonts/fallback
#下面是安装标准的sun-java，安装过程中需要使用tab键切换同意其授权协议
sudo apt-get install sun-java6-jdk ttf-arphic-uming
sudo apt-get remove ttf-kochi-gothic ttf-kochi-mincho ttf-unfonts ttf-unfonts-core
sudo mkdir -p /usr/lib/jvm/java-6-sun/jre/lib/fonts/fallback
sudo ln -s /usr/share/fonts/truetype/arphic/*.ttc /usr/lib/jvm/java-6-sun/jre/lib/fonts/fallback
```



### 程序显示有些字大，有些小

```
 sudo apt-get remove ttf-kochi-gothic ttf-kochi-mincho ttf-unfonts ttf-unfonts-core
```



### PDF 文件乱码

```
sudo apt-get install xpdf-chinese-simplified xpdf-chinese-traditional poppler-data
```



### Flash中文乱码

```
sudo gedit /etc/fonts/conf.d/49-sansserif.conf 
将倒数第四行 <string>sans-serif</string>
改为 <string>文泉驿正黑</string>
保存即可，重启firefox
```



### 增加农历

```
sudo apt-get install lunar-applet
鼠标点击面板右键 -> 添加到面板 -> 农历日期
```



### unzip 中文文件名乱码

```
unzip -O CP936 xxx.zip (用GBK, GB18030也可以)
或
/etc/environment中加入2行
UNZIP="-O CP936"
ZIPINFO="-O CP936"

注意：升级到ubuntu 9.10后，unzip会升级到6.0，而这个版本的unzip已经取消了-O选项，如果在/etc/environment中加入了-O的内容，会导致unzip不能正常解压。请注释掉这两行，然后重新登陆。
```

 



### ibus跟随

安装ibus-gtk即可,最好另外安装：ibus-qt4



## 文件管理



### 一屏查看文件内容

```
cat 文件名
```



### 不显示以＃开头的行

```
cat /etc/vsftpd.conf |grep -v ^#
```



### 分页查看文件内容

```
more 文件名
```



### 可控分页查看文件内容

```
less 文件名
```



### 带行号显示文件的内容

```
nl 文件名
cat -n 文件名
```



### 去除文件中的行号

```
cut -c 5- a.py
```



### 删除文件中的重复行

```
cat file.txt |sort -u
```



### 根据字符串匹配来查看文件部分内容

```
grep 字符串 文件名
```



### 显示包含或者不包含字符串的文件名

```
grep -l -r 字符串 路径　#显示内容包含字符串的文件名
grep -L -r 字符串 路径　#显示内容不包含字符串的文件名
find . -path './cache' -prune -o -name "*.php" -exec grep -l "date_cache[$format]['lang']" {} /; #显示当前目录下不包含cache目录的所有含有“date_cache[$format]['lang']”字符串的php文件。
```



### 快速查找某个文件

```
whereis filename
find 目录 -name 文件名
locate 文件名 # 注意，为了得到更好的效果，运行前可以更新下数据库，运行 sudo updatedb 即可，但这个命令每隔一段时间会自动运行，所以不用太在意
```



### 创建两个空文件

```
touch file1 file2
```



### 递归式创建一些嵌套目录

```
mkdir -pv /tmp/xxs/dsd/efd
```



### 递归式删除嵌套目录

```
rm -fr /tmp/xxs
```



### 回当前用户的宿主目录

```
cd ~
# 或者更简单的
cd
```



### 查看当前所在目录的绝对路经

```
pwd
```



### 获得文件的后缀名

```
echo xxx.xxx.rmvb |sed 's/.*/(/..*$/)//1/'
```



### 去除文件的后缀名

```
echo xxx.xxx.rmvb |sed 's//(.*/)/..*$//1/'
```



### 列出当前目录下的所有文件,包括以.开头的隐含文件

```
ls -a
```



### 移动路径下的文件并改名

```
mv 路径/文件  /新路径/新文件名
```



### 复制文件或者目录

```
cp -av 原文件或原目录 新文件或新目录
```



### 查看文件类型

```
file filename
```



### 对比两个文件之间的差异　

```
diff file1 file2
```



### 显示xxx文件倒数6行的内容

```
tail -6 xxx
```



### 让tail不停地读取最新的内容

```
tail -10f /var/log/apache2/access.log
```

或者

```
tailf /var/log/apache2/access.log
```



### 查看文件中间的第五行（含）到第10行（含）的内容

```
sed -n '5,10p' /var/log/apache2/access.log
```

 



### 查找关于xxx的命令

```
apropos xxx
man -k xxx
```



### 通过ssh传输文件

```
scp -rp /path/filename username@remoteIP:/path #将本地文件拷贝到服务器上
scp -rp username@remoteIP:/path/filename /path #将远程文件从服务器下载到本地
tar cvzf - /path/ | ssh username@remoteip "cd /some/path/; cat -> path.tar.gz" #压缩传输
tar cvzf - /path/ | ssh username@remoteip "cd /some/path/; tar xvzf -" #压缩传输一个目录并解压
```



### 把所有文件的後辍由rm改为rmvb

```
rename 's/.rm$/.rmvb/' *
```



### 把所有文件名中的大写改为小写

```
rename 'tr/A-Z/a-z/' *
```



### 删除特殊文件名的文件，如文件名：--help.txt

```
rm -- --help.txt 或者 rm ./--help.txt
```



### 查看当前目录的子目录

```
ls -d */. 或 echo */.
```



### 将当前目录下最近30天访问过的文件移动到上级back目录

```
find . -type f -atime -30 -exec mv {} ../back /;
```



### 查找当前目录下最近30天访问过的文件打包备份

```
find . -type f -atime -30 | xargs tar zcvpf backup.tar.gz
find . -type f -atime -30 -print -exec tar rvpf backup.tar {} /;
```



### 显示系统服务器一小时以内的包含 xxxx 的所有邮件

```
find /home/ -path "*Maildir*" -type f -mmin -60|xargs -i  grep -l xxxx '{}'
```



### 将当前目录下最近2小时到8小时之内的文件显示出来

```
find . -mmin +120 -mmin -480 -exec more {} /;
```



### 删除修改时间在30天之前的所有文件

```
find . -type f -mtime +30 -mtime -3600 -exec rm {} /;
```



### 删除创建时间在30天之前的所有文件

```
find . -type f -ctime +30 -ctime -3600 -exec rm {} /;
```



### 查找guest用户的以avi或者rm结尾的文件并删除掉

```
find . -name '*.avi' -o -name '*.rm' -user 'guest' -exec rm {} /;
```



### 查找不以java和xml结尾,并7天没有使用的文件删除掉

```
find . ! -name *.java ! -name ‘*.xml’ -atime +7 -exec rm {} /;
```



### 查找目录下所有有包含abcd文字的文本文件，并替换为xyz

```
grep -rIl "abcd" ./* --color=never | xargs sed -i "s/abcd/xyz/g" #注意grep的一个参数是大写的i，一个参数是小写的L
```



### 删除当前目录里面所有的 .svn 目录

```
find . -name .svn -type d -exec rm -fr {} /;
```



### 删除当前目录所有以“~”结尾的临时文件

```
find . -name "*~" -exec rm {} /;
```



### 统计当前文件个数

```
ls /usr/bin|wc -w
```



### 统计当前目录下所有jpg文件的尺寸

```
find . -name *.jpg -exec wc -c {} /;|awk '{print $1}'|awk '{a+=$1}END{print a}'
```



### 统计当前目录个数

```
ls -l /usr/bin|grep ^d|wc -l
```



### 统计当前目录下占空间最大的前10名文件或目录

```
du -sm * | sort -nr | head -10
```



### 显示当前目录下2006-01-01的文件名

```
ls -l |grep 2006-01-01 |awk '{print $8}'
```



### 备份当前系统到另外一个硬盘　

```
sudo rsync -Pa / /media/disk1 --exclude=/media/* --exclude=/home/* --exclude=/sys/* --exclude=/tmp/* --exclude=/proc/* --exclude=/mnt/*
```



### 使用ssh方式同步远程数据到本地目录

```
rsync -Pa -I --size-only --delete --timeout=300 Remote_IP:/home/ubuntu/back /backup
```



### 使用ftp方式同步远程数据到本地目录

```
lftp -c "open Remote_IP;user UserName Password;set cache:enable false;set ftp:passive-mode false;set net:timeout 15;mirror -e -c /back /backup;"
```



### 去掉文件中的^M

```
#注意不要使用同样的文件名，会清空掉原文件
cat filename | tr -d "^M" > newfile;
或者
sed -e "s/^M//g" filename > newfile;
或者
ex "+:%s/[Ctrl+V][Enter]//g" "+:wq"  filename #直接修改文件
```



### 转换Dos文本文件到Unix文本文件

```
tr -d '/15/32' < dosfile.txt > unixfile.txt                         #dos = > unix
awk '{ sub("/r$", ""); print }' dosfile.txt > unixfile.txt   #dos = > unix
awk 'sub("$", "/r")' unixfile.txt > dosfile.txt                  #unix = > dos
```



### 转换bin/cue到iso文件

```
#sudo apt-get install bchunk
bchunk image.bin image.cue image
```



### 转换目录到iso文件

```
mkisofs -o isofile.iso  dirname
```



### 转换CD到iso文件

```
dd if=/dev/cdrom of=isofile.iso
```



## 影像



### 如何保存串流视频(mms/rtsp)

```
mplayer -dumpfile dump.rm -dumpstream rstp://....
```



### 批量将rmvb转为avi

```
#ipod touch可用
for i in *.rmvb; do mencoder -vf harddup -oac mp3lame -lameopts vbr=3 -ovc xvid -xvidencopts fixed_quant=4 -of avi $i -o `echo $i | sed -e 's/rmvb$/avi/'`; done
```



### 批量将DVD转为avi

```
for i in *.VOB; do mencoder -oac mp3lame -lameopts aq=7:vbr=2:q=6 -srate 44100 -ovc xvid -xvidencopts fixed_quant=4 -of avi $i -o `echo $i | sed -e 's/VOB$/avi/'`; done
```



### 批量将任何格式的电影转为ogv

```
#sudo apt-get install ffmpeg2theora   
#firefox3.5或chrome直接支持播放，无需安装任何解码器，注意不支持rmvb，rmvb会出现a/v不同步问题
for i in *; do ffmpeg2theora --optimize --deinterlace $i; done
```



### 批量将rmvb格式的电影转为ogv

```
#!/bin/bash
for i in *; do
   mkfifo "/tmp/$i"
   mencoder -quiet -vf harddup -ovc raw -oac pcm -o "/tmp/$i" "$i" &
   ffmpeg2theora --optimize --deinterlace "/tmp/$i" -o "`echo $i | sed 's//(.*/)/..*$//1/'`.ogv"
   rm "/tmp/$i"
done
```



### 利用gst来转换任意电影为ogv

```
#!/bin/bash
for i in *; do
   gst-launch-0.10 filesrc location="$i" ! decodebin name=demux /
   { oggmux name=mux ! filesink location="`echo $i | sed 's//(.*/)/..*$//1/'`.ogv" } /
   { demux. ! queue ! audioconvert ! vorbisenc ! queue ! mux. } /
   { demux. ! queue ! ffmpegcolorspace ! videorate ! theoraenc ! mux. } 
done
```

 



### 批量转换任意文件为ipod touch使用的mp4

```
#!/bin/bash
#sudo apt-get install mencoder mplayer
find . -name '*.avi' -o -name '*.rm' -o -name '*.rmvb' -o -name '*.wmv' -o /
      -name '*.vob' -o -name '*.asf' -o -name '*.mpg' -o -name '*.ts' -o /
      -name '*.flv' -o -name '*.mpeg' -o -name '*.ogv' -o -name '*.mov' -o /
      -name '*.mkv' -o -name '*.dat' | while read i; do
  basename=`echo $i | sed 's//(.*/)/..*$//1/'`
  font="WenQuanYi Zen Hei"

  if [ -f "${basename}.srt" ] ; then
     cp "${basename}.srt" $$.srt
     m0="-vf scale=480:-10,harddup -sub $$.srt -unicode -subcp GB18030 -subfont-text-scale 3"; 
  else
     m0="-vf scale=480:-10,harddup"; 
  fi
 
  x0="-lavfopts format=mp4 -faacopts mpeg=4:object=2:raw:br=160 -oac faac -ovc x264 -sws 9 -x264encopts nocabac:level_idc=30:bframes=0:global_header:threads=auto:subq=5:frameref=6:partitions=all:trellis=1:chroma_me:me=umh:bitrate=500 -of lavf -ofps 24000/1001";
  mencoder $m0 -fontconfig -font "${font}" $x0 -o "$$.mp4" "$i"
  mv "$$.mp4" "${basename}.mp4"
  if [ -f "${basename}.mp4" ] ; then
     rm "$i"
  fi
done
```



### 图形界面为ipod touch转mp4的方法

先确保有zenity和memcoder

```
wget http://linuxfire.com.cn/~lily/toIpod
 -O ~/.gnome2/nautilus-scripts/toIpod && chmod +x ~/.gnome2/nautilus-scripts/toIpod
```

在nautilus里对视频文件点右键，选择"脚本"-"toIpod".

详情请查看[出处](javascript:void()) 。



### 合并多个 rm 文件为一个 avi 文件

```
mencoder -ovc lavc 1.rm -oac mp3lame -o 1.avi
mencoder -ovc lavc 2.rm -oac mp3lame -o 2.avi
mencoder -idx 1.avi -ovc copy -oac copy -o o1.avi
mencoder -idx 2.avi -ovc copy -oac copy -o o2.avi
cat o1.avi o2.avi | mencoder -noidx -ovc copy -oac copy -o output.avi -
```



### CD 抓轨为 mp3 (有损)

```
#sudo apt-get install abcde
abcde -o mp3 -b
```



### CD 抓轨为 Flac (无损)

```
#sudo apt-get install abcde
abcde -o flac -b
```



### ape 转换为 flac

```
#sudo apt-get install flac shntool iconv
iconv -f GB2312 -t UTF-8 example.cue -o example_UTF-8.cue
shntool split -t "%n.%p-%t" -f example_UTF-8.cue -o flac example.ape -d flacOutputDir
```



### ape/flac 转换为 mp3

```
#sudo apt-get install flac shntool lame 
#ape 需要安装  monkeys 包
iconv -f GB2312 -t UTF-8 example.cue -o example_UTF-8.cue
shntool split -t "%n.%p-%t" -f example_UTF-8.cue -o 'cust ext=mp3 lame --r3mix -b 320 --quiet - %f' example.ape -d mp3OutputDir
```



### 批量将 ape 转为 mp3

```
for i in *.ape; do shnconv -i ape -o "cust ext=mp3 lame -b 320 - %f" "$i" -d mp3OutputDir; done
```



### 批量将 flac 转为 mp3

```
for i in *.flac; do shnconv -i flac -o "cust ext=mp3 lame -b 320 - %f" "$i" -d mp3OutputDir; done
```



### 批量将svg转为png

```
for i in *; do inkscape $i --export-png=`echo $i | sed -e 's/svg$/png/'`; done
```



### 批量转换格式到mp3

```
#sudo apt-get install lame mplayer
for i in *; do base=${i%.*}; mplayer -quiet -vo null -vc dummy -af volume=0,resample=44100:0:1 -ao pcm:waveheader:file="$i.wav" "$i" ; lame -V0 -h -b 192 -vbr-new "$i.wav" "$base.mp3"; rm -f "$i.wav" ; done
```



### 批量缩小图片到30%

```
for i in *.jpg; do convert -resize 30%x30% "$i" "sm-$i"; done
```



### 批量转换jpg到png

```
for i in *; do convert $i `echo $i | sed -e 's/jpg$/png/'`; done
```



### 如何压缩png图片

```
#sudo apt-get install optipng
optipng -o7 old.png new.png
#或 sudo apt-get install pngcrush
#pngcrush -brute old.png new.png
```



### 批量把pdf转换为txt并格式化

```
sudo apt-get install poppler-utils poppler-data
find ./ -name '*.txt' | while read i; do cat $i | awk '{if ($0 ~ "^space:
”) {printf “/n”$0} else {printf $0}}’ | sed ‘/^space:
*digit:
*$/d’ | sed ’s/^space:
/+/    /’ | sed ’s/＂/”/g’ > “../txt/$i”; done
```



### 转换 pdf 到 png

```
#sudo apt-get install imagemagick
convert -density 196 FILENAME.pdf  FILENAME.png
```



### 获取jpg的扩展信息(Exif)

```
identify -verbose xxx.jpg
```



### 获取视频文件 xxx.avi 的信息

```
mplayer -vo null -ao null -frames 0 -identify "xxx.avi" 2>/dev/null | sed -ne '/^ID_/ { s/[]()|&;<>`'"'"'//!$" []///&/g;p }'
```



## 压缩



### 增加 7Z 压缩软件

```
#支持 7Z,ZIP,Zip64,CAB,RAR,ARJ,GZIP,BZIP2,TAR,CPIO,RPM,ISO,DEB 压缩文件格式
sudo apt-get install p7zip p7zip-full p7zip-rar
```



### 增加 rar 软件压缩和解压缩支持

```
sudo apt-get install rar unrar
```



### 解压缩 xxx.tar.gz

```
tar -zxvf xxx.tar.gz
```



### 解压缩 xxx.tar.bz2

```
tar -jxvf xxx.tar.bz2
```



### 压缩aaa bbb目录为xxx.tar.gz

```
tar -zcvf xxx.tar.gz aaa bbb
```



### 压缩aaa bbb目录为xxx.tar.bz2

```
tar -jcvf xxx.tar.bz2 aaa bbb
```



### 增加 lha 支持

```
sudo apt-get install lha
```



### 增加解 cab 文件支持

```
sudo apt-get install cabextract
```



## Nautilus



### 显示隐藏文件

```
Ctrl+h
```



### 显示地址栏

```
Ctrl+l
```



### 特殊 URI 地址

```
* computer:/// - 全部挂载的设备和网络
* network:/// - 浏览可用的网络
* burn:/// - 一个刻录 CDs/DVDs 的数据虚拟目录
* smb:/// - 可用的 windows/samba 网络资源
* x-nautilus-desktop:/// - 桌面项目和图标
* file:/// - 本地文件
* trash:/// - 本地回收站目录
* ftp:// - FTP 文件夹
* ssh:// - SSH 文件夹
* fonts:/// - 字体文件夹，可将字体文件拖到此处以完成安装
* themes:/// - 系统主题文件夹
```



### 查看已安装字体

```
fc-list
在nautilus的地址栏里输入”fonts:///“，就可以查看本机所有的fonts  (这个似乎不行)
```



## 日期和时间



### 显示日历

```
cal # 显示当月日历
cal 2 2007 # 显示2007年2月的日历
```



### 设置日期

```
date -s mm/dd/yy
```



### 设置时间

```
date -s HH:MM
```



### 将时间写入CMOS

```
hwclock --systohc
```



### 查看CMOS时间

```
 hwclock --show
```



### 读取CMOS时间

```
hwclock --hctosys
```



### 从服务器上同步时间

```
sudo ntpdate ntp.ubuntu.com
sudo ntpdate time.nist.gov
```



### 设置电脑的时区为上海

```
sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```



### XP 和 Ubuntu 相差了 8 小时的时差

```
#关闭UTC，将当前时间写入CMOS。
sudo sed -ie 's/UTC=yes/UTC=no/g' /etc/default/rcS
sudo hwclock --systohc
```



## 工作区



### 不同工作区间切换

```
Ctrl + ALT + ←
Ctrl + ALT + →
或者，将滚轮鼠标放在工作区图标上滚动
```



## 控制台



### 指定控制台切换

```
Ctrl + ALT + Fn(n:1~7)
```



### 控制台下滚屏

```
SHIFT + pageUp/pageDown
```



### 控制台抓图

```
setterm -dump n(n:1~7)
```

只是支持tty1-7。没中文。没颜色代码序列。



## 数据库



### mysql的数据库存放路径

```
/var/lib/mysql
```



### 从mysql中导出和导入数据

```
mysqldump 数据库名 > 文件名 #导出数据库
mysqladmin create 数据库名 #建立数据库
mysql 数据库名 < 文件名 #导入数据库
```



### 忘了mysql的root口令怎么办

```
sudo /etc/init.d/mysql stop
sudo mysqld_safe --skip-grant-tables &
sudo mysqladmin -u user password 'newpassword
sudo mysqladmin flush-privileges
```



### 修改mysql的root口令

```
sudo mysqladmin -u root -p password '你的新密码'
```

 



### 如何优化mysql

```
wget  http://www.day32.com/MySQL/tuning-primer.sh

chmod +x tuning-primer.sh
./tuning-primer.sh
```



### mysql命令行中文显示?号

```
mysql> set names utf8;
```



### 常用mysql管理语句

```
show table status;  #查询表状态
show full processlist;  #查询mysql进程
alter table site_stats engine=MyISAM;   #转换表为MyISAM类型，转表锁为行锁。
show variables;  ＃查看mysql 变量
```



## 控制



### 如何使用命令关闭显示器

```
xset dpms force off
```



### 设置CPU的频率

```
sudo apt-get install cpufrequtils 
#查看cpu当前频率信息
sudo cpufreq-info 
设置模式,对应于{最省电（最低频率），用户控制，最高或最低，正常，最大性能} 
cpufreq-set -g {powersave, userspace, ondemand, conservative, performance}
```



### 命令关机

```
sudo halt
sudo shutdown -h now              #现在关机
```



### 定时关机

```
sudo shutdown -h 23:00           #晚上11点自动关机
sudo shutdown -h +60          #60分钟后关机
```



### 命令重启电脑

```
sudo reboot
sudo shutdown -r now
```



### 如何修改ssh登录提示

```
sudo gedit  /etc/motd
```



### 如何关闭ssh登录提示

```
sudo gedit  /etc/ssh/sshd_config 修改这一行为： PrintLastLog no
```



### 如何使用命令关闭笔记本的触摸板

```
synclient touchpadoff=1
那么开启触摸板就是：
synclient touchpadoff=0
```



## 统计



### 统计最常用的10条命令

```
history | awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a;}' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl | head -n10 
```



### 统计每个单词的出现频率，并排序

```
awk '{arr[$1]+=1 }END{for(i in arr){print arr[i]"/t"i}}' FILE_NAME | sort -rn
```



### 统计80端口的连接个数并按照从大到小排列

```
netstat -na|grep :80|awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -r -n
```



## 编辑器



### vim中删除技巧

```
:%s/[Ctrl-v][Enter]//g 删除DOS方式的回车^M
:%s= *$== 删除行尾空白
:%!sort -u 删除重复行
:%s/^.{-}pdf/new.pdf/ 只是删除第一个pdf
:%s/// 删除多行注释
:g/^$/d 删除所有空行 
:g!/^dd/d 删除不含字符串'dd'的行
:v/^dd/d 删除不含字符串'dd'的行
:g/str1/,/str2/d 删除所有第一个含str1到第一个含str2之间的行
:v/./.,/./-1join 压缩空行
:g/^$/,/./-j 压缩空行
ndw 或 ndW 删除光标处开始及其后的 n-1 个字符。
d0 删至行首。
d$ 删至行尾。
ndd 删除当前行及其后 n-1 行。
x 或 X 删除一个字符。
Ctrl+u 删除输入方式下所输入的文本。
D 删除到行尾
x,y 删除与复制包含高亮区
dl 删除当前字符（与x命令功能相同）
d0 删除到某一行的开始位置
d^ 删除到某一行的第一个字符位置（不包括空格或TAB字符）
dw 删除到某个单词的结尾位置
d3w 删除到第三个单词的结尾位置
db 删除到某个单词的开始位置
dW 删除到某个以空格作为分隔符的单词的结尾位置
dB 删除到某个以空格作为分隔符的单词的开始位置
d7B 删除到前面7个以空格作为分隔符的单词的开始位置
d） 删除到某个语句的结尾位置
d4） 删除到第四个语句的结尾位置
d（ 删除到某个语句的开始位置
d） 删除到某个段落的结尾位置
d{ 删除到某个段落的开始位置
d7{ 删除到当前段落起始位置之前的第7个段落位置
dd 删除当前行
d/text 删除从文本中出现“text”中所指定字样的位置，一直向前直到下一个该字样所出现的位置（但不包括该字样）之间的内容
dfc 删除从文本中出现字符“c”的位置，一直向前直到下一个该字符所出现的位置（包括该字符）之间的内容
dtc 删除当前行直到下一个字符“c”所出现位置之间的内容
D 删除到某一行的结尾
d$ 删除到某一行的结尾
5dd 删除从当前行所开始的5行内容
dL 删除直到屏幕上最后一行的内容
dH 删除直到屏幕上第一行的内容
dG 删除直到工作缓存区结尾的内容
d1G 删除直到工作缓存区开始的内容
```



### 如何配置Emacs

打开新立得或者命令行，查找emacs， 选择你想要的版本，比如emacs22或者emacs21.

之后的配置参考[Emacs 常见问题及其解决方法](javascript:void())



### vim 如何显示彩色字符

```
sudo cp /usr/share/vim/vimcurrent/vimrc_example.vim /usr/share/vim/vimrc
```



### 让 vim 直接支持编辑 .gz 文件

```
sudo apt-get install vim-full
```



### vim 如何显示行号，在~/.vimrc中加入

```
set number
```

如果没有~/.vimrc文件，则可以在/etc/vim/vimrc中加入

```
set number
```



### vim配色方案 (~/.vimrc)

```
colorscheme scheme
可用的 scheme 在 /usr/share/vim/vim71/colors/ 
```



### 解决vim中文编码问题，在~/.vimrc中加入

```
let &termencoding=&encoding
set fileencodings=utf-8,gbk,ucs-bom,cp936
#再提供一个实践中觉得不错的配置：
" Encoding related
set encoding=UTF-8
set langmenu=zh_CN.UTF-8
language message zh_CN.UTF-8
set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1
set fileencoding=utf-8
```



### gedit中文乱码的解决

```
gconftool-2 --set /apps/gedit-2/preferences/encodings/auto_detected "[UTF-8,CURRENT,GB18030,BIG5,ISO-8859-15,UTF-16]" --type list --list-type string
```



## 编译和打包



### 安装通用编译环境

```
sudo apt-get install build-essential
```



### 通用的编译安装步骤

```
./configure && make && sudo make install
```



### 如何编译安装软件 kate

```
sudo apt-get install apt-build
sudo apt-build install kate
```



### 获得源代码包

```
sudo apt-get source mysql-server
```



### 解压缩还原源代码包

```
dpkg-source -x mysql-dfsg-5.1_5.1.30-1.dsc
```



### 安装编译打包环境

```
sudo apt-get build-dep mysql-server
```



### 重新编译并打包Debian化的源码

```
dpkg-buildpackage -rfakeroot
```



### 获得源码并重新打包

```
apt-get source php5-cgi  
#或手工下载源码后使用 dpkg-source -x  php5_5.2.6.dfsg.1-3ubuntu4.1.dsc 解开源码
sudo apt-get build-dep php5-cgi
cd php5-5.2.6.dfsg.1
dpkg-buildpackage -rfakeroot -uc -b
```



### 给源代码打补丁

```
patch -p0 < mysql.patch
```



## 其它



### 把终端加到右键菜单

```
sudo apt-get install nautilus-open-terminal 
```



### 如何删除Totem电影播放机的播放历史记录

```
rm ~/.recently-used
```



### 清除桌面挂载硬盘图标

```
gconftool-2 --set /apps/nautilus/desktop/volumes_visible 0 --type bool
```

恢复：

```
gconftool-2 --set /apps/nautilus/desktop/volumes_visible 1 --type bool
```



### 如何更换gnome程序的快捷键

```
点击菜单，鼠标停留在某条菜单上，键盘输入任意你所需要的键，可以是组合键，会立即生效；
如果要清除该快捷键，请使用backspace
```



### man 如何显示彩色字符

```
vim ~/.bashrc
#增加下面的内容：
export LESS_TERMCAP_mb=$'/E[01;31m'
export LESS_TERMCAP_md=$'/E[01;31m'
export LESS_TERMCAP_me=$'/E[0m'
export LESS_TERMCAP_se=$'/E[0m'
export LESS_TERMCAP_so=$'/E[01;44;33m'
export LESS_TERMCAP_ue=$'/E[0m'
export LESS_TERMCAP_us=$'/E[01;32m'
#生效文件
#source .bashrc
```



### 如何在命令行删除在会话设置的启动程序

```
cd ~/.config/autostart
rm 需要删除启动程序
```



### 如何提高wine的反应速度

```
sudo sed -ie '/GBK/,/^}/d' /usr/share/X11/locale/zh_CN.UTF-8/XLC_LOCALE
```


可能造成某些游戏帧数降低



### 如何命令行刻录

```
mkisofs -o test.iso -Jrv -V test_disk /home/carla/
cdrecord -scanbus
scsibus1:
 1,1,0 101) 'HL-DT-ST' 'CD-RW GCE-8481B ' '1.04' Removable CD-ROM
cdrecord -v -eject speed=8 dev=1,1,0 test.iso
```

[http://www.ibm.com/developerworks/cn/linux/l-cdburn/index.html](javascript:void())



### 延迟抓图

```
gnome-screenshot -d 10 #延迟10秒抓图
gnome-screenshot -w -d 5 #延迟5秒抓当前激活窗口
```



### 回收站在哪里

```
Ubuntu8.04 Gnome 2.22.2 以后在 $HOME/.local/share/Trash/
Ubuntu8.04 之前在 $HOME/.Trash/
```



### 强制清空回收站

```
sudo rm -fr $HOME/.local/share/Trash/files/
```



### 默认打开方式的配置文件在哪里

```
#全局
/etc/gnome/defaults.list 
#个人
~/.local/share/applications/mimeapps.list
```



### Firefox 的缓存目录在哪里

```
ls ~/.mozilla/firefox/*.default/Cache/
```



### 查看samba的用户

```
sudo pdbedit -L
```



### 增加一个用户到samba

```
sudo pdbedit -a username
```



### 从samba账户中删除一个用户

```
sudo pdbedit -x username
```



### 显示samba账户信息

```
sudo pdbedit -r username
```



### 测试samba账户是否正常

```
smbclient -L 192.168.1.1 -U username -d 3
```



### samba的数据库存在哪里

```
/var/lib/samba/passdb.tdb
```



### samba用户Windows下无法登录

```
在Windows的运行输入 cmd ，进入终端，输入 net use 命令，将显示已经连接的帐号如 //192.168.1.1/username
再输入 net use  //192.168.1.1/username /delete 删除现有的共享连接，再尝试进入另外一个目录就可以正常登录了。
```



### Pidgin 的聊天记录在哪里

```
~/.purple/logs/
```



### 安装PDF打印机

```
sudo apt-get install cups-pdf
#打印生成的pdf文件在 ~/PDF 文件夹里面
```



### nvidia快速重设显示设置及配置多显示器

```
sudo dpkg-reconfigure xserver-xorg
sudo nvidia-xconfig
#nvidia-settings 用于设置分辨率和多显示器
sudo nvidia-settings
```



### kacpid进程大量占用CPU

硬件驱动中不要激活无线网卡驱动即可