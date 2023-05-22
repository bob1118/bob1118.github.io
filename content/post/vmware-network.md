---
title: "Vmware Network"
date: 2021-07-14T20:26:17+08:00
lastmod: 2021-07-14T20:26:17+08:00
draft: false
keywords: [vmware]
description: "virtual machine networking missing"
tags: [vmware]
categories: [pub]
author: "bob"

---

<!--more-->
# Vmware Network

## 环境

本地机器两个千兆网卡，在本地机器上安装win10，VMwareWorkstation16，还有一些日常软件。  
在VMware上创建一台虚拟机，网络桥接到本地，虚拟机DHCP获取IP地址上网。发现虚拟机的网络经常出现获取不到IP地址。

## 可能的原因

### DHCP Server

dhcp server 使用RouterOS v6.48.3 dhcp package，dhcp client只有虚拟机上时好时坏，其他的dhcp client正常使用。在winbox里查看dhcp client的状态，在等待client发包。

### DHCP Client

dhcp client 使用命令ifdown,ifup皆不能从dhcp server收到任何回应。

## 罪魁祸首

Vmware->编辑->虚拟网络编辑器->更改设置  
把默认的已桥接至(自动)改成(目标网卡),应用保存就可以了。