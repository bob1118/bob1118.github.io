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

最快捷的方法就是去tag下载二进制文件了，[hugo with extended](https://github.com/gohugoio/hugo/releases)。

## 编译安装

可以参考hugo的[Readme.md](https://github.com/gohugoio/hugo/blob/master/README.md)。

在编译之前需要CGO支持，windowsx64下CGO需要用到 [MinGW-W64 GCC-8.1.0](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/8.1.0/threads-win32/sjlj/x86_64-8.1.0-release-win32-sjlj-rt_v6-rev0.7z)。访问 <https://sourceforge.net/projects/mingw-w64/files> 找需要的mingw win64版本。

```shell
go version
go version go1.15.2 windows/amd64

git clone https://github.com/gohugoio/hugo.git
cd hugo
go install --tags extended
...
Error: export ordinal too large: 78124

go install -a -x --tags extended
...
c:\go\pkg\tool\windows_amd64\link.exe: running g++ failed: exit status 1
../x86_64-w64-mingw32/bin/ld.exe: Error: export ordinal too large: 78124
collect2.exe: error: ld returned 1 exit status

go install -a -x -v -buildmode=exe --tags extended
...
cp $WORK\b001\exe\a.out.exe C:\Users\bob\go\bin\hugo.exe
```

编译的时候报告*Error: export ordinal too large: 78124*，参考 <https://github.com/golang/go/issues/40795> 需要加上-buildmode=exe，具体原因需要等待golang官方调查...

该问题已经在go1.16版本修复.

## ldflags

默认编译的文件比较大，可以使用-ldflags "-w -s"去掉调试信息。

``` shell
go install -a -x -v -ldflags "-w -s" --tags extended
```
