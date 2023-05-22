---
title: "Mac Port 8021"
date: 2021-12-13T14:51:09+08:00
lastmod: 2021-12-13T14:51:09+08:00
draft: false
keywords: [mac tcp port 8021,mac udp port 8021,mac port 8021]
description: "mac port already used."
tags: [mac]
categories: [pub]
author: "bob"

---

<!--more-->
# Mac port 8021

在mac上调试程序的时，偶尔碰到tcp端口8021被系统占用，调试失败。  
用lsof -Pi:8021看看PID，kill就可以了。碰到不能kill的需要进一步调查，然后得到如下命令，下次碰到被占用时试试。  
`sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.ftp-proxy.plist`

## 谁占用了8021

查看了mac上的[*端口占用表*](https://support.apple.com/zh-cn/HT202944 "Apple 软件产品使用的 TCP 和 UDP 端口")，表格里面没有8021是那个应用占用。  
用lsof查看一下占用8021端口的程序。

```zsh
sudo lsof -Pi:8021
...
sudo kill -9 1
kill: 1: Operation not permitted

```

kill pid 1失败。了解了一下launchd，mac上的launchd和linux上的initd，systemd类似。  
在网上搜了一会儿相同的问题，找到一篇类似问题的[*解决方案*](<https://apple.stackexchange.com/questions/404346/what-is-intu-ec-client-listening-on-tcp-port-8021/404356#404356> "What is `intu-ec-client` listening on TCP port 8021?").  
/System/Library/LaunchDaemons/com.apple.ftp-proxy.plist文件中定义了8021端口。

## 如何释放8021

```zsh
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.ftp-proxy.plist
```

## 附录

*<https://support.apple.com/zh-cn/HT202944>*  
*<https://apple.stackexchange.com/questions/404346/what-is-intu-ec-client-listening-on-tcp-port-8021/404356#404356>*
