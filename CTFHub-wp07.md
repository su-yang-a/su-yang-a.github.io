---
layout: null
---

### 题目名称：random

题目来源：CTFHub（原题：2017-赛客夏令营-Web-random）

题目类别：Web

题目描述：

![题目描述](Ch-ph-wp07.png)

![题目描述](Ch-ph-wp07-2.png)

### 解题思路

### 详细步骤

### Payload

```
GET /index.php?num=4 HTTP/1.1
Host: challenge-19f5b58fdae14894.sandbox.ctfhub.com:10800
```

核心就是 num 这个参数固定不变，反复重放：

```
http://challenge-19f5b58fdae14894.sandbox.ctfhub.com:10800/index.php?num=4
```

### flag

ctfhub{edcf8d18f71ffbb0749232a9}

### 总结&反思
