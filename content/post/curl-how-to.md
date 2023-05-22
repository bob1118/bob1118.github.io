---
title: "Curl How To"
description: "how to debug http server"
date: 2023-05-14T10:44:13+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: true
---

# CURL

curl是一个linux下的命令行工具，基于libcurl开发。用来和服务器交互数据。  
curl支持多种协议(DICT, FILE, FTP, FTPS, GOPHER, GOPHERS, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET, TFTP, WS and WSS)。  
curl常用来模仿浏览器向服务器发起请求，方便在开发、测试中调试httd接口。  
要深入了解可以阅读在线文档<https://everything.curl.dev/>。  

## HTTP

参考<https://everything.curl.dev/http>结合man手册就可以上手。

比如curl模拟浏览器访问bing.com。

```shell
curl bing.com
```

请求后发现没有任何返回，我们带上-v(--verbose)看看交互细节。

```shell
curl bing.com -v
*   Trying 204.79.197.200:80...
* Connected to bing.com (204.79.197.200) port 80 (#0)
> GET / HTTP/1.1
> Host: bing.com
> User-Agent: curl/7.74.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 301 Moved Permanently
< Location: http://cn.bing.com/
< X-MSEdge-Ref: Ref A: F2AB225F32D7483E87030511DC816BB4 Ref B: SG1EDGE0109 Ref C: 2023-05-15T04:59:26Z
< Date: Mon, 15 May 2023 04:59:25 GMT
< Content-Length: 0
<
* Connection #0 to host bing.com left intact
```

从返回的头发现是重定向到cn.bing.com。于是访问cn.bing.com;或者访问bing.com带上 -L(\--localtion)。

```shell
curl cn.bing.com -v
*   Trying 204.79.197.200:80...
* Connected to cn.bing.com (204.79.197.200) port 80 (#0)
> GET / HTTP/1.1
> Host: cn.bing.com
> User-Agent: curl/7.74.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Cache-Control: private
< Content-Length: 16094
< Content-Type: text/html; charset=utf-8
...

curl bing.com -Lv
*   Trying 13.107.21.200:80...
* Connected to bing.com (13.107.21.200) port 80 (#0)
> GET / HTTP/1.1
> Host: bing.com
> User-Agent: curl/7.74.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 301 Moved Permanently
< Location: http://cn.bing.com/
< X-MSEdge-Ref: Ref A: 42181FE0EB5849FD87499B943D23289C Ref B: SG1EDGE0316 Ref C: 2023-05-15T05:17:20Z
< Date: Mon, 15 May 2023 05:17:20 GMT
< Content-Length: 0
<
* Connection #0 to host bing.com left intact
* Issue another request to this URL: 'http://cn.bing.com/'
*   Trying 204.79.197.200:80...
* Connected to cn.bing.com (204.79.197.200) port 80 (#1)
> GET / HTTP/1.1
> Host: cn.bing.com
> User-Agent: curl/7.74.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Cache-Control: private
< Content-Length: 16094
< Content-Type: text/html; charset=utf-8
...

```

bing.com返回16094字节内容,返回内容如果让浏览器解析，在浏览器里就能看到微软搜索引擎UI了。

### GET

```shell
// get
curl url
curl -X GET url
curl -Lv bing.com

// get with query
curl url?a=b&c=d
curl --url-query a=b --url-query c=d url
curl --get --data-urlencode a=b --data-urlencode c=d url
```

### POST

```shell
// post url query (--url-query)
curl -X POST --url-query a=b --url-query c=d url

// application/x-www-form-urlencoded (-d --data --data-urlencode )
curl -X POST -d a=b -d c=d  url
curl -X POST --data a=b --data c=d url
curl -X POST --data-urlencode a=b --data-urlencode c=d  url

// application/json
curl --data [arg] --header "Content-Type: application/json" --header "Accept: application/json" url
curl --json '{"tool": "curl"}' https://example.com/ (curl >=7.82.0)

// multipart/form-data (-F --form)
curl -X POST -F a=b -F c=d url

```

### PUT

```shell
//json
curl -X PUT  -H "Content-Type: application/json" -H "Accept: application/json" -d a=b -d c=d url

```

### DELETE

```shell
//json
curl -X DELETE -H "Accept: application/json" --url-query uuid=xxx url
curl -X DELETE -H "Accept: application/json" url?uuid=xxx
```

## 测试我的api

### get

- 请求10.10.10.20/api/v1，返回"default api v1 content"。

```shell
curl 10.10.10.20/api/v1
<a href="/api/v1/">Moved Permanently</a>.

curl 10.10.10.20/api/v1 -Lv
> GET /api/v1 HTTP/1.1
> Host: 10.10.10.20
> User-Agent: curl/7.74.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 301 Moved Permanently
< Content-Type: text/html; charset=utf-8
< Location: /api/v1/
< Date: Mon, 15 May 2023 05:55:33 GMT
< Content-Length: 43
<
* Ignoring the response-body
* Connection #0 to host 10.10.10.20 left intact
* Issue another request to this URL: 'http://10.10.10.20/api/v1/'
* Found bundle for host 10.10.10.20: 0x55af6299e9f0 [serially]
* Can not multiplex, even if we wanted to!
* Re-using existing connection! (#0) with host 10.10.10.20
* Connected to 10.10.10.20 (10.10.10.20) port 80 (#0)
> GET /api/v1/ HTTP/1.1
> Host: 10.10.10.20
> User-Agent: curl/7.74.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Content-Type: text/plain; charset=utf-8
< Date: Mon, 15 May 2023 05:55:33 GMT
< Content-Length: 22
<
* Connection #0 to host 10.10.10.20 left intact
default api v1 content
```

- 请求10.10.10.20/api/v1/api?cmd=xxx，返回。

```shell
curl 10.10.10.20/api/v1/api?cmd=status
UP 0 years, 0 days, 5 hours, 39 minutes, 32 seconds, 931 milliseconds, 598 microseconds
FreeSWITCH (Version 1.10.9 -release-21-a615e85afc 64bit) is ready
0 session(s) since startup
0 session(s) - peak 0, last 5min 0
0 session(s) per Sec out of max 30, peak 0, last 5min 0
1000 session(s) max
min idle cpu 0.00/99.63
Current Stack Size/Max 240K/8192K

curl 10.10.10.20/api/v1/api?cmd=sofia%20status
curl 10.10.10.20/api/v1/api --url-query 'cmd=sofia status'
                     Name          Type                                       Data      State
=================================================================================================
            external-ipv6       profile                   sip:mod_sofia@[::1]:5080      RUNNING (0)
                 mydomain         alias                                   internal      ALIASED
                 external       profile             sip:mod_sofia@10.10.10.20:5080      RUNNING (0)
    external::example.com       gateway                    sip:joeuser@example.com      NOREG
        external::vos_out       gateway                        sip:username@vos.ip      NOREG
         external::vos_in       gateway                        sip:username@vos.ip      NOREG
    external::myfsgateway       gateway                      sip:1000@10.10.10.200      NOREG
            external::p2p       gateway                      sip:FreeSWITCH@p2p.ip      NOREG
              10.10.10.20         alias                                   internal      ALIASED
            internal-ipv6       profile                   sip:mod_sofia@[::1]:5060      RUNNING (0)
                 internal       profile             sip:mod_sofia@10.10.10.20:5060      RUNNING (0)
=================================================================================================
4 profiles 2 aliases
```

- 请求10.10.10.20/api/v1/accounts?id=8000和10.10.10.20/api/v1/accounts?id=8000&domain=mydomain返回accounts并json_pp格式化。

```shell
curl 10.10.10.20/api/v1/accounts?id=8000|json_pp
...
{
   "code" : {
      "rtcode" : 0,
      "rtmsg" : ""
   },
   "data" : {
      "len" : 2,
      "lists" : [
         {
            "a1hash" : "",
            "auth" : "8000",
            "cacheable" : "",
            "domain" : "10.10.10.20",
            "group" : "default",
            "id" : "8000",
            "name" : "8000",
            "password" : "8000",
            "proxy" : "10.10.10.20",
            "uuid" : "5fd443ed-c829-4053-9309-b9715f010c1d"
         },
         {
            "a1hash" : "",
            "auth" : "8000",
            "cacheable" : "",
            "domain" : "mydomain",
            "group" : "default",
            "id" : "8000",
            "name" : "8000",
            "password" : "8000",
            "proxy" : "10.10.10.20",
            "uuid" : "a6df52df-f570-4a29-89a0-530b3a8a8e09"
         }
      ]
   }
}

 curl --url-query id=8000 --url-query domain=mydomain 10.10.10.20/api/v1/accounts|json_pp
 ...
{
   "code" : {
      "rtcode" : 0,
      "rtmsg" : ""
   },
   "data" : {
      "len" : 1,
      "lists" : [
         {
            "a1hash" : "",
            "auth" : "8000",
            "cacheable" : "",
            "domain" : "mydomain",
            "group" : "default",
            "id" : "8000",
            "name" : "8000",
            "password" : "8000",
            "proxy" : "10.10.10.20",
            "uuid" : "53699506-c953-4c09-8a9a-dc76be6e902d"
         }
      ]
   }
}


```

### post

- form-urlencode

```shell
//--url-query
curl -X POST --url-query numberstart=87654321 --url-query numberend=87654325 -H Content-Type:application/json http://10.10.10.20/api/v1/e164s|json_pp

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   679  100   679    0     0  96944      0 --:--:-- --:--:-- --:--:--  110k
{
   "code" : {
      "rtcode" : 0,
      "rtmsg" : ""
   },
   "data" : {
      "len" : 5,
      "lists" : [
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "87654321",
            "uuid" : "5160e5b2-408c-46b1-9419-137cad1bf8cf"
         },
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "87654322",
            "uuid" : "149d873a-856b-497d-a35b-5d794cb8b84c"
         },
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "87654323",
            "uuid" : "25b228fe-6609-45d1-9884-4cf7fa7c91fd"
         },
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "87654324",
            "uuid" : "8c3c6e8f-6537-4fea-bea9-ba4408088557"
         },
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "87654325",
            "uuid" : "67a18b77-3f1f-42fa-b20d-bdaa71a92425"
         }
      ]
   }
}

//form-urlencoded
curl -X POST -d a=b -d c=d  url

```

- json

```shell
//json
curl --data [arg] --header "Content-Type: application/json" --header "Accept: application/json" url
curl --json '{"tool": "curl"}' https://example.com/ (curl >=7.82.0)

curl -X POST -d'{"number":"12345678"}'  --header "Content-Type: application/json" --header "Accept: application/json" 10.10.10.20/api/v1/e164 -v|json_pp
curl --json  '{"number":"1234567890"}'  --header "Content-Type: application/json" --header "Accept: application/json" 10.10.10.20/api/v1/e164 -v|json_pp
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 10.10.10.20:80...
* Connected to 10.10.10.20 (10.10.10.20) port 80 (#0)
> POST /api/v1/e164 HTTP/1.1
> Host: 10.10.10.20
> User-Agent: curl/7.88.1
> Content-Type: application/json
> Accept: application/json
> Content-Length: 23
>
} [23 bytes data]
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< Date: Wed, 17 May 2023 15:31:02 GMT
< Content-Length: 185
<
{ [185 bytes data]
100   208  100   185  100    23  30766   3825 --:--:-- --:--:-- --:--:-- 41600
* Connection #0 to host 10.10.10.20 left intact
{
   "code" : {
      "rtcode" : 0,
      "rtmsg" : ""
   },
   "data" : {
      "len" : 1,
      "lists" : [
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "1234567890",
            "uuid" : "8fad3099-040e-4688-8632-0dd03fd5391a"
         }
      ]
   }
}

```

- form-data

```shell
curl -X POST -F a=b -F c=d url
```

### put

```shell
//json
curl -X PUT  -H "Content-Type: application/json" -H "Accept: application/json" -d a=b -d c=d url
curl -X PUT --json '{"gname":"","number":"000000001","enable":true,"lockin":false,"lockout":false}' -H "Content-Type: application/json" -H "Accept: application/json" -Lv  10.10.10.20/api/v1/e164/8fad3099-040e-4688-8632-0dd03fd5391a|json_pp
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 10.10.10.20:80...
* Connected to 10.10.10.20 (10.10.10.20) port 80 (#0)
> PUT /api/v1/e164/8fad3099-040e-4688-8632-0dd03fd5391a HTTP/1.1
> Host: 10.10.10.20
> User-Agent: curl/7.88.1
> Content-Type: application/json
> Accept: application/json
> Content-Length: 78
>
} [78 bytes data]
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< Date: Wed, 17 May 2023 16:07:14 GMT
< Content-Length: 184
<
{ [184 bytes data]
100   262  100   184  100    78  20852   8839 --:--:-- --:--:-- --:--:-- 32750
* Connection #0 to host 10.10.10.20 left intact
{
   "code" : {
      "rtcode" : 0,
      "rtmsg" : ""
   },
   "data" : {
      "len" : 1,
      "lists" : [
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "000000001",
            "uuid" : "8fad3099-040e-4688-8632-0dd03fd5391a"
         }
      ]
   }
}
```

### delete

```shell
//json
curl -X DELETE -H "Accept: application/json" url

curl -X DELETE -H "Content-Type: application/json" -H "Accept: application/json" 10.10.10.20/api/v1/e164/8c3c6e8f-6537-4fea-bea9-ba4408088557 |json_pp
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   183  100   183    0     0  37492      0 --:--:-- --:--:-- --:--:-- 45750
{
   "code" : {
      "rtcode" : 0,
      "rtmsg" : ""
   },
   "data" : {
      "len" : 1,
      "lists" : [
         {
            "enable" : true,
            "gname" : "",
            "lockin" : false,
            "lockout" : false,
            "number" : "87654324",
            "uuid" : "8c3c6e8f-6537-4fea-bea9-ba4408088557"
         }
      ]
   }
}
```
