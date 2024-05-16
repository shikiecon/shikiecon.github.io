---
title: 高级加密标准
published: 2024-05-17
tags: [密码学]
category: 密码学
draft: false
---

**高级加密标准(Advanced Encryption Standard, AES)** 是美国联邦政府采用的分块对称加密算法, 截至目前仍然不可攻破, 关于AES的官方文档可以参看[这个链接](https://doi.org/10.6028/NIST.FIPS.197-upd1), 该算法由比利时密码学家Daeman和Rijmen所设计, 结合这两位作者的名字, AES又称为Rijndael加密法.[^1] 

[^1]: AES和Rijindael并非完全相同, 区分在于AES要求每个分块长度为128bits, 而Rijndael算法中分块长度可以为128bits, 192bits和256bits.

AES是一种分块加密算法, 需要严格将加密的信息按128bits分块, [^2]

[^2]: 如果要加密的信息不是128bits的整数倍, 那么需要在结尾补齐，补齐方法包括PKCS7、PKCS5、Zeros等, 具体可见[Wikipedia](https://en.wikipedia.org/wiki/PKCS).

 而密钥长度可以分别选取为128bits, 192bits和256bits, 此时分别称AES-128, AES-192和AES-256, 穷举法破解它们的计算复杂度分别为$2^{128}$, $2^{192}$和$2^{256}$. 即使量子计算机成熟, 使用Grover算法也只能做到二次加速, 使它们的计算复杂度减半, 此时AES-256仍然安全.

 
