---
title: "Macbook Pro 2021"
date: 2021-12-10T16:52:16+08:00
lastmod: 2021-12-10T16:52:16+08:00
draft: false
keywords: [macbook pro 2021, macbook pro M1 pro, macbook pro M1 max]
description: "Apple new macbook pro 2021 release."
tags: [mac]
categories: [pub]
author: "bob"

---

<!--more-->
# Macbook Pro 2021

2021年10月19日凌晨1点，apple召开了秋季线上发布会。  
大家期待的arm M2 soc并没有到来，新发布的arm soc采用堆核心，发布14inch和16inch的macbook pro。  
自己的使用对GPU要求不高，CPU核心提高为前提，拿到了macbook pro 14inch 10coreCPU/14coreGPU的机器。  
这是第一次使用apple家的macbook，macbook pro 2021上手使用两个礼拜的一些情况。

## CPU/GPU

1. 2020 M1     (2+2)Firestorm + (2+2)icestorm CPU/8 core GPU
2. 2021 M1 pro (2+2+2+2)Firestorm + 2icestorm CPU/14～32 core GPU
3. 2021 M1 max (2+2+2+2)Firestorm + 2icestorm CPU/14～32 core GPU

## 主要用途

以码字、后端API编译为主，影音为辅，以后可能需要处理点视频素材。

## 必备软件

### Homebrew

homebrew是mac上开源包管理器。官方安装提供了脚本，打开mac默认shell zsh执行脚本就可以安装（系统会提示安装git，按提示安装即可）。

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
…
curl: (35) LibreSSL SSL_connect: Connection reset by peer in connection to raw.githubusercontent.com:443
```

如果github访问受阻，可以使用国内的源，根据提示完成安装。源推荐使用bfsu。  
基于git安全考虑，需要设置brew的几个目录。

```shell
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
...

git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-core
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-cask
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-services

brew -v
Homebrew 3.5.1-48-g4484068
Homebrew/homebrew-core (git revision c5e07e21cf2; last commit 2022-06-13)
Homebrew/homebrew-cask (git revision 729a0c47c3; last commit 2022-06-13)

brew update -v
Already up-to-date.
```


### Oh-my-zsh

oh-my-zsh是zsh的个性化配置管理器，默认安装就可以。  
`sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

### Vim

```zsh
cp /usr/share/vim/vimrc ~/.vimrc
vim ~/.vimrc
syntax on
set cursorline
set autoindent
```

### Golang

```zsh
brew install golang
go version
go version go1.17.3 darwin/arm64

go env -w  GO111MODULE=on
go env -w  GOPROXY=https://goproxy.cn,direct
go env -w  GOBIN=`go env GOPATH`/bin

echo '  export PATH=$(go env GOBIN):$PATH' >>~/.zprofile
```

### Visual-studio-code

先用brew安装vscode，打开vscode，设置同步一下插件就可以使用了。  

```zsh
brew install visual-studio-code
```

### SshPrivatekey

mac对key的保护比较严格，一定要设置下key的权限，只能允许当前用户读取。

```zsh
mkdir -p ~/.ssh
cp ~/Downloads/.ssh/id_rsa ~/.ssh
chmod 400 ~/.ssh/id_rsa
ssh -T git@github.com
```

### Postgresql

安装一下postgresql数据库和dbeaver数据库图形管理软件（登入postgre数据库以默认登入系统的用户登入）。

```zsh
brew install postgresql
brew services restart postgresql
brew install dbeaver-community
...
```

## 其他软件

使用brew安装就可以，比如utm，sourcetree，freeplane，libreoffice，firefox，iina，keka，wechat，ximalaya，downie，thunder，qbittorrent等。
