---
layout: null
---

### 题目名称：telnet

题目来源：Bugku CTF

题目类别：MISC

题目描述：key格式flag{xxxxxxxxxxxxxxxxxxxxxxxxxxx}

附件：[file-wp28.pcap](file-wp28.pcap)

### 解题思路

无，马后炮。

听从deepseek，这是一道流量分析题，附件是一个 pcap 抓包文件。先看协议分级，59 个包全是 TCP；再看会话，只有一个会话——192.168.221.128:1146 连到 192.168.221.164 的 23 端口。23 端口是 Telnet，而 Telnet 是明文协议，登录用的用户名和密码会直接出现在数据包里。于是直接追踪这条 TCP 流，里面就有 flag。

### 详细步骤

1. 用 Wireshark 打开 networking.pcap

2. 统计 到 协议分级，看到 59 个包全部是 TCP

3. 统计 到 会话，只有一个会话，对端端口是 23，也就是 Telnet

4. 右键任意一个包，选择 追踪流 到 TCP 流

5. 明文对话全部展开，里面就能看到 flag

6. 获得flag

### Payload

无

### flag

flag{d316759c281bf925d600be698a4973d5}

### 总结&反思
