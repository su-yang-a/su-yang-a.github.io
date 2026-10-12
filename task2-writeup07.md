---
layout: null
---

### 题目名称：POST

题目来源：Bugku CTF

题目类别：web

题目描述：显示代码：$what=$_POST['what']; echo $what; if($what=='flag') echo 'flag{\*\*\*\*}';

### 解题思路

无，马后炮。

听从deepseek，本体代码要求使用post请求方式，传入一个值为‘flag’的‘what’参数。

### 详细步骤

1. f12打开控制台
2. 在控制台里输入这段代码

`fetch(window.location.href, { method: 'POST', headers: {'Content-Type': 'application/x-www-form-urlencoded'}, body: 'what=flag' }).then(r => r.text()).then(console.log);`

3. 获得flag

### Payload

what=flag

### flag

flag{86d8e343408a4cf1cc0db547e4203051}

### 总结&反思

经询问deepseek，本体考察HTTP协议与PHP基础代码审计，并使用了Web前端调试技巧，虽然我还是没有完全掌握， 但是至少了解代码含义。
