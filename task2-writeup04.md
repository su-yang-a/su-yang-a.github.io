---
layout: null
---

### 题目名称：你必须让他停下

题目来源：Bugku CTF

题目类别：web

题目描述：页面每500ms刷新一次，源代码其中之一包含flag。

### 解题思路

无，马后炮。

f12打开源代码，打开设置禁用js，手动刷新。

### 详细步骤

1. f12打开源代码
2. 左上角设置打开disable javascript
3. f5手动刷新
4. 直到出现flag

### Payload

无

### flag

flag{b1016a76fcde02e97662a0efdbc79421}

### 总结&反思

经询问deepseek，可以通过代码多次发送请求，直到出现flag后发回计算机。
