---
title: 无混淆情形
published: 2024-05-17
tags: [计量经济学, 因果推断]
category: 经济学
draft: false
---

### 基本概念

尽管RCT的效力是最高的, 但RCT花费的代价也是巨大的, 甚至某些情况下无法进行实验(总不能随机让一部分人上学, 另一部分回家种地), 随机处置指派不成立. 然而, 当控制了一系列可观测变量$X$后, 潜在结果可能独立于处置状态, 也即
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
其中$n_1$为处置组样本个数, $I_0$和$I_1$分别为控制组和处置组, $i$和$j$分别代表个体位于处置组和控制组, $S_p$为共同支撑域, $w_{ij}$​为匹配权重 (不同匹配方法的权重不同).

### 加总组别估计

这里介绍的加总组别估计(Aggregating Group-wise, AGG)和下一节的IPW殊途同归, 只是AGG适用于离散型协变量. 无混淆情形假设(C.5)变为
$$
\begin{equation}
  \{Y_i(0),Y_i(1)\}\perp D_i\,|\, X_i=x,\quad \forall x\in\mathcal{X} \tag{C.6}
\end{equation}
$$
此时
$$
\tau(x)=\mathbb{E}[Y_i(1)-Y_i(0)|X_i=x]
$$
类似地, 在SUTVA和无混淆情形下, 上式可以被识别为
$$
\tau(x)=\mathbb{E}[Y_i|D_i=1,X_i=x]-\mathbb{E}[Y_i|D_i=0,X_i=x]
$$
于是可以定义加总组别估计量
$$
\begin{align*}
\hat{\tau}_\text{AGG}&=\sum_{x\in\mathcal{X}}\frac{n_x}{n}\hat{\tau}(x) \\
\hat{\tau}_\text{AGG}(x)&=n_{x1}^{-1}\sum_{X_i=x,D_i=1}Y_i-n_{x0}^{-1}\sum_{X_i=x, D_i=0}Y_i
\end{align*}
$$
其中$n_x=|\{i:X_i=x\}|$, $n_{xd}=|\{i:X_i=x,D_i=d\}|$. 进一步可知
$$
\begin{equation}
  \sqrt{n_x}[\hat{\tau}(x)-\tau(x)]\xrightarrow{d} \mathcal{N}\left(0,\frac{\mathrm{var}[Y_i(0)|X_i=x]}{1-p(x)}+\frac{\mathrm{var}[Y_i(1)|X_i=x]}{p(x)}\right) \tag{C.7}
\end{equation}
$$
如果$\mathrm{var}[Y_i(d)|X_i=x]=\sigma^2(x)$不依赖于处置状态$d$, 那么
$$
\sqrt{n_x}[\hat{\tau}(x)-\tau(x)]\xrightarrow{d} \mathcal{N}\left[0,\frac{\sigma^2(x)}{p(x)(1-p(x))} \right]
$$
注意到$\hat{\tau}_\text{AGG}=\sum_{x\in\mathcal{X}}\hat{\pi}(x)\hat{\tau}(x)$, $\hat{\pi}(x)=n_x/n$, 经过一些代数运算后最终得到
$$
\sqrt{n}(\hat{\tau}_\text{AGG}-\tau)\xrightarrow{d} \mathcal{N}(0,V_\text{AGG})
$$
其中
$$
\begin{align}
V_\text{AGG}&=\mathrm{var}[\tau(X_i)]+\sum_{x\in\mathcal{X}}\pi^2(x)\frac{1}{\pi(x)}\frac{\sigma^2(x)}{p(x)(1-p(x))} \nonumber \\
&=\mathrm{var}[\tau(X_i)]+\mathbb{E}\left[\frac{\sigma^2(X_i)}{p(X_i)(1-p(X_i))}\right] \tag{C.8}
\end{align}
$$
并且$\pi(x)=\mathbb{P}[X_i=x]$​.

### 逆概率加权估计

如果协变量是连续型的, 那么需要将假设(C.6)替换为
$$
\begin{equation}
  \{Y_i(0),Y_i(1)\}\perp D_i\,|\, p(X_i) \tag{C.9}
\end{equation}
$$
此时我们有ATE的IPW估计量[^2]

[^2]: 如果要估计ATT, 那么IPW估计量变为$\displaystyle ^\dagger\hat{\tau}_{\text{IPW}}=n_1^{-1}\sum_{i=1}^{n}\left[D_iY_i-\frac{(1-D_i)Y_i\hat{p}(X_i)}{1-\hat{p}(X_i)}\right]$.


$$
\begin{equation}
  \hat{\tau}_\text{IPW}=n^{-1}\sum_{i=1}^{n}\left[\frac{D_iY_i}{\hat{p}(X_i)}-\frac{(1-D_i)Y_i}{1-\hat{p}(X_i)}\right] \tag{C.10}
\end{equation}
$$
为了分析它的渐近性质, 我们首先给出理想IPW估计量
$$
\hat{\tau}_\text{IPW}^\ast=n^{-1}\sum_{i=1}^{n}\left[\frac{D_iY_i}{{p}(X_i)}-\frac{(1-D_i)Y_i}{1-{p}(X_i)}\right]
$$
根据共同支撑域假设, 存在$0<\eta<1$使得对于任意$x\in\mathcal{X}$都有
$$
\begin{equation}
  \eta\leq p(x)\leq 1-\eta \tag{C.11}
\end{equation}
$$
根据SUTVA和无混淆情形可知
$$
\mathbb{E}[\hat{\tau}^\ast_\text{IPW}]=\mathbb{E}[Y_i(1)-Y_i(0)]
$$
由此可知理想IPW估计量是无偏的.

进一步, 为了分析$\hat{\tau}_\text{IPW}^\ast$的方差, 可以选取$c(x)$使得以下分解成立
$$
\begin{align}
\begin{split}
Y_i(0)&=c(X_i)-[1-p(X_i)]\tau(X_i)+e_i(0),\quad \mathbb{E}[e_i(0)|X_i]=0 \\
Y_i(1)&=c(X_i)+p(X_i)\tau(X_i)+e_i(1),\quad \mathbb{E}[e_i(1)|X_i]=0
\end{split}
\tag{C.12}
\end{align}
$$
并且还假设$\mathrm{var}[e_i(d)|X_i=x]=\sigma^2(x)$不依赖于处置状态$d$, 于是
$$
\begin{align*}
n\mathrm{var}(\hat{\tau}_\text{IPW}^\ast)=\mathbb{E}\left[\frac{c^2(X_i)}{p(X_i)(1-p(X_i))}\right]+\mathrm{var}[\tau(X_i)]+\mathbb{E}\left[\frac{\sigma^2(X_i)}{p(X_i)(1-p(X_i))}\right]
\end{align*}
$$
综上可知, 当$n\to\infty$时有
$$
\sqrt{n}\left(\hat{\tau}_\text{IPW}^\ast-\tau\right)\xrightarrow{d} \mathcal{N}(0,V_\text{IPW}^\ast)
$$
其中
$$
V_\text{IPW}^\ast=\mathbb{E}\left[\frac{c^2(X_i)}{p(X_i)(1-p(X_i))}\right]+\mathrm{var}[\tau(X_i)]+\mathbb{E}\left[\frac{\sigma^2(X_i)}{p(X_i)(1-p(X_i))}\right]
$$
根据式(C.8})可知
$$
\begin{equation}
  V_\text{IPW}^\ast=V_\text{AGG}+\mathbb{E}\left[\frac{c^2(X_i)}{p(X_i)(1-p(X_i))}\right] \tag{C.13}
\end{equation}
$$
由此可见, 除非$c(x)$处处为0, 否则理想IPW估计量的渐近方差总大于AGG估计量的. 事实上, AGG估计量$\hat{\tau}_\text{AGG}$是IPW估计量的特例, 只需在式(\ref{eq11.11})中令$\hat{p}(x)=n_{x1}/n_1$即可.

综上可知, $\hat{\tau}_\text{AGG}$作为一致性的可行IPW估计量, 它表现得总比理想IPW估计量更好, 这是因为估计得到的倾向得分修正了$D_i$抽样分布的局部变异性, 也即它考虑每组实际接受处置的个体数量.

现在来看IPW和线性回归模型的关系, 在经典线性回归分析中, 可以写出
$$
Y_i\sim X_i'\beta+D_i\tau
$$
来估计非随机化的处置$D_i$的因果效应, 具体需要用到OLS. 根据前面章节的讨论, $\tau$是控制了$X_i$后$D_i$的因果效应 (ATE而非ATT).

值得注意的是, 如果线性回归模型是误设的, 那么没有理由认为$\hat{\tau}_\text{OLS}$​会收敛到一个可以被解释为因果效应的值, 而只要倾向得分的估计是正确的, IPW在无混淆情形(C.9)仍是ATE的一致估计量.

### 双重稳健估计

同样保持SUTVA, 无混淆情形及共同支撑域假设不变, 如果令
$$
\begin{align*}
\mu_{0}(x)&=\mathbb{E}[Y_i|X_i=x,D_i=0] \\
\mu_{1}(x)&=\mathbb{E}[Y_i|X_i=x,D_i=1]
\end{align*}
$$
那么ATE可以表示为$\tau=\mathbb{E}[\mu_{1}(x)-\mu_{0}(x)]$, 我们可以用参数或者非参数方法得到$\mu_{0}(x)$和$\mu_{1}(x)$的一致估计量, 于是$\tau$的回归估计量为
$$
\hat{\tau}_\text{REG}=n^{-1}\sum_{i=1}^{n}\,[\hat{\mu}_{1}(X_i)-\hat{\mu}_{0}(X_i)]
$$
与之前类似, 如果DGP是线性的, 那么$\hat{\mu}_{d}(x)$可由OLS得到.

由于IPW估计量的一致性依赖于倾向得分估计的正确性, REG估计量的一致性依赖于模型设定, 我们可以将这两个估计量结合为 **增广逆概率加权(Augmented IPW)** 估计量
$$
\hat{\tau}_\text{AIPW}=n^{-1}\sum_{i=1}^{n}\left[\hat{\mu}_{1}(X_i)-\hat{\mu}_{0}(X_i)+D_i\frac{Y_i-\hat{\mu}_{1}(X_i)}{\hat{p}(X_i)}-(1-D_i)\frac{Y_i-\hat{\mu}_{0}(X_i)}{1-\hat{p}(X_i)}\right]
$$
可以证明, 只要$\hat{p}(X_i)$和$\hat{\mu}_{d}(X_i)$中有一个是一致估计量, 那么$\hat{\tau}_\text{AIPW}$也是一致的, 表明AIPW估计量具有(弱的)双重稳健性.

注意, 双重稳健的AIPW估计量只保证了一致性, 但通常我们还关心收敛率和置信区间, 并且依赖于机器学习的现代统计模型通常要同时得到$\mu_d(x)$和$p(x)$​的一致估计量, 因此AIPW估计量可能并不是那么有用.

> 无论是PSM、AGG、IPW还是双重稳健的AIPW, 都是基于SUTVA、无混淆情形和共同支撑域假设的估计方法, 它们无法解决由于不可观测异质性带来的偏误.

### 参考文献

Rosenbaum P R, Rubin D B. The Central Role of The Propensity Score in Observational Studies for Causal Effects[J]. Biometrika, 1983, 70(1): 41-55.

邱嘉平. 因果推断实用计量方法[M]. 北京: 高等教育出版社, 2020.
