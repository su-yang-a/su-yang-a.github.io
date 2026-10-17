---
layout: null
---

### 题目名称：Quick Math

题目来源：CTFHub（原题：2020-CSICTF-Crypto-Quick Math）

题目类别：Crypto

题目描述：Ben has encrypted a message with the same value of 'e' for 3 public moduli - n1 = 86812553978993 n2 = 81744303091421 n3 = 83695120256591 and got the cipher texts - c1 = 8875674977048 c2 = 70744354709710 c3 = 29146719498409. Find the original message. (Wrap it with csictf{})

### 解题思路

题目只给了三组模数和密文，没给 e，但说"同一个 e"。同一条消息、同一个 e、多个互不相同的模数，这是典型的 RSA 广播攻击（Håstad）。

e 不用猜，模数的个数已经把它锁死了。广播攻击的前提是 m^e 必须小于所有模数的乘积，否则中国剩余定理合成出来的只是一个"绕过圈"的余数，开方开不出整数。而消息 m 比每个 n 都小，所以 m^e < (min n)^e，只要模数个数 k ≥ e 这个条件就成立。这题给了 k = 3 个模数，于是 e ≤ 3；又因为 RSA 的 e 必须是大于 1 的奇数（e = 2 时 φ(n) 恒为偶数，gcd(2, φ(n)) = 2，私钥 d 不存在），所以 e 只能是 3。

确认 e = 3 之后，做法就固定了：三组 (n, c) 用中国剩余定理合成得到 m^3，对它开整数三次方拿到明文数字，最后再把数字变回字符串。最后这一步出题人的写法有点特别，后面细说。

### 详细步骤

1. 先看数据规模。三个 n 都是 14 位十进制，换算成二进制约 46 位，对 RSA 来说非常小

2. 定 e。给了 3 个模数，按 k ≥ e 的约束加上 e 必须为奇，e 只能是 3

3. 用中国剩余定理把三组 (n, c) 合成一个同余式

    n1·n2·n3 = 593936706583013317449904263441604016328323
    X        = 319222184729548122617007524482681344

4. 检查 X 是否小于 n1·n2·n3。这里成立，所以 X 就是 m^3 本身

5. 对 X 开整数三次方，得到 m = 683435743464。注意不能用浮点的 x ** (1/3)，大整数会丢精度，要用整数牛顿法，开完还要用 m**3 == X 校验

6. 反推验证：把 m 代回去，pow(m, 3, n) 是否等于题目的 c。三个全部吻合，说明 e 和 m 同时被证实

7. 把数字变回字符串。这里踩了坑：当成字节串还原出来是 9f 1f f1 d8 e8，不是可打印字符。改成"把十进制的数字串当十六进制解"，683435743464 拆成 68 34 35 74 34 64，正好是 h45t4d，于是获得flag

### Payload

```python
n = [86812553978993, 81744303091421, 83695120256591]
c = [8875674977048, 70744354709710, 29146719498409]
e = 3

def crt(a1, m1, a2, m2):
    t = ((a2 - a1) * pow(m1, -1, m2)) % m2
    return a1 + m1 * t, m1 * m2

x, m12 = crt(c[0], n[0], c[1], n[1])
X, M = crt(x, m12, c[2], n[2])
assert X < M

def iroot3(v):
    x = 1 << ((v.bit_length() + 2) // 3)
    while True:
        y = (2 * x + v // (x * x)) // 3
        if y >= x:
            return x
        x = y

m = iroot3(X)
assert m ** 3 == X
assert all(pow(m, e, n[i]) == c[i] for i in range(3))

print(bytes.fromhex(str(m)).decode())
```

### flag

csictf{h45t4d}

### 总结&反思

这题真正容易错的不是 RSA，而是最后"数字变回字符串"那一步。

出题人的代码大概是这么写的：

    m = int(msg.hex())

这里漏了 base=16 这个参数。msg.hex() 得到的是字符串 "683435743464"，而 int 默认按十进制解析，于是本该是十六进制大数的 m，被当成了十进制数 683435743464。

所以解密时不能照标准套路走。标准写法是 int.from_bytes(msg.encode(), "big")，还原时用 m.to_bytes(...) 再 decode；而这题的写法是 int(msg.hex())，还原时要把 str(m) 当十六进制串再 bytes.fromhex。两种写法差一个参数，还原方式就完全不同。

正确代码应该写成：

    m = int(msg.hex(), 16)

或者

    m = int.from_bytes(msg.encode(), "big")

对应的还原就是 m.to_bytes((m.bit_length() + 7) // 8, "big").decode()。

另外一点，明文 h45t4d 换成 leet 就是 hastad，也就是 Håstad 广播攻击的名字。这一点也反过来印证了结果没跑偏。