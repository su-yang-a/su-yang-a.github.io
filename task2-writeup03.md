---
layout: null
---

### 题目名称：alert

题目来源：Bugku CTF

题目类别：Web

题目描述：只有页面显示“flag就在这里，来找找吧。”

### 解题思路

无，马后炮。

f12打开源代码后是空白，Ctrl+U查看源代码。

### 详细步骤

1. Ctrl+U打开源代码
2. 找到&lt;!-- &amp;#102;&amp;#108;&amp;#97;&amp;#103;&amp;#123;&amp;#102;&amp;#49;&amp;#54;&amp;#99;&amp;#98;&amp;#99; &amp;#51;&amp;#55;&amp;#49;&amp;#50;&amp;#52;&amp;#49;&amp;#56;&amp;#57;&amp;#56;&amp;#55;&amp;#101;&amp;#101; &amp;#53;&amp;#98;&amp;#53;&amp;#54;&amp;#57;&amp;#48;&amp;#57;&amp;#98;&amp;#102;&amp;#53;&amp;#102;&amp;#53;&amp;#49;&amp;#55;&amp;#125; --&gt;
3. 复制到解码器解码
4. 输入正确答案获得flag

### Payload

http://160.202.254.160:18631/

### flag

flag{f16cbc3712418987ee5b56909bf5f517}

### 总结&反思

据deepseek说，Ctrl+U可以打开隐藏源代码，&#XXX是html编码的特征。
