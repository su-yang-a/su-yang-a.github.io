---
layout: null
---

### 题目名称：认真你就输了

题目来源：CTFHub（原题：2020-BJDCTF-Misc-认真你就输了）

题目类别：MISC

题目描述：

![题目描述](Ch-ph-wp09.png)

附件：[Ch-file-wp09.xls](Ch-file-wp09.xls)

### 解题思路

这题给的是一个 .xls 表格。用 Excel 打开，里面四张表全是教学示例：类型演示、直线 y=-x+4、抛物线 y=x²+2x+3、加法表，一共 495 个公式，看不出和 flag 有任何关系。题名叫「认真你就输了」，意思就是别真去算这些表。

真正的答案不在表里，在文件本身。这个 .xls 的文件头是 50 4B 03 04，也就是 PK，这是 ZIP 的标志；而真正的老式 xls 文件头应该是 D0 CF 11 E0。所以它其实是 .xlsx 改了个后缀。

xlsx 本质就是一个 ZIP 包。Office 隐写最常见的手法就是往包里塞文件，塞进去的东西 Excel 不会显示，但解压就能看到。

把包解开，会发现 xl/charts/ 目录下多了一个 flag.txt。这个目录正常只放 chart1.xml、chart2.xml 这种图表定义和 _rels，出现一个 .txt 就是人为塞进去的。读出来就是 flag。

### 详细步骤

1. 下载附件 10.xls，用 Excel 打开，四张表都是示例数据，没有任何线索

2. 看文件头，判断真实类型。Windows 上可以用 PowerShell

    ```powershell
    Format-Hex -Path '10.xls' -Count 16
    ```

    输出开头是 50 4B 03 04，也就是 ASCII 的 PK，是 ZIP 的标志。老式 xls 的文件头应该是 D0 CF 11 E0，所以这个文件是 xlsx 改了后缀

3. 既然表面没有线索，就去看包内部。可以把文件复制一份改名为 10.zip 右键解压，也可以直接列结构

    ```bash
    unzip -l '10.xls'
    ```

4. 对照正常 xlsx 的目录，发现异常项：xl/charts/ 下多了一个 flag.txt。这个目录正常只有 chart1.xml、chart2.xml 和 _rels

5. 直接读取这个文件的内容

    ```bash
    unzip -p '10.xls' 'xl/charts/flag.txt'
    ```

6. 得到 flag{M9eVfi2Pcs#}，获得flag

### Payload

无

### flag

flag{M9eVfi2Pcs#}

### 总结&反思
