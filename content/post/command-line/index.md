---
title: Command Line develop
description: how to build a command line tool
date: 2022-06-20T13:25:51+08:00
image: cli.png
keywords: 
    - cobra
    - cli
    - flag
tags: 
    - cli
categories: 
    - dev
author: bob
draft: true
hidden: false
comments: true
---

# Cobra

cobra是一个用来创建命令行程序的扩展库 <https://github.com/spf13/cobra>。  

## cli

命令行程序由applicaton 和三个非必须部分command argument flag组成。  

- application
- command
- arguments
- flags

```shell

#application
git

#application command
git version

#application command flag
git version --build-options

#application command arguments
git clone https://github.com/signalwire/freeswitch.git

#application command flags arguments
git clone   -bv1.10 -v      https://github.com/signalwire/freeswitch.git    freeswitch 

app command flag1   flag2   argument1                                       argument2
git clone   -bv1.0  -v      https://github.com/signalwire/freeswitch.git    freeswitch

```

flags 格式可以参考:  
<https://pkg.go.dev/flag#hdr-Command_line_flag_syntax>  
<https://github.com/spf13/pflag#command-line-flag-syntax>  
<https://www.gnu.org/software/libc/manual/html_node/Argument-Syntax.html>  

```shell
-flag
-flag=x
-flag x     // non-boolean flags only

--flag
--flag=x
--flag x    // non-boolean flags only
```

## app

cobra提供了cobra-cli来自动生成application。  

```shell
go install github.com/spf13/cobra-cli@latest

mkdir app && cd app
go mod init github.com/xxx/app
cobra-cli init

#app directory
▾ app/
    ▾ cmd/
        root.go
      main.go

go run main.go

cobra-cli add server
cobra-cli add config
cobra-cli add create -p 'configCmd'

#app direcotry
▾ app/
    ▾ cmd/
        config.go
        create.go
        serve.go
        root.go
      main.go
```

## vscode

vscode在调试app时需在launch.json中新增args来传参。  
args传入app的command，argument，flag。  

```format
"args":["command","argument1","argument2","flag1","flag2"]
```

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch Package",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "program": "${fileDirname}",
            "args": ["fsconfig","/etc/myetc"]
        }
    ]
}
```
