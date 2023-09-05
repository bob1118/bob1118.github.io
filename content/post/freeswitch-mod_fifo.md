---
title: Freeswitch Mod_fifo
description: Freeswitch Mod_fifo
date: 2023-07-06T16:50:58+08:00
image: 
keywords: 
    - mod_fifo
    - mod_acd
    - 排队
tags: 
    - mod_fifo
    - mod_acd
categories: 
    - pub
author: bob
draft: true
hidden: false
comments: true
---

# MOD_FIFO

freeswitch mod_fifo 是一个做做排队的app，允许您使用分配的优先级创建自定义的呼叫队列。  
在使用mod_fifo时，一般是caller(profile external的incoming channel/outgoing channel)执行app命中坐席的过程。  

坐席定义了两种(consumer和member):

- consumer是坐席先呼入，被应答，登入队列保持等待。consumer等待caller提供通道服务。
- member是在配置fifo.conf.xml中定义或fifo_member动态增减。member等待caller。

## 用法

fifo app 在caller channel 执行。

```shell
<fifo name>[!<importance_number>] [in [<announce file>|undef] [<music file>|undef] | out [wait|nowait] [<announce file>|undef] [<music file>|undef]]

//dialplan take a caller out of FIFO queue.
<action application="fifo" data="myqueue out nowait /tmp/caller-found.wav /tmp/agent-music-on-hold.wav"/>

//dialplan put a caller into a FIFO queue.
<action application="fifo" data="myqueue in /tmp/exit-message.wav /tmp/music-on-hold.wav"/>
```
