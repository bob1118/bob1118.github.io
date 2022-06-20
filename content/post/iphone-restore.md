---
title: "Iphone Restore"
date: 2020-09-20T16:10:22+08:00
lastmod: 2020-09-20T16:10:22+08:00
draft: true
keywords: []
description: ""
tags: [iphone]
categories: [pub]
author: "bob"

---

<!--more-->
# IOS14

自从IOS 14发布后，很多人都去体验[IOS 14 新特性](https://www.apple.com.cn/ios/ios-14/features/)。

## OTA升级

根据官方建议[https://support.apple.com/zh-cn/ios/update](https://support.apple.com/zh-cn/ios/update)

* Iphone->设置->通用->软件更新

我也尝试升级到了IOS14，总体效果还是值得赞赏的。听了一下午网易云音乐，后盖烫的厉害，尝试DFU恢复一下看看。

## iTunes

手机自身无法更新或恢复的时候，借助[Itunes](https://www.apple.com/itunes/download/win64)来恢复，参考[用Itunes来恢复](https://support.apple.com/zh-cn/HT201263)。

### recovery模式

recovery模式是apple官方给出的刷机方式，进入恢复模式后屏幕显示笔记本和一根线缆。  
操作步骤如下：

* 连接iphone到计算机usb
* 在计算机中运行Itunes（Itunes版本最新）
* 按下调高音量按钮再快速松开。按下调低音量按钮再快速松开。按住开机按钮，直到屏幕变黑开始重新启动。
* 继续按住开机按钮，直到设备进入恢复模式。(适用iphone8以上版)
* Itunes弹出“iphone出现问题需要更新或恢复”，点恢复下载ios14.ispw恢复Iphone。

### dfu模式

Device firmware Upgrade mode，进入dft模式后屏幕显示黑色。  
操作步骤如下：  

* 连接iPhone到计算机usb
* 在计算机中运行iTunes
* 按下音量调高按钮再快速松开。按下音量调低按钮再快速松开。按住开机按钮，直到屏幕变黑开始重新启动。
* 按住开机按钮和量调低按钮，几秒钟后松开开机按钮，再过几秒钟松开音量调低按钮，进入dfu模式。
* Itunes弹出“iphone出现问题需要更新或恢复”，点恢复下载ios14.ispw恢复Iphone。
