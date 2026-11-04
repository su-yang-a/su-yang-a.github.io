---
layout: null
---

### 题目名称：afr-2

题目来源：CTFHub（原题：N1Book-第一章Web入门-任意文件读取漏洞-afr_2）

题目类别：Web

题目描述：

### 解题思路

首页是个静态页，只有一句 HELLO! 和一张图，没有任何参数入口。按上一关的思路先试参数名，p、file、page 这些全试一遍，返回和原页面一样，说明不是 PHP 参数注入。再看 /index.php 是 404，而 / 是 200，说明首页是静态文件，站里没有 PHP。

既然不是 PHP 的洞，就往 Web 服务器本身看。访问 /img/ 发现开了目录列表，说明 /img 有独立的 location 配置，但里面只有一个 img.gif，没有子目录可挖。于是转去试目录穿越。

这题的坑是 nginx 的 alias 少写了一个斜杠：location 写 /img 不带结尾斜杠，alias 却带。请求 /img../flag 时，nginx 把 /img 匹配掉、剩下的 ../flag 原样拼到 alias 后面，得到 /img/../flag，.. 生效就跳到了 /flag。

要注意 /img/../flag 反而不行。nginx 会在匹配 location 之前先规范化 URI，/img/../flag 里有独立的 .. 段，会被提前算成 /flag，然后匹配不上 location /img，就当普通文件返回 404。而 /img.. 只是一个叫 img.. 的段，不是独立的 ..，所以躲过了规范化，等拼接成 /img/../flag 时 .. 才出现，这时已经没人检查了。

### 详细步骤

1. 打开首页，只有一句 HELLO! 和一张图，没有任何参数入口

2. 试常见参数名 p、file、page、path、filename 等，返回和原页面完全一样，说明不是 PHP 参数注入

3. 访问 /index.php 返回 404，而 / 返回 200，说明首页是静态文件，站点没有 PHP

4. 访问 /img/，开了目录列表，里面只有一个 img.gif，没有子目录。说明 /img 有独立配置，值得深挖

5. 顺着目录名做穿越。先试 /img../，返回的内容变成整个文件系统根目录

    ```
    Index of /img../
    ../  bin/  dev/  etc/  home/  lib/  media/  mnt/  opt/
         proc/ root/ run/  sbin/ srv/  sys/  tmp/   usr/  var/
         flag                                    21 字节
    ```

    说明已经跳出网站根目录，落到了系统的 / 下面

6. 直接读根目录下的 flag

    ```
    /img../flag
    ```

    返回 n1book{afr_2_solved}，获得flag

### Payload

```
GET /img../flag HTTP/1.1
```

完整地址

```
http://challenge-0c786bc2d5e787c7.sandbox.ctfhub.com:10800/img../flag
```

等价的编码写法

```
/img..%2fflag
/img%2e%2e%2fflag
```

写成 /img/../flag 是不行的，img 后面加了斜杠，nginx 会先把 .. 规范化掉，请求变成 /flag，返回 404。要点是斜杠不能夹在 img 和 .. 中间

### flag

n1book{afr_2_solved}

### 总结&反思
