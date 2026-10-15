---
layout: null
---

### 题目名称：GET

题目来源：Bugku CTF

题目类别：Web

题目描述：显示代码：$what=$_GET['what']; echo $what; if($what=='flag') echo 'flag{\*\*\*\*}';

### 解题思路

无，马后炮。

听从deepseek，在地址处添加?what=flag

### 详细步骤

1. 地址输入http://160.202.254.160:14691/?what=flag
2. 获得flag

### Payload

?what=flag

### flag

flag{55b8a987379498184ee1dc643e8b4906}

### 总结&反思

经询问deepseek，本次通过赋值给what，使其满足代码输出要求获得flag。
