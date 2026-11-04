---
layout: null
---

### 题目名称：相机制造商

题目来源：CTFHub（原题：2016-第五届山东省网络安全竞赛-Misc-相机制造商）

题目类别：MISC

题目描述：

![题目描述](Ch-file-wp11.jpg)

附件：[Ch-file-wp11.jpg](Ch-file-wp11.jpg)

### 解题思路

题目叫「相机制造商」，这本身就是提示：相机的制造商，对应的是图片元数据里的相机品牌信息。

先在十六进制编辑器里看文件头确认格式。前三个字节是 FF D8 FF，这是 JPEG 的文件头，所以这是一张普通的照片。

确认是 JPEG 之后，直接在十六进制里找明文就行。这题的 flag 就写在相机厂商那一段，正常那里应该是品牌名（比如 OnePlus），出题人把它换成了 flag。

### 详细步骤

1. 下载附件 ex.jpg，用 ImHex 打开

2. 看左上角第一行的前几个字节：FF D8 FF，这是 JPEG 的文件头，说明这是一张普通的 JPEG 照片

3. 在 ImHex 里搜 Flag{，或者直接把右侧的 ASCII 列往下看。在偏移 0x92 附近就能看到明文

    ![ImHex 里看到的内容](Ch-ph-wp11.png)

    ```
    Flag{6MO3Nbdad7umkHmQ}
    ```

4. 往旁边看，紧跟着的是 ONEPLUS A3000、OnePlus3-user 6.0.1 MMB29M、2016:08:14 15:59:11 这些内容，说明这一段是手机拍摄时写入的相机信息，flag 就藏在相机品牌那个字段里，正常应该是 OnePlus

5. 读取偏移 0x92 处的那段明文，获得flag

### Payload

无

### flag

Flag{6MO3Nbdad7umkHmQ}

### 总结&反思

这imhex还挺好用，啥东西往里一放就行。看看文件头然后该干什么干什么。
