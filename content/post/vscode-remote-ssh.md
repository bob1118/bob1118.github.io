---
title: Vscode Remote Ssh
description: vscode remote development how to .
date: 2022-04-15T15:39:30+08:00
image: 
keywords: 
    - vscode
    - remote ssh
tags: 
    - vscode
    - ssh
categories: 
    - pub
author: bob
draft: true
hidden: false
comments: true
---

<!--more-->
# VSCODE远程开发

## 简介

微软家的开发工具从来不缺少远程开发，从二十年前的visual stuidio 6到现在的visual stuidio 2022都具有远程开发的功能。  
那么号称宇宙第一IDE的vscode当然不会例外，vscode对远程开发的支持通过扩展remote-ssh来实现。  
visualstudio的远程开发参考 <https://docs.microsoft.com/zh-cn/visualstudio/debugger/remote-debugging>  
vscode的远程开发参考 <https://code.visualstudio.com/docs/remote/remote-overview>

## 构架

我们看一下vscode远程开发的结构示意图

![arch](https://code.visualstudio.com/assets/docs/remote/remote-overview/architecture.png "vscode remote develop architecture")

LocalOS是vscode（remote-ssh）使用的客户端主机，RemoteOS是安装vscode server（具备真实开发环境的服务器）等服务端。  
我们操作LocalOS上的vscode通过ssh遥控RemoteOS上的代码，完成开发、调试工作。

## 目标

在LocalOS Mac上远程调试 RemoteOS debian11.3上的golang中间件。

### 准备工作

RemoteOS安装openssh-server，build-essential，golang(Debian11源中的golang版本比较老，可以从<https://golang.google.cn/dl/>下载新版本安装)。

```zsh
apt-get update && apt-get install -y openssh-server build-essential

wget https://golang.google.cn/dl/go1.18.1.linux-amd64.tar.gz
tar -zxvf ./go1.18.1.linux-amd64.tar.gz -C /usr/local/

echo -e "#\n# Golang GOROOT/bin\n export PATH+=:`/usr/local/go/bin/go env GOROOT`/bin" >>~/.bashrc
echo -e "#\n# Golang GOPATH/bin\n export PATH+=:`/usr/local/go/bin/go env GOPATH`/bin" >>~/.bashrc
source .bashrc

go env -w  GO111MODULE=on
go env -w  GOPROXY=https://goproxy.cn,direct

go version
go version go1.18.1 linux/amd64
```

LocalOS安装vscode和vscode扩展remote-ssh。

```zsh
brew update
brew install visual-studio-code
code --install-extension ms-vscode-remote.remote-ssh

code -v
1.66.2
arm64
```

LocalOS SSH生成~/.ssh/ssh_rsa和~/.ssh/ssh_rsa.pub。  
把ssh_rsa.pub加入到RemoteOS /root/.ssh/authorized_keys。  
LocalOS使用ssh_rsa登入root@remoteOS。

```zsh
ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/mymac/.ssh/id_rsa): ~/.ssh/ssh_rsa  
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 

scp ~/.ssh/ssh_rsa.pub bob@10.10.10.10:/home/bob/ssh_rsa.pub
...
ssh bob@10.10.10.10
...
su root -l
...
cat /home/bob/ssh_rsa.pub >>~/.ssh/authorized_keys
...
exit
...
exit
...
ssh -i .ssh/ssh_rsa root@10.10.10.10
root@d113:~#
```

### 小试牛刀

LocalOS使用密钥ssh到RemoteOS成功后就可以打开vscode使用远程开发了。  
接下来基本都是图形化操作，可以参考<https://code.visualstudio.com/docs/remote/ssh-tutorial>。  
对于每一个RemoteOS都可以有如下配置，配置保存在~/.ssh/config文件里。

```vim
Host debian remote server
  HostName 10.10.10.10
  User root
  ForwardAgent yes
  IdentityFile /Users/mymac/.ssh/ssh_rsa
```

在vscode中，我们SSH到一台RemoteOS就像我们本地LocalOS开发一样简单。
