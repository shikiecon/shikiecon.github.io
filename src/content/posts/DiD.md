---
title: 双重差分
published: 2024-05-22
tags: [因果推断]
category: 经济学
draft: false
---

### 基本情形

尽管RCT是估计处置效应的最清楚和最直观的方法, 但出于技术难度和道德伦理的限制, 很难运用到实践中. 除了RCT之外, 几种被称为准实验设计 (quasi-experimental design)的方法被证明也能较好地估计因果效应, 双重差分 (Difference-in-Differences, DiD)就是其中的一种, 主要在政策评估领域中用来估计ATT.

首先介绍经典的DiD, 研究者想知道某项在$t=2$时期实施的政策$D_i$对结果变量$Y_i$​​的影响, 也即ATT[^1]

[^1]: 这里的$Y_{i,t}(d)$表示个体$i$在$t$时刻的潜在结果.

$$
^\dagger\tau=\mathbb{E}[Y_{i,2}(1)-Y_{i,2}(0)|D_i=1]
$$


在SUTVA成立的情况下, ATT是良好定义的, 但我们无法识别出它. 在缺乏实施RCT的情境下, 需要做出一些额外假设才能正确识别ATT.

> 平行趋势: $\mathbb{E}[Y_{i,2}(0)-Y_{i,1}(0)|D_i=1]=\mathbb{E}[Y_{i,2}(0)-Y_{i,1}(0)|D_i=0]$;
>
> 无预期效应: 对于所有$D_i=1$的个体$i$都有$Y_{i,1}(0)=Y_{i,1}(1)$.

平行趋势假设表明, 如果在$t=2$期没有政策冲击, 那么处置组和控制组的结果变量随时间变化的趋势应该一样的, 也即其他因素造成处置组和控制组的差异在$t=1$和$t=2$时期相同. 无预期效应表明, 受政策影响的个体在政策实施前的潜在结果相同, 也即政策在实施前不会对结果产生任何影响.

根据以上两个假设可以得到
$$
\begin{align*}
\mathbb{E}[Y_{i,2}(0)|D_i=1]&=\mathbb{E}[Y_{i,1}(0)|D_i=1]+\mathbb{E}[Y_{i,2}(0)-Y_{i,1}(0)|D_i=0] \\
&=\mathbb{E}[Y_{i,1}(1)|D_i=1]+\mathbb{E}[Y_{i,2}(0)-Y_{i,1}(0)|D_i=0] \\
&=\mathbb{E}[Y_{i,1}|D_i=1]+\mathbb{E}[Y_{i,2}-Y_{i,1}|D_i=0]
\end{align*}
$$
从而处置组处置效应可以被识别为
$$
^\dagger\tau=\mathbb{E}[Y_{i,2}-Y_{i,1}|D_i=1]-\mathbb{E}[Y_{i,2}-Y_{i,1}|D_i=0]
$$
上式第1项表示由于政策冲击和其他因素造成的处置组结果变量的平均差异, 第2项为其他因素造成的控制组结果变量的平均差异, 因此该方法称为$2\times 2$​双重差分, 如[图1](https://raw.githubusercontent.com/shikiecon/shikiecon.github.io/main/src/assets/images/fig1.png)所示.

类似地, 我们可以直接写出$^\dagger\tau$的一致估计量
$$
^\dagger\hat{\tau}=(\bar{Y}_{2,1}-\bar{Y}_{1,1})-(\bar{Y}_{2,0}-\bar{Y}_{1,0})
$$
其中$\bar{Y}_{t,d}$是$D_i=d$的所有个体$i$的结果变量$Y_i$在$t$时刻的平均值. 除此之外, 还可以使用双向固定效应模型
$$
Y_{i,t}=\alpha_i+\lambda_t+(1[t=2]\cdot D_i)\beta+e_{i,t}
$$
出自以上回归方程的OLS估计量$\hat{\beta}$数值上等价于$^\dagger\hat{\tau}$, 这里的$\alpha_i$和$\lambda_t$分别为个体固定效应和时间固定效应, $e_{i,t}$为随机扰动项. 此外, 如果$\{Y_{i,2},Y_{i,1},D_i\}_{i=1}^n$是出自某个满足平行趋势假设的分布的i.i.d.随机样本, 那么在温和的正则条件下, 当$n\to\infty$时有
$$
\sqrt{n}(\hat{\beta}-{^\dagger\tau})\xrightarrow{d}N(0,V_\text{DiD})
$$
