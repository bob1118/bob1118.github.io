---
title: Install Hugo With Extended
description: Install Hugo With Extended
date: 2020-09-22T13:27:37+08:00
image: 
keywords: 
    - hugo
    - hugo extended
tags: 
    - Hugo
categories: 
    - pub
author: bob
draft: true
hidden: false
comments: true
---

<!--more-->
# hugo

hugo 在windows X64平台支持Sass/SCCS。

## 快捷安装

最快捷的方法就是去tag下载二进制文件了，[hugo with extended](https://github.com/gohugoio/hugo/releases)。在下载页面寻找hugo_extended_version_os_arch下载。

## 编译安装

可以参考hugo的[Readme.md](https://github.com/gohugoio/hugo/blob/master/README.md)。  
hugo extended 在编译之前需要CGO支持，在命令行运行
```shell
go env -w CGO_ENABLED=1
```
在 https://www.mingw-w64.org/downloads/ 下载支持windows_x64的二进制构建包[mysys2](https://github.com/msys2/msys2-installer/releases/download/2024-01-13/msys2-x86_64-20240113.exe)。  
按照mysys2安装说明[mysys2 started](https://www.msys2.org/)安装mysys2。  
打开 MYSYS2 UCRT64，安装支持windows x64 ucrt gcc。
```shell
pacman -Syu
pacman -S mingw-w64-ucrt-x86_64-gcc

gcc -v
Using built-in specs.
COLLECT_GCC=C:\msys64\ucrt64\bin\gcc.exe
COLLECT_LTO_WRAPPER=C:/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/13.2.0/lto-wrapper.exe
Target: x86_64-w64-mingw32
...
Thread model: posix
Supported LTO compression algorithms: zlib zstd
gcc version 13.2.0 (Rev6, Built by MSYS2 project) 

```
安装完mingw-w64-ucrt-x86_64-gcc后，在windows系统环境变量PATH里面添加C:\msys64\ucrt64\bin。让go编译的时候能找到gcc。  
下面开始下载代码进行编译。
```shell
go version
go version go1.15.2 windows/amd64

git clone https://github.com/gohugoio/hugo.git
cd hugo
go install --tags extended
...
Error: export ordinal too large: 78124

go install -a -x -tags extended
...
c:\go\pkg\tool\windows_amd64\link.exe: running g++ failed: exit status 1
../x86_64-w64-mingw32/bin/ld.exe: Error: export ordinal too large: 78124
collect2.exe: error: ld returned 1 exit status

go install -a -x -v -buildmode=exe -tags extended
...
cp $WORK\b001\exe\a.out.exe C:\Users\bob\go\bin\hugo.exe
```

编译的时候报告*Error: export ordinal too large: 78124*，参考 <https://github.com/golang/go/issues/40795> 需要加上-buildmode=exe，具体原因需要等待golang官方调查...

该问题已经在go1.16版本修复.

## ldflags

默认编译的文件比较大，可以使用-ldflags "-w -s"去掉调试信息。

``` shell
go install -v -ldflags "-w -s" -tags extended
```

需要全编译加上 -a即可。
```shell 
go install -a -x -v -ldflags "-w -s" -tags extended
```
