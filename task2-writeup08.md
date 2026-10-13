---
layout: null
---

### 题目名称：source

题目来源：Bugku CTF

题目类别：Web

题目描述：代码：

Hello,world!

This is my friend :

### 解题思路

无，马后炮。

听从deepseek和前人wp，先用base64解码发现flag并不是真的，扫描目录可以发现/.git/目录，扒取目录后发现“flag is here？”的嫌疑语句，提取哈希值，尝试flag。

### 详细步骤

1. 安装 git-dumper（我是在 pycharm 里装的，也可以在终端执行下面这条）

    `pip install git-dumper`

2. 把目标网站的 .git 目录扒下来

    `git-dumper http://160.202.254.160:12070/.git/ ./source_code`

3. 进入新建的 source_code 文件夹，查看 git 操作记录

    `cd source_code`

    `git reflog`

4. 找到嫌疑哈希值，还原出flag。
5. 尝试直到找到正确flag

### Payload

无

### flag

flag{git_is_good_distributed_version_control_system}

### 总结&反思

本次更多借鉴前人wp，前人所用为Linux，而我是用pycharm，我将搭建Linux系统。本题考查目录扫描，git操作。解题完成后偶然猜想或许源码里的This is my friend :<!--tig-->tig提示了我们用git。
