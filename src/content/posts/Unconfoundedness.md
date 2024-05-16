---
title: 无混淆情形
published: 2024-05-17
tags: [计量经济学, 因果推断]
category: 经济学
draft: false
---

### 基本概念

尽管RCT的效力是最高的, 但RCT花费的代价也是巨大的, 甚至某些情况下无法进行实验, 随机处置指派不成立. 然而, 当控制了一系列可观测变量$X$后, 潜在结果可能独立于处置状态, 也即
$$
\begin{equation}
  \left.\{Y_i(0),Y_i(1)\}\perp D_i\,\right|\,X_i \tag{C.5}
\end{equation}
$$
此时称为 **无混淆情形 (unconfoundedness)**, 也称依可观测变量选择. 另一方面, ATE回答的是个体被随机分配到处置组所预期的因果效应, 研究者可能不感兴趣, 因为它包括了那些没有参与处置的个体的影响. 通常而言, 我们感兴趣的是 **处置组平均处置效应 (Average Treatment Effect on Treated, ATT)**[^1]

[^1]: 在随机控制实验中, ATE和ATT是完全相等的.

$$
^\dagger\tau=\mathbb{E}[Y_i(1)|D_i=1]-\mathbb{E}[Y_i(0)|D_i=1]
$$

也即处置行为真正带来的因果效应, 这里我们在$\tau$左上方标记$\dagger$以说明它是ATT而非ATE. 仍以药物实验为例, $^\dagger\tau$指的是在服药的组群中, 那些服药个体的健康程度与他们如果不服药的健康程度之差在总体中的平均值.

为了估计出ATE或ATT, 我们还需要 **共同支撑域(common support)** 假设: 对于总体的一切协变量值$X=x$, 总有$0<\mathbb{P}[D=1|X=x]<1$. 该假设排除了处置状态在某些协变量值上没有变化的可能性. 实践中, 我们经常去掉某些处置可能性过高或过低的观测, 以减少被估效应的不稳定性, 这相当于牺牲外部有效性来增强内部有效性.

非混淆情形假设(C.5)看起来似乎难以实现, 因为协变量$X_i$可以是任意的随机向量. 然而, Rosenbaum and Rubin (1983)证明了, 使用 **倾向得分 (propensity score)**
$$
p(x)=\mathbb{P}[D_i=1|X_i=x]
$$
可以使该假设更加容易处理, 此时假设(1)可以用
$$
\left.\{Y_i(0),Y_i(1)\}\perp D_i\,\right|\,p(X_i)
$$
进行替代, 二者是等价的. 

基于倾向得分, 在SUTVA, 非混淆情形以及共同支撑域假设成立的情况下, 研究者可以使用 **倾向得分匹配 (Propensity Score Matching, PSM)** 和 **逆概率加权 (Inverse Probability Weighing, IPW)** 等方法来估计ATE或ATT.

### 倾向得分匹配

PSM的主要思想比较简单, 当处置组和控制组的倾向得分相同时, 可以认为它们的可观测特征分布没有差别. 以ATT为例, 根据不同的协变量值$x$, 可以得到
$$
\begin{align*}
^\dagger\tau(x)&=\mathbb{E}[Y_i(1)|D_i=1,X_i=x]-\mathbb{E}[Y_i(0)|D_i=1,X_i=x] \\
&=\mathbb{E}[Y_i|D_i=1,p(X_i=x)]-\mathbb{E}[Y_i|D_i=0,p(X_i=x)]
\end{align*}
$$
加权即可得到
$$
^\dagger\tau=\sum_{x\in\mathcal{X}}{^\dagger}\tau(x)\cdot\mathbb{P}[p(X_i=x)|D_i=1]
$$
其中$\mathcal{X}$是$X_i$取值的离散空间且$|\mathcal{X}|<\infty$, 如果$X_i$包含连续型随机变量, 那么将求和符号换为Lebesgue积分. 这里在$\tau$的左上角标记$^\dagger$说明它表示的是ATT而非ATE.

如何估计$^\dagger\hat{\tau}$呢? 由于观测数据的倾向得分并非RCT那样是已知的, 故而首先需要对其估计, 通常使用Probit模型、Logit模型或非参数方法; 然后根据倾向得分将样本划分为若干区间, 使得每个区间里处置组和控制组的$p(X)$均值相同; 再评估共同支撑域假设是否成立, 并选择匹配方法 (分块匹配、最近邻匹配、核匹配等); 最终计算
$$
^\dagger\hat{\tau}=n_1^{-1}\sum_{i\in I_1\cap S_p}\left(Y_i-\sum_{j\in I_0\cap S_p}w_{ij}Y_j\right)
$$
其中$n_1$为处置组样本个数, $I_0$和$I_1$分别为控制组和处置组, $i$和$j$分别代表个体位于处置组和控制组, $S_p$为共同支撑域, $w_{ij}$为匹配权重 (不同匹配方法的权重不同).

### 参考文献

Rosenbaum P R, Rubin D B. The Central Role of The Propensity Score in Observational Studies for Causal Effects[J]. Biometrika, 1983, 70(1): 41-55.
