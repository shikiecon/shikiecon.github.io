---
title: 高级加密标准
published: 2024-05-17
tags: [对称加密]
category: 密码学
draft: false
---

### 简介

**高级加密标准(Advanced Encryption Standard, AES)** 是美国联邦政府采用的分块 **对称加密算法**, 截至目前仍然不可攻破, 可被用于加密经NSA批准的加密模块中的绝密文档, 关于AES的官方文档可以参看[FIPS 197](https://doi.org/10.6028/NIST.FIPS.197-upd1). 该算法由比利时密码学家Daeman和Rijmen所设计, 结合这两位作者的名字, AES又称为Rijndael加密法.[^1] 

[^1]: 严格来说, AES和Rijindael并非完全相同, 区分在于AES要求每个分块长度为128比特, 而Rijndael算法中分块长度可以为128比特, 192比特或256比特.

AES是一种分块加密算法, ECB模式需要严格将加密的信息按128比特分块,[^2] [^3]而密钥长度可以分别选取为128比特, 192比特和256比特,[^4]此时分别称AES-128, AES-192和AES-256, 穷举法破解它们的计算复杂度分别为$2^{128}$, $2^{192}$和$2^{256}$. 即使量子计算机成熟, 使用Grover算法也只能做到二次加速, 使它们的计算复杂度减半, 此时AES-256仍然安全, 但AES-128和AES-192不再安全.

[^4]: 用于AES的密钥和平日里输入的密码不同, 密码通常经密钥派生函数 (KDF)映射为适配AES密钥长度的密钥, 而密码的熵越高, 破解难度越大.
[^3]: 除了ECB模式外, AES算法中还包括CBC、CTR等模式, 具体见[Wikipedia](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).
[^2]: 如果要加密的信息不是128bits的整数倍, 那么需要在结尾补齐，补齐方法包括PKCS7、PKCS5、Zeros等. 

AES算法运行在一个$4\times 4$状态矩阵上, 每个矩阵的元素是$1$个字节, 也即
$$
\begin{array}{|c|c|c|c|}
\hline
b_0&b_4&b_8&b_{12} \\ \hline 
b_1&b_5&b_9&b_{13} \\ \hline 
b_2&b_6&b_{10}&b_{14} \\ \hline 
b_3&b_7&b_{11}&b_{15}  \\
\hline
\end{array}
$$
AES加密步骤包括 **字节替换(SubBytes)**、**行移位(RowShifts)**、**列混淆(MixColumns) ** 和 **轮密钥加(AddRoundKey)**. 在AES-128中, 需要让明文经过10轮加密, 其中前9轮加密完整经历以上4个步骤, 最后1轮加密不经过列混淆, 而在AES-192和AES-256中, 加密轮数分别扩大为12轮和14轮, 最后1轮加密仍然不经过列混淆.

考虑如下明文

| T    | h    | e    |      | p    | u    | r    | e    | -    | b    | l    | o    | o    | d    | e    | d    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 54   | 68   | 65   | 20   | 70   | 75   | 72   | 65   | 2D   | 62   | 6C   | 6F   | 6F   | 64   | 65   | 64   |

每一个ASCII字符为1字节, 共16字节, 字符下方为ASCII对应的16进制码. 再给定以下16字节的密钥

| A    | l    | i    | c    | e    |      | K    | u    | o    | n    | j    | i    | 0    | 9    | 3    | 0    |
| :--- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 41   | 6C   | 69   | 63   | 65   | 20   | 4B   | 75   | 6F   | 6E   | 6A   | 69   | 30   | 39   | 33   | 30   |

下面将给出ECB模式下的AES-128加密明文的预备数学内容和详细加密过程.

### 预备数学内容

AES算法中的大部分操作都定义在Galois域$\mathrm{GF}(2^8)$上, 每个字节$\{b_7\,b_6\,b_5\,b_4\,b_3\,b_2\,b_1\,b_0\}$都可以表示为多项式
$$
b(x)=b_7x^7+b_6x^6+b_5x^5+b_4x^4+b_3x^3+b_2x^2+b_1x+b_0
$$
例如, $\{01100011\}$可以表示为多项式$x^6+x^5+x+1$. 

为了在$\mathrm{GF}(2^8)$上对两个元素进行加法运算, 表示这两个元素的的系数需要以2为模进行异或, 也即$1\oplus 1=0$, $1\oplus 0=1$和$0\oplus 0=0$. 因此, 每个字节的加法运算是与其相关的8个比特的异或运算, 也即$\{a_7\,a_6\,a_5\,a_4\,a_3\,a_2\,a_1\,a_0\}$与$\{b_7\,b_6\,b_5\,b_4\,b_3\,b_2\,b_1\,b_0\}$的和为
$$
\{a_7\oplus b_7\,\,a_6\oplus b_6\,\, a_5\oplus b_5\,\,a_4\oplus b_4\,\,a_3\oplus b_3\,\,a_2\oplus b_2\,\, a_1\oplus b_1\,\, a_0\oplus a_0\}
$$
例如, 以下三个运算是等价的
$$
\begin{align}
(x^6+x^4+x^2+x+1)+(x^7+x+1)&=x^7+x^6+x^4+x^2 \tag{多项式} \\
\{01010111\}\oplus\{10000011\}&=\{11010100\} \tag{二进制} \\ 
\{57\}\oplus\{83\}&=\{\mathrm{D}4\} \tag{十六进制}
\end{align}
$$
其中, 由于多项式系数以$2$为模, 系数$1$等价于$-1$, 于是加法等价于减法. 例如, $x^4+x^2$表示相同的有限域元素$x^4-x^2$, $-x^4+x^2$和$-x^4-x^2$. 类似地, 任意元素与它自身的和为$0$元素.

另一方面, $\mathrm{GF}(2^8)$上的乘法运算$\bullet$定义为多项式的乘积取模一个固定的$8$次不可约多项式
$$
m(x)=x^8+x^4+x^3+x+1
$$
如果令多项式$b(x)$和$c(x)$分别表示字节$b$和$c$, 那么$b\bullet c$表示
$$
b(x)c(x)\quad \mathrm{mod}\,m(x)
$$
例如, 

### 加密过程

首先将明文和密钥储存在$4\times 4$矩阵中进行异或运算, 称为初始替代, 也即
$$
\begin{array}{|c|c|c|c|}
\hline
54 &70&2\mathrm{D}&6\mathrm{F} \\ \hline
68&75&62&64 \\ \hline
65&72&6\mathrm{C}&65 \\ \hline
20&65&6\mathrm{F}&64\\ \hline
\end{array}\,\, \bigoplus\,\,
\begin{array}{|c|c|c|c|}
\hline
41 &65&6\mathrm{F}&30 \\ \hline
6\mathrm{C}&20&6\mathrm{E}&39\\ \hline
69&4\mathrm{B}&6\mathrm{A}&33 \\ \hline
63&75&69&30 \\ \hline
\end{array}\,\,=\,\,\,\begin{array}{|c|c|c|c|}
\hline
15 &15&42&5\mathrm{F}\\ \hline
04&55&0\mathrm{C}&5\mathrm{D} \\ \hline
0\mathrm{C}&39&06&56 \\ \hline
43&10&06&54 \\ \hline
\end{array} \tag{A.1}
$$
然后开始进行第一轮加密的字节替换, 这需要用到[FIPS 197](https://doi.org/10.6028/NIST.FIPS.197-upd1)中的S盒将状态矩阵中的每一个元素进行替换, 例如元素`5D`被替换为S盒中第5行第D列的元素`4C`, 由此得到新的状态矩阵
$$
\begin{array}{|c|c|c|c|}
\hline
59 &59&2\mathrm{C}&\mathrm{CF}\\ \hline
\mathrm{F}2&\mathrm{FC}&\mathrm{FE}&4\mathrm{C} \\ \hline
\mathrm{FE}&12&6\mathrm{F}&\mathrm{B1} \\ \hline
1\mathrm{A}&\mathrm{CA}&\mathrm{6F}&20\\ \hline
\end{array} \tag{A.2}
$$
接下来是行移位, 即让状态矩阵(A.2)的第$i$行中的每个字节向左移动$i-1$位, 这里$i=1,2,3,4$. 于是新的状态矩阵又变为
$$
\begin{array}{|c|c|c|c|}
\hline
59 &59&2\mathrm{C}&\mathrm{CF}\\ \hline
\mathrm{FC}&\mathrm{FE}&4\mathrm{C}&\mathrm{F2} \\ \hline
6\mathrm{F}&\mathrm{B}1&\mathrm{FE}&12 \\ \hline
20&1\mathrm{A}&\mathrm{CA}&6\mathrm{F}\\ \hline
\end{array} \tag{A.3}
$$
再下来是列混淆, 需要将状态矩阵(A.3)左乘以一个固定的$4\times 4$矩阵
$$
\begin{array}{|c|c|c|c|}
\hline
02 &03&01&01\\ \hline
01&02&03&01 \\ \hline
01&01&02&03 \\ \hline
03&01&01&02\\ \hline
\end{array}
$$
我们记状态矩阵(A.3)的每一列为$[s_{0,c},s_{1,c},s_{2,c},s_{3,c}]'$, 其中$0\leq c<4$. 经上述固定矩阵左乘后的新列向量为
$$
\begin{array}{|c|}
\hline
s_{0,c}' \\ \hline
s_{1,c}' \\ \hline
s_{2,c}' \\ \hline
s_{3,c}' \\ \hline
\end{array}\,\,=\,\,\begin{array}{|c|c|c|c|}
\hline
02 &03&01&01\\ \hline
01&02&03&01 \\ \hline
01&01&02&03 \\ \hline
03&01&01&02\\ \hline
\end{array}\,\,\bigotimes\,\,\begin{array}{|c|}
\hline
s_{0,c} \\ \hline
s_{1,c} \\ \hline
s_{2,c} \\ \hline
s_{3,c} \\ \hline
\end{array}
$$
其中
$$
\begin{align*}
s_{0,c}'&=(\{02\}\bullet s_{0,c})\oplus(\{03\}\bullet s_{1,c})\oplus s_{2,c}\oplus s_{3,c} \\
s_{1,c}'&=s_{0,c}\oplus(\{02\}\bullet s_{1,c})\oplus(\{03\}\bullet s_{2,c})\oplus s_{3,c} \\
s_{2,c}'&=s_{0,c}\oplus s_{1,c}\oplus(\{02\}\bullet s_{2,c})\oplus (\{03\}\bullet s_{3,c}) \\
s_{3,c}'&=(\{03\}\bullet s_{0,c})\oplus s_{1,c}\oplus s_{2,c}\oplus (\{02\}\bullet s_{3,c})
\end{align*}
$$
 这里的乘法运算$\cdot$定义在Galois域$\mathrm{GF}(2^8)$上
