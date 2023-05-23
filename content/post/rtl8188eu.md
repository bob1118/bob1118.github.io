---
title: Rtl8188eu
description: Realtek rtl8188eu wireless Lan USB Network Adapter sniffer.
date: 2022-05-25T15:49:34+08:00
image: 
keywords: 
    - wlan wifi
    - airport interface sniffer
tags: 
    - wlan
    - wifi
    - iw
categories: 
    - pub
author: bob
draft: true
hidden: false
comments: true
---
<!--more-->
# Realtek rtl8188eu

在抽屉翻出来个十年前的USB无线网卡，怼到windows计算机的usb口上，直接使用即可（windows10带8188eu驱动程序）。
切换一下系统到debian，默认是不能驱动的。于是到realtek官方网站看看有没有新的驱动，翻遍了网站也没找到下载链接。

## Debian

在debian环境下，realtek的驱动和固件在non-free里面。
打开sources.list，在main后面加入non-free search一下 rtl8188eu(源里面需支持non-free，否则要切换带nong-free的安装源)。  
安装驱动firmware-realtek，ip link查看网卡，显示wlx0c826830b763网卡。  
安装wpasupplicant，在wlx0c826830b763连接到SSID时需要wpa辅助wpa key协商。  
打开/etc/network/interfaces，为wlx0c826830b763网卡添加参数。  
ifup wlx0c826830b763，连ssid，获取ip完成联网。

```shell

echo /etc/apt/sources.list
deb https://mirrors.bfsu.edu.cn/debian/ bullseye main contrib non-free
...

apt-get update && apt-cache search rtl8188eu
firmware-realtek - Binary firmware for Realtek wired/wifi/BT adapters
apt-get -y install firmware-realtek
...

ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: wlx0c826830b763: <BROADCAST,MULTICAST> mtu 2312 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 0c:82:68:30:b7:63 brd ff:ff:ff:ff:ff:ff

apt-get install wpasupplicant

cat /etc/network/interfaces
...
auto  wlx0c826830b763
iface wlx0c826830b763 inet dhcp
wpa-ssid myssid
wpa-psk  mypswd

ifup wlx0c826830b763
...
```

## wireless modes

### AP

AP(AccessPoint infrastructure mode)，我们常用的热点就可以叫ap。

### Station

Station连接到AP，成为WIFI网络的一部分。我们常用的手机、计算机、等终端。

### Monitor

监听模式，只进不出，用来分析网络状况。

### Ad-Hoc（IBSS）

IBSS(Independent Basic Service)，点对点模式。

### WDS

Wireless Distribution System，有无线桥接和无线中继两种。

### mesh

多跳无线网格，mesh网络是一种基于多跳路由和对等网络的技术。

## iw

iw是管理802.11设备的命令行工具，支持被添加进内核的的驱动程序管理和配置。

iw list 列出所有的wireless设备的参数。  
iw dev wlx0c826830b763 scan 扫描AP。  
iw dev wlx0c826830b763 link 获取链路状态。  
iw wlx0c826830b763 connect myssid 连接到myssid。  
...

```shell
apt-get update && apt-get install iw

iw list
...

iw dev
        Interface wlx0c826830b763
                ifindex 2
                wdev 0x200000001
                addr 0c:82:68:30:b7:63
                ssid hello
                type managed
                txpower 13.00 dBm

iw dev wlx0c826830b763 link
Connected to c0:bc:9a:15:d3:50 (on wlx0c826830b763)
        SSID: hello
        freq: 2422
        signal: -82 dBm
        tx bitrate: 65.0 MBit/s

...
```

## aircrack-ng

aircrack-ng是无线网络的安全套件包。  
主要致力于以下四个领域。

1. 监控，数据包捕获、导出、分析。
2. 渗透，数据包注入，取消身份验证，虚拟接入点。
3. 测试，网卡和驱动功能的测试。
4. 破解，WEP和WPA PSK（WPA1和WPA2）

### 开始前

在使用aircrack-ng套件之前需要检查无线网卡的芯片组是否兼容。  
检查了一下rtl8188eu在kernel.org中仅能做为station，不支持ap、ibss、mesh、monitor。  

<https://wireless.wiki.kernel.org/en/users/drivers>  
<http://linux-wless.passys.nl/>  

### 新的驱动

linux firmware 中rtl8188eu就是个普通的上网卡，其他方式是不可行的。  
在aircrack-ng上翻了翻，找到了支持mesh、monitor和frame injiection的新驱动<https://github.com/aircrack-ng/rtl8188eus>。  
按照README一路安装驱动程序，注意这里的仅仅是驱动程序，固件还是使用前面non-free里面的bin文件。

```shell 安装驱动程序
apt-get update && apt-get upgrade && apt-get dist-upgrade
apt-get install linux-headers-$(uname -r)
apt get install git build-essential libelf-dev dkms bc 
echo 'blacklist r8188eu'|sudo tee -a '/etc/modprobe.d/realtek.conf'
rmmod r8188eu.ko
reboot

cd usr/src
git clone https://github.com/aircrack-ng/rtl8188eus.git
cd rtl8188eus && make && make install

lsmod|grep 8188
8188eu               1249280  0
cfg80211              970752  1 8188eu
usbcore               323584  5 ehci_pci,usbhid,8188eu,ehci_hcd,uhci_hcd

ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: wlx0c826830b763: <BROADCAST,MULTICAST> mtu 2312 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 0c:82:68:30:b7:63 brd ff:ff:ff:ff:ff:ff

iw dev 
phy#0
        Interface wlx0c826830b763
                ifindex 2
                wdev 0x1
                addr 0c:82:68:30:b7:63
                type managed
                txpower 13.00 dBm

ifup wlx0c826830b763
Internet Systems Consortium DHCP Client 4.4.1
Copyright 2004-2018 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/wlx0c826830b763/0c:82:68:30:b7:63
Sending on   LPF/wlx0c826830b763/0c:82:68:30:b7:63
Sending on   Socket/fallback
DHCPDISCOVER on wlx0c826830b763 to 255.255.255.255 port 67 interval 6
DHCPDISCOVER on wlx0c826830b763 to 255.255.255.255 port 67 interval 6
DHCPOFFER of 192.168.1.221 from 192.168.1.1
DHCPREQUEST for 192.168.1.221 on wlx0c826830b763 to 255.255.255.255 port 67
DHCPACK of 192.168.1.221 from 192.168.1.1
bound to 192.168.1.221 -- renewal in 1407 seconds.

```

网卡做为statsion可以正常上网了。新驱动支持其他几种模式，我们下来安装aircrack-ng来看看。

### 安装

aircrack-ng可以编译安装也可以下安装包。  
<https://github.com/aircrack-ng/aircrack-ng.git>  
<https://packagecloud.io/aircrack-ng/release>  

```shell install aircrack-ng
wget --content-disposition https://packagecloud.io/aircrack-ng/release/packages/any/any/aircrack-ng_1.7-1_amd64.deb/download.deb
apt-get install wireless-tools
dpkg -i aircrack-ng_1.7-1_amd64.deb

```

### 监控

断开刚才的station连接，设置网卡处于monitor模式，扫描周边网络。

```shell
airmon-ng check kill
ip link set wlx0c826830b763 down
iw dev wlx0c826830b763 set type monitor

iw dev
phy#0
        Interface wlx0c826830b763
                ifindex 2
                wdev 0x100000001
                addr 0c:82:68:30:b7:63
                type monitor
                txpower 13.00 dBm

airodump-ng wlx0c826830b763

```
