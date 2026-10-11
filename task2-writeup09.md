---
layout: null
---

### 题目名称：瑞士军刀

题目来源：Bugku CTF

题目类别：PWN

题目描述：代码：nc 171.80.2.169 16845

### 解题思路

无，马后炮。

听从deepseek，“nc”是Linux系统指令，意思是使用nc连接ip是171.80.2.169服务器的16845端口，直接使用。但我没有linux系统，所以需要下载虚拟机。

### 详细步骤

1. 下载VMware Workstation Pro
2. 下载Kali Linux
3. 执行nc 171.80.2.169 16845
4. 输入Is
5. 发现flag文件，输入cat flag。
6. 获得flag

### Payload

nc 171.80.2.169 16845

### flag

flag{9e861196d5a73bcd}

### 总结&反思

本次是我首次运用虚拟机和Linux系统，需要继续学习。
