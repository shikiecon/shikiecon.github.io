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
AES加密步骤包括 **字节替换(SubBytes)**, **行移位(RowShifts)**, **列混淆(MixColumns) ** 和 **轮密钥加(AddRoundKey)**. 在AES-128中, 需要让明文经过10轮加密, 其中前9轮加密完整经历以上4个步骤, 最后1轮加密不经过列混淆, 而在AES-192和AES-256中, 加密轮数分别扩大为12轮和14轮, 最后1轮加密仍然不经过列混淆.

考虑如下明文

|      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| T    | h    | e    |      | p    | u    | r    | e    | -    | b    | l    | o    | o    | d    | e    | d    |
| 54   | 68   | 65   | 20   | 70   | 75   | 72   | 65   | 2d   | 62   | 6c   | 6f   | 6f   | 64   | 65   | 64   |

每一个ASCII字符为1字节, 共16字节, 字符下方为ASCII对应的16进制码. 再给定以下16字节的密钥

|      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| :--- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| A    | l    | i    | c    | e    |      | K    | u    | o    | n    | j    | i    | 0    | 9    | 3    | 0    |
| 41   | 6c   | 69   | 63   | 65   | 20   | 4b   | 75   | 6f   | 6e   | 6a   | 69   | 30   | 39   | 33   | 30   |

下面将给出ECB模式下的AES-128加密明文的数学基础和详细加密过程.

### 数学基础

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
\{57\}\oplus\{83\}&=\{\mathrm{d}4\} \tag{十六进制}
\end{align}
$$
其中, 由于多项式系数以$2$为模, 系数$1$等价于$-1$, 于是加法等价于减法. 例如, $x^4+x^2$表示相同的有限域元素$x^4-x^2$, $-x^4+x^2$和$-x^4-x^2$. 类似地, 任意元素与它自身的和为$0$元素.

另一方面, $\mathrm{GF}(2^8)$上的乘法运算$\bullet$定义为多项式的乘积取模一个固定的$8$次不可约多项式
$$
m(x)=x^8+x^4+x^3+x+1
$$
如果令多项式$b(x)$和$c(x)$分别表示字节$b$和$c$, 那么$b\bullet c$表示
$$
b(x)c(x)\quad \mathrm{mod}\,\, m(x)
$$
例如, $\{57\}\bull\{13\}=\{\mathrm{FE}\}$, 这是因为
$$
(x^6+x^4+x^2+x+1)(x^4+x+1)=x^{10}+x^{8}+x^7+x^3+1
$$
以及
$$
x^{10}+x^{8}+x^7+x^3+1\quad\mathrm{mod}\,\, x^8+x^4+x^3+x+1=x^7+x^6+x^5+x^4+x^3+x^2+x
$$
进一步, 如果$c(x)=x$, 也即$c=\{02\}$, 那么乘积$b\bull \{02\}$可以表示为$\mathrm{XTIMES}(b)$, 表示为
$$
\mathrm{XTIMES}(b)=\begin{cases}
\{b_6\,b_5\,b_4\,b_3\,b_2\,b_1\,b_0\,0\}, &b_7=0 \\
\{b_6\,b_5\,b_4\,b_3\,b_2\,b_1\,b_0\,0\}\oplus\{00011011\}, &b_7=1
\end{cases}
$$
于是
$$
\begin{align*}
\{57\}\bull\{01\}&=\{57\} \\
\{57\}\bull\{02\}&=\mathrm{XTIMES}(\{57\})=\{\mathrm{ae}\} \\
\{57\}\bull\{04\}&=\mathrm{XTIMES}(\{\mathrm{ae}\})=\{\mathrm{47}\} \\
\{57\}\bull\{08\}&=\mathrm{XTIMES}(\{\mathrm{47}\})=\{\mathrm{8e}\} \\
\{57\}\bull\{10\}&=\mathrm{XTIMES}(\{\mathrm{8e}\})=\{\mathrm{07}\} 
\end{align*}
$$
从而
$$
\begin{align*}
\{57\}\bull\{13\}&=\{57\}\bull(\{01\}\oplus\{02\}\oplus\{10\}) \\
&=\{57\}\oplus\{\mathrm{AE}\}\oplus\{07\} \\
&=\{\mathrm{fe}\}
\end{align*}
$$

### 加密过程

**First: 初始替代.** 将明文和密钥存储在$4\times 4$矩阵中进行加法运算, 也即
$$
\begin{array}{|c|c|c|c|}
\hline
54 &70&2\mathrm{d}&6\mathrm{f} \\ \hline
68&75&62&64 \\ \hline
65&72&6\mathrm{c}&65 \\ \hline
20&65&6\mathrm{f}&64\\ \hline
\end{array}\,\, \oplus \,\,
\begin{array}{|c|c|c|c|}
\hline
41 &65&6\mathrm{f}&30 \\ \hline
6\mathrm{c}&20&6\mathrm{e}&39\\ \hline
69&4\mathrm{b}&6\mathrm{a}&33 \\ \hline
63&75&69&30 \\ \hline
\end{array}\,\,=\,\,\begin{array}{|c|c|c|c|}
\hline
15 &15&42&5\mathrm{f}\\ \hline
04&55&0\mathrm{c}&5\mathrm{d} \\ \hline
0\mathrm{c}&39&06&56 \\ \hline
43&10&06&54 \\ \hline
\end{array} \tag{A.1}
$$
然后开始进行第一轮加密的字节替换, 这需要用到[FIPS 197](https://doi.org/10.6028/NIST.FIPS.197-upd1)中的S盒将状态矩阵中的每一个元素进行替换, 例如元素`5d`被替换为S盒中第5行第D列的元素`4c`, 由此得到新的状态矩阵
$$
\begin{array}{|c|c|c|c|}
\hline
59 &59&2\mathrm{c}&\mathrm{cf}\\ \hline
\mathrm{f}2&\mathrm{fc}&\mathrm{fe}&4\mathrm{c} \\ \hline
\mathrm{fe}&12&6\mathrm{f}&\mathrm{B1} \\ \hline
1\mathrm{a}&\mathrm{ca}&\mathrm{6f}&20\\ \hline
\end{array} \tag{A.2}
$$
**Second: 行移位.** 让状态矩阵(A.2)的第$i$行中的每个字节向左移动$i-1$位, 这里$i=1,2,3,4$. 于是新的状态矩阵又变为
$$
\begin{array}{|c|c|c|c|}
\hline
59 &59&2\mathrm{c}&\mathrm{cf}\\ \hline
\mathrm{fc}&\mathrm{fe}&4\mathrm{c}&\mathrm{f2} \\ \hline
6\mathrm{f}&\mathrm{b}1&\mathrm{fe}&12 \\ \hline
20&1\mathrm{a}&\mathrm{ca}&6\mathrm{f}\\ \hline
\end{array} \tag{A.3}
$$
**Third: 列混淆.** 需要将状态矩阵(A.3)左乘 以一个固定的$4\times 4$矩阵
$$
\begin{array}{|c|c|c|c|}
\hline
02 &03&01&01\\ \hline
01&02&03&01 \\ \hline
01&01&02&03 \\ \hline
03&01&01&02\\ \hline
\end{array}
$$
我们记状态矩阵`(A.3)`的每一列为$(s_{0,c},s_{1,c},s_{2,c},s_{3,c})^\top$, 其中$0\leq c<4$. 经上述固定矩阵左乘后的新列向量为
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
\end{array}\,\,\,\begin{array}{|c|}
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
经列混淆后的状态矩阵又变为
$$
\begin{array}{|c|c|c|c|}
\hline
\mathrm{e2}&00 &\mathrm{b8} & \mathrm{f5}\\ \hline
\mathrm{2b}&\mathrm{6c} &67 & 69\\ \hline
\mathrm{1b}&\mathrm{f0} &\mathrm{c2} &\mathrm{a8} \\ \hline
38&\mathrm{90} &49 & 74\\ \hline
\end{array} \tag{A.4}
$$
其中元素$s_{0,0}'$的计算方式为
$$
\begin{align*}
s_{0,0}'&=(\{02\}\bull\{59\})\oplus(\{03\}\bull\{\mathrm{fc}\})\oplus\{\mathrm{6f}\}\oplus\{20\} \\
&=\{\mathrm{b2}\}\oplus\{\mathrm{1f}\}\oplus\{\mathrm{6f}\}\oplus\{\mathrm{20}\} \\
&=\{\mathrm{e2}\}
\end{align*}
$$
**Fourth: 轮密钥加.** 现在需要使用轮密钥矩阵与状态矩阵`(A.4)`进行异或操作, 这个轮密钥矩阵是什么? 记初始密钥矩阵为$(w[0],w[1],w[2],w[3])$, 其中
$$
\begin{align*}
w[0]&=(41,\, 6\mathrm{c},\, 69,\, 63)^\top \\
w[1]&=(65,\,20,\,4\mathrm{b},\,75)^\top \\
w[2]&=(6\mathrm{f},\,\mathrm{6e},\,\mathrm{6a},\,69)^\top \\
w[3]&=(30,\,39,\,33,\,30)^\top
\end{align*}
$$
再设$l$为轮数, 则轮密钥矩阵为$(w[4l],w[4l+1],w[4l+2],w[4l+3])$, 并且
$$
w[j]=\begin{cases}
w[j-4]\oplus w[j-1],& j\mod4\neq 0 \\
w[j-4]\oplus g(w[j-1]),& j\mod4=0
\end{cases}
$$
这里的函数$g$使得$w[j-1]$经过字循环、字节替换和轮常量异或. 具体而言

- 字循环: 将$w[j-1]$中的字节循环左移$1$个单位;

- 字节替换: 将字循环得到的列向量根据S盒作替换;

- 轮常量异或: 将字节替换得到的列向量与轮常量$\mathrm{Rcon}[l]$进行异或, 这里的$l$​为轮数.[^5]

  [^5]: 轮常量是一个固定的向量, 具体可见[FIPS 197](https://doi.org/10.6028/NIST.FIPS.197-upd1).

现在我们需要得到第$1$轮的轮密钥矩阵$(w[4],w[5],w[6],w[7])$. 首先计算得到$g(w[3])=(13,\,\mathrm{c}3,\,04,\,04)^\top$,  从而有
$$
w[4]=w[0]\oplus g(w[3])=(\mathrm{52},\,\mathrm{af},\,\mathrm{6d},\,67)^\top
$$
于是
$$
\begin{align*}
w[5]&=w[1]\oplus w[4]=(\mathrm{37},\,\mathrm{8f},\,\mathrm{26},\,12)^\top \\
w[6]&=w[2]\oplus w[5]=(\mathrm{58},\,\mathrm{e1},\,\mathrm{4c},\,\mathrm{7b})^\top \\
w[7]&=w[3]\oplus w[6]=(\mathrm{68},\,\mathrm{d8},\,\mathrm{7f},\,\mathrm{4b})^\top
\end{align*}
$$
然后将轮密钥矩阵与状态矩阵`(A.4)`进行异或, 得到首轮加密后的状态矩阵
$$
\begin{array}{|c|c|c|c|}
\hline
\mathrm{e2}&00 &\mathrm{b8} & \mathrm{f5}\\ \hline
\mathrm{2b}&\mathrm{6c} &67 & 69\\ \hline
\mathrm{1b}&\mathrm{f0} &\mathrm{c2} &\mathrm{a8} \\ \hline
38&\mathrm{90} &49 & 74\\ \hline
\end{array} \,\,\oplus\,\,\, \begin{array}{|c|c|c|c|}
\hline
\mathrm{52}&37 &\mathrm{58} & \mathrm{68}\\ \hline
\mathrm{af}&\mathrm{8f} &\mathrm{e1} & \mathrm{d8}\\ \hline
\mathrm{6d}&\mathrm{26} &\mathrm{4c} &\mathrm{7f} \\ \hline
67&\mathrm{12} &\mathrm{7b} & \mathrm{4b}\\ \hline
\end{array}\,\,=\,\,\,\begin{array}{|c|c|c|c|}
\hline
\mathrm{b0}&37 &\mathrm{e0} & \mathrm{9d}\\ \hline
\mathrm{84}&\mathrm{e3} &86 & \mathrm{b1}\\ \hline
\mathrm{76}&\mathrm{d6} &\mathrm{8e} &\mathrm{d7} \\ \hline
\mathrm{5f}&\mathrm{82} &32 & \mathrm{3f}\\ \hline
\end{array} \tag{A.5}
$$
第一轮加密结束, 随后的第二轮至第九轮加密过程完全相同, 只是轮常量随轮数发生变化, 最后一轮除了不进行列混淆外, 其余步骤也与之前相同. 最后, 我们将十轮加密得到的状态矩阵列举如下:

|          |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| -------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| Round 1  | b0   | 84   | 76   | 5f   | 37   | e3   | d6   | 82   | e0   | 86   | 8e   | 32   | 9d   | b1   | d7   | 3f   |
| Round 2  | bb   | e6   | 85   | f2   | 24   | 3d   | 70   | 4a   | ef   | 22   | d8   | a6   | be   | 09   | 58   | 0d   |
| Round 3  | 3a   | b2   | d0   | f1   | 0d   | ac   | b4   | bd   | 95   | 2a   | 7b   | 07   | dd   | 14   | b2   | 66   |
| Round 4  | d1   | 0b   | f1   | 71   | 4b   | 8c   | 16   | c2   | 65   | de   | fd   | 77   | e2   | 5f   | da   | 58   |
| Round 5  | d8   | 34   | 4a   | 98   | c4   | f5   | 4a   | cc   | 10   | c2   | 43   | 91   | ca   | 9c   | b7   | 67   |
| Round 6  | e5   | 50   | 1a   | 41   | 5b   | 9b   | 33   | 3e   | cc   | fb   | 00   | a3   | 1e   | 76   | b3   | 7a   |
| Round 7  | 50   | 4f   | 84   | 00   | 12   | b9   | 01   | 86   | f6   | 62   | ec   | f2   | 69   | 44   | 5f   | e9   |
| Round 8  | 6c   | 3d   | 55   | 31   | bf   | 90   | c5   | 31   | f5   | f8   | a0   | 12   | 7d   | 0a   | 73   | 02   |
| Round 9  | 39   | 71   | 96   | bc   | 4a   | 55   | 7c   | b3   | d3   | 4b   | d9   | d7   | 83   | df   | 50   | 19   |
| Round 10 | 3b   | 8a   | fa   | 26   | 2e   | 57   | fe   | 67   | 08   | c1   | d4   | 87   | e2   | aa   | cb   | ef   |

