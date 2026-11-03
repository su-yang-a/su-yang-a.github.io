---
layout: null
---

### 题目名称：sign in

题目来源：CTFHub（原题：2020-BJDCTF-Crypto-sign in）

题目类别：Crypto

题目描述：

![题目描述](Ch-ph-wp08.png)

附件：[Ch-file-wp08.txt](Ch-file-wp08.txt)

### 解题思路

签到题。附件里一句 welcome，加一行密文。

看密文的特征：只有 0-9 和 a-f，没有大写字母、没有 + 和 /、也没有 =，长度 44 是偶数。这是十六进制编码，每两个字符对应一个字节。把每两位按 ASCII 还原，开头 42 4a 44 正好是 BJD，接着 7b 是左花括号，说明后面就是 flag 的内容，一直解到 7d 的右花括号为止。

### 详细步骤

1. 下载附件 sign_in.txt，内容是 welcome to crypto world！！ 和一行密文

    ```
    424a447b57653163306d655f74345f424a444354467d
    ```

2. 看密文特征：字符集只有 0-9 和 a-f，长度 44 是偶数，判断为十六进制编码

3. 两个字符一组还原成字节，44 位正好还原出 22 个字节。可以直接用 xxd 还原

    ```bash
    echo '424a447b57653163306d655f74345f424a444354467d' | xxd -r -p
    ```

    或者用 Python

    ```python
    bytes.fromhex('424a447b57653163306d655f74345f424a444354467d').decode()
    ```

    逐字节对照

    ```
    42=B  4a=J  44=D  7b={  57=W  65=e  31=1  63=c  30=0  6d=m  65=e
    5f=_  74=t  34=4  5f=_  42=B  4a=J  44=D  43=C  54=T  46=F  7d=}
    ```

4. 其中 7b 是左花括号，7d 是右花括号，中间就是 flag 内容，连起来就是 BJD{We1c0me_t4_BJDCTF}，获得flag

### Payload

无

### flag

BJD{We1c0me_t4_BJDCTF}

### 总结&反思
