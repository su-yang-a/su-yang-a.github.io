---
layout: null
---

### 题目名称：easysql

题目来源：CTFHub（原题：SUCTF-2019-Web-easysql）

题目类别：Web

题目描述：

```
<html>
<head>
</head>

<body>

<a> Give me your flag, I will tell you if the flag is right. </a>
<form action="" method="post">
<input type="text" name="query">
<input type="submit">
</form>

</body>
</html>
```

### 解题思路

页面只有一个输入框 query，提交后会在 </html> 后面追加一段输出。试了一圈发现规律很奇怪：输入非零数字有回显，输入字母没有回显；输入 flag 直接回 nonono；union、order、where、from 全被拦，输入长度还限制在 40 字符以内。

数字有回显、字母没回显，说明后端语句里有一个逻辑或的结构，也就是 select 输入的内容 || 某个列 from 某个表。既然表名和列名都叫 Flag，那后端语句就是 select $_POST['query'] || flag from Flag。这样输入 1 时是 1||flag，恒为真，返回 1；输入字母时是真假运算，返回假，就没有回显。

想通这一点之后就简单了：只要让 select 后面的部分变成 select * 就能把整张表查出来。输入 *,1，语句变成 select *,1||flag from Flag，1||flag 恒为 1，等价于 select *,1 from Flag，Flag 表里的内容就全出来了。

### 详细步骤

1. 打开页面，源码只有一个 form，POST 参数名是 query，提交后结果追加在 </html> 后面

    ```
    <form action="" method="post">
    <input type="text" name="query">
    </form>
    ```

2. 分别输入数字和字母，发现只有非零数字有回显，回显固定是 Array ( [0] => 1 )，输入字母没有回显

3. 试常规注入：union、order by、where、from 都返回 nonono，引号也不通；输入超过 40 字符回显 Too long

4. 用堆叠注入确认表名

    ```sql
    1;show databases;
    ```

    回显 ctf、ctftraining、information_schema、mysql、performance_schema、test

    ```sql
    1;show tables;
    ```

    回显 Flag

5. 由数字有回显、字母没回显推出后端语句是

    ```php
    $sql = "select " . $_POST['query'] . "||flag from Flag";
    ```

    || 在 MySQL 里是逻辑或，1||flag 恒为真，所以输入 1 有回显；字母参与运算结果为假，所以没回显

6. 输入 *,1，语句变成 select *,1||flag from Flag，等价于 select *,1 from Flag，直接把 Flag 表整张查出来

    ```
    *,1
    ```

    页面回显 Array ( [0] => ctfhub{7de047bf5dd8302e03a9b421} [1] => 1 )，flag 就在第一个字段里，获得flag

### Payload

```
*,1
```

### flag

ctfhub{7de047bf5dd8302e03a9b421}

### 总结&反思
