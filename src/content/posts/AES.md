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

[^4]: 用于AES的密钥和平日里输入的密码不同, 密码通常经密钥派生函数 (Key Derivation Function, KDF)映射为适配AES密钥长度的密钥, 而密码的熵越高, 破解难度越大.
[^3]: 除了ECB模式外, AES算法中还包括CBC、CTR等模式, 具体见[Wikipedia](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).
[^2]: 如果要加密的信息不是128bits的整数倍, 那么需要在结尾补齐，补齐方法包括PKCS7、PKCS5、Zeros等. 

AES算法运行在一个$4\times 4$矩阵上, 该矩阵称为 **体(state)**, 每个矩阵的元素是$1$个字节, 也即
$$
\begin{array}{|c|c|c|}
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

| T    | o    | d    | a    | y    |      | i    | s    |      | T    | u    | e    | s    | d    | a    | y    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 54   | 6F   | 64   | 61   | 79   | 20   | 69   | 73   | 20   | 54   | 75   | 65   | 73   | 64   | 61   | 79   |

每一个ASCII字符为1字节, 共16字节, 字符下方为ASCII对应的16进制码. 再给定以下16字节的密钥

| A    | l    | i    | c    | e    |      | K    | u    | o    | n    | j    | i    | 0    | 9    | 3    | 0    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 41   | 6C   | 69   | 63   | 65   | 20   | 4B   | 75   | 6F   | 6E   | 6A   | 69   | 30   | 39   | 33   | 30   |

下面将给出ECB模式下的AES-128加密明文的具体步骤.

### 加密过程

首先将明文和密钥储存在$4\times 4$矩阵中进行异或运算, 也即
$$
\begin{array}{|c|c|c||}
\hline
54 &79&20&73 \\ \hline
6\mathrm{F}&20&54&64 \\ \hline
64&69&75&61 \\ \hline
61&73&65&79 \\ \hline
\end{array}\,\, \bigoplus\,\,
\begin{array}{|c|c|c|}
\hline
41 &65&6\mathrm{F}&30 \\ \hline
6\mathrm{C}&20&6\mathrm{E}&39\\ \hline
69&4\mathrm{B}&6\mathrm{A}&33 \\ \hline
63&75&69&30 \\ \hline
\end{array}\,\,=\,\,\,\begin{array}{|c|c|c|}
\hline
15 &1\mathrm{C}&4\mathrm{F}&43\\ \hline
03&00&3\mathrm{A}&5\mathrm{D} \\ \hline
0\mathrm{D}&22&1\mathrm{F}&52 \\ \hline
02&06&0\mathrm{C}&49 \\ \hline
\end{array}
$$
