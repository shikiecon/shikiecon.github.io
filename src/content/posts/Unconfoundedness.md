---
title: 无混淆情形
published: 2024-05-16
tags: [计量经济学, 因果推断]
category: 经济学
draft: false
---

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

#### 参考文献

Rosenbaum P R, Rubin D B. The Central Role of The Propensity Score in Observational Studies for Causal Effects[J]. Biometrika, 1983, 70(1): 41-55.
