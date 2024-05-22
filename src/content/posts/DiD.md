---
title: 双重差分
published: 2024-05-22
tags: [因果推断]
category: 经济学
draft: false
---

### 基本情形

尽管RCT是估计处置效应的最清楚和最直观的方法, 但出于技术难度和道德伦理的限制, 很难运用到实践中. 除了RCT之外, 几种被称为 **准实验设计(quasi-experimental design)** 的方法被证明也能较好地估计因果效应, **双重差分(Difference-in-Differences, DiD)** 就是其中的一种, 主要在政策评估领域中用来估计ATT.

首先介绍经典的DiD, 研究者想知道某项在$t=2$时期实施的政策$D_i$对结果变量$Y_i$​​的影响, 也即ATT[^1]

[^1]: 这里的$Y_{i,t}(d)$表示个体$i$在$t$时刻的潜在结果.

$$
^\dagger\tau=\mathbb{E}[Y_{i,2}(1)-Y_{i,2}(0)|D_i=1]
$$

在SUTVA成立的情况下, ATT是良好定义的, 但我们无法识别出它. 在缺乏实施RCT的情境下, 需要做出一些额外假设才能正确识别ATT.

:::note[假设条件1]

平行趋势: $\mathbb{E}[Y_{i,2}(0)-Y_{i,1}(0)|D_i=1]=\mathbb{E}[Y_{i,2}(0)-Y_{i,1}(0)|D_i=0]$;

无预期效应: 对于所有$D_i=1$的个体$i$都有$Y_{i,1}(0)=Y_{i,1}(1)$​.

:::

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

### 交错时间处置

在经典DiD的框架下, 政策在同一时刻发生, 且只包含了处置组和控制组, 然而政策可能具有滞后性, 因此经典DiD不再适用. 有鉴于此, 我们可以将$2\times2$的经典DiD推广至多维, 也即存在多个组群在不同时点受到政策冲击.

假设时间跨度为$T+1$, 每个时点由$t=0,\cdots,T$标记, 个体$i$可以在任意$t\ge0$的时点受到二元处置, 并且处置是 **吸收态(absorbing state)**, 也即一旦个体接受处置, 那么它在剩余的时间上保持被处置的状态. 我们使用$D_{i,t}$作为个体$i$在时点$t$是否接受处置的二元指示符, $G_i=\min\{t:D_{i,t}=1\}$为个体$i$首次接受处置的时点, 同在$G_i$接受处置的个体$i$构成一个组群, $G_i=\infty$表示个体$i$从未接受处置. 由于处置是吸收的, 因此对于一切$t\ge G_i$都有$D_{i,t}=1$.

我们需要推广潜在结果的表示方法. 设$\mathbf{0}_s$和$\mathbf{1}_s$分别为$s$维的0向量和1向量, 如果个体$i$在时点$g$首次接受处置, 那么它在$t$时刻的潜在结果为$Y_{i,t}(\mathbf{0}_{g-1},\mathbf{1}_{T-g+1})$, 而用$Y_{i,t}(\mathbf{0}_T)$表示从未接受处置的潜在结果, 分别简写为$Y_{i,t}(g)$以及$Y_{i,t}(\infty)$, 并且
$$
Y_{i,t}(g)=Y_{i,t}(\infty)+\sum_{0\leq g\leq T}\,[Y_{i,t}(g)-Y_{i,t}(\infty)]\cdot 1[G_i=g]
$$
同样地, 为了正确识别处置效应, 我们需要新的平行趋势和无参与效应的假设, 它们是前文所述假设的推广.

:::note[假设条件2]

不可逆性: 对于一切个体$i$都有$Y_{i,1}=Y_{i,1}(0)$, 并且$Y_{i,t-1}(1)$意味着$Y_{i,t}(1)$.

平行趋势: 对于一切$t\neq t'$, $g\neq g'$都有$\mathbb{E}[Y_{i,t}(\infty)-Y_{i,t'}(\infty)|G_i=g]=\mathbb{E}[Y_{i,t}(\infty)-Y_{i,t'}(\infty)|G_i=g']$.

无预期效应: 对于一切$i$和$t<g$都有$Y_{i,t}(g)=Y_{i,t}(\infty)$.

:::

这里的不可逆性意味着处置状态是吸收态, 个体一旦接受处置, 则在后续阶段一直属于处置组.

假设处置时点为$g$, 考虑使用如下静态TWFE模型
$$
Y_{i,t}=\alpha_i+\lambda_t+D_{i,t}\beta_\text{post}+e_{i,t}
$$
于是个体$i$在$t$时刻的ATT可以表示为
$$
^\dagger\tau_{i,t}(g)=Y_{i,t}(g)-Y_{i,t}(\infty)
$$
如果对于所有的个体$i$, 当$t\ge g$时总有$^\dagger\tau_{i,t}(g)={^\dagger}\tau$, 并且不论处置时点过去了多久, 处置总有相同的效应, 那么在假设条件2下, 回归系数$\beta_\text{post}={^\dagger}\tau$​.

但是当处置效应具有异质性时, OLS估计量$\hat{\beta}_\text{post}$不再可行. 简单起见, 假设异质性以以下形式呈现
$$
^\dagger\tau_{i,t}(g)=\sum_{s\ge0}{^\dagger}\tau_s1[t-g=s]
$$
也即所有个体在接受处置后的第$s$个时期具有相同的处置效应$^\dagger\tau_s$. 此时, $\beta_\text{post}$与$\tau_s$的非凸加权平均联系在一起, 例如$\beta_\text{post}=\sum_s\omega_s^\dagger\tau_s$, 其中权重$\omega_s$之和为1但可能为负.

现在问题来了, 如果所有的$\tau_s$均为正, 但是某个权重值$\omega_s$为负, 会不会导致回归系数$\beta_\text{post}$为负? 答案是肯定的. Goodman-Bacon (2021)证明了概率极限
$$
\underset{n\to\infty}{\text{plim}}\hat{\beta}_\text{post}=\text{VWATT}-\text{VWCT}+\Delta \text{ATT}
$$
其中$\mathrm{VWATT}$是TWFE-DiD估计量所能解释的处置组平均处置效应, $\mathrm{VWCT}$是方差加权的共同趋势, 并且它在平行趋势假设成立的情况下为0, $\Delta\text{ATT}$等于各组群在处置前后的处置结果变化的加权和, 也是负权重的来源.[^2] 正如上面提到的, 如果处置效应是同质的 (也即ATT跨组群和时点保持不变), 那么$\Delta\text{ATT}=0$, 此时$\beta_\text{post}$正确识别了因果关系, 使用OLS估计静态TWFE模型是合理的.

[^2]: 如果$^\dagger\tau_{i,t}(g)$仅跨时点不变而跨组群可变, 此时仍有$\Delta\text{ATT}=0$, 但是VWATT不等于样本ATT.



为何会出现负权重? 根据FWL定理可知${\beta}_{\text{post}}$的OLS估计量为
$$
\hat{\beta}_\text{post}=\frac{\sum_{i,t}(D_{i,t}-\hat{D}_{i,t})Y_{i,t}}{\sum_{i,t}(D_{i,t}-\hat{D}_{i,t})^2}
$$
其中$\hat{D}_{i,t}$是出自回归$D_{i,t}=\alpha_i+\lambda_t+u_{i,t}$的$D_{i,t}$的拟合值. 由于上式分母始终为正, 以及$Y_{i,t}=Y_{i,t}(\infty)+\tau_{i,t}(g)$, 因此如果$D_{i,t}=1$且$D_{i,t}-\hat{D}_{i,t}<0$, 那么$\tau_{i,t}(g)$在$\hat{\beta}_\text{post}$中取得负权重, 即使个体$i$在$t$时刻接受处置.

进一步, 经过某些代数运算可得
$$
\hat{D}_{i,t}=\bar{D}_i+\bar{D}_t-\bar{D}
$$
其中$\bar{D}_i=T^{-1}\sum_t D_{i,t}$表示个体$i$的处置$D$在时间上的平均, $\bar{D}_t=N^{-1}\sum_i D_{i,t}$表示时点$t$的处置$D$在截面上的平均, $\bar{D}=(NT)^{-1}\sum_{i,t}D_{i,t}$表示处置$D$在整个截面和时间上的平均.

如果个体在几乎所有时点都是处置状态, 那么$\bar{D}_i\approx 1$, 如果某时点几乎所有个体都被处置, 那么$\bar{D}_t\approx 1$, 在二者同时成立的情况下有$\hat{D}_{i,t}\approx 2-\bar{D}$, 如果在某段时间内有部分未接受处置的个体存在, 那么$\hat{D}_{i,t}>1$. 由此可见, 早期接受处置的个体在后期更容易产生负权重, 使用较早接受处置的组群作为控制组也被称为 **禁止比较(forbidden comparison)**.

既然非同质处置效应会导致静态TWFE模型失效, 我们自然转向使用动态TWFE模型, 通常称为 **事件研究设计(event study design)**, 具体如下
$$
\begin{equation}
  Y_{i,t}=\alpha_i+\lambda_t+\sum_{l=-K}^{-2}\mu_lD_{i,t}^l+\sum_{l=0}^{L}\mu_lD_{i,t}^l+e_{i,t} \tag{C.14}
\end{equation}
$$
其中, $D_{i,t}=1[t-G_i=l]$是相对时间变量, $G_i$是个体$i$接受处置的时点, $l$是相对处置时点. 在上式中, 相对时间变量$D_{i,t}$被分为了两组, 一组代表处置前 ($l\leq-2$), 一组代表处置后 ($l\ge0$), 各个$\mu_g$体现了动态效应 (dynamic effects). 此外, 为了避免完全多重共线性, 选取$l=-1$作为基期. 这里的$K=G^{\max}-1$, $L=T-G^{\min}$, 并且$G^{\max}$和$G^{\min}$分别表示最晚和最早接受处置的时间.

然而, 单纯的事件研究仅能解决同质性处置效应路径的TWFE估计偏误,[^3] 实践中经常用到的是稳健估计量.

[^3]: 同质性处置效应路径指的是, 处置效应的异质性仅随时间流动产生动态变化且路径对每个$g$相同.

### Callaway-Sant'Anna估计量

对于随时间和组群变化的一般性异质性处置效应路径, 可以使用Callaway and Sant'Anna (2021)提出的稳健估计量来解决TWFE估计偏误.

具体可以考虑
$$
\theta(g,t)=\mathbb{E}[Y_{t}(g)-Y_{t}(\infty)|G_g=1]
$$
这里为了简单起见省略了下标$i$, $G_g$是个体$i$在时点$g$是否接受处置时的指示符, 上式表示在时点$g$首次接受处置的那批个体在时点$t$的ATT. 由于因果推断的根本难题, $\theta(g,t)$是无法直接估计的, 但是可以由 **回归调整(Regression Adjustment, RA)**, 逆概率加权和双重稳健方法来估计.

为了通过上述方法得到识别ATT, 需要先定义控制组, 而它的选择有两种: 其一是从未处置$C^\text{NEV}=G_\infty$, 其二是尚未处置$C^\text{NY}=(1-G_g)(1-D_t)$, 以下统一记作$C_{g,t}^\ast$.

考虑加入用以控制可观测特征的协变量$X$, 下面给出RA, IPW和DR的待估计量
$$
\begin{align*}
\theta_\text{RA}(g,t)&=\mathbb{E}\left[\frac{G_g}{\mathbb{E}[G_g]}\{Y_t-Y_{g-1}-m_{g,t}({X})\}\right] \\
\theta_\text{IPW}(g,t)&=\mathbb{E}\left[\left(\frac{G_g}{\mathbb{E}[G_g]}-\frac{\frac{p_{g,t}(X)C_{g,t}^\ast}{1-p_{g,t}(X)}}{\mathbb{E}\left[\frac{p_{g,t}(X)C_{g,t}^\ast}{1-p_{g,t}(X)}\right]}\right)(Y_t-Y_{g-1})\right] \\
\theta_{\text{DR}}(g,t)&=\mathbb{E}\left[\left(\frac{G_g}{\mathbb{E}[G_g]}-\frac{\frac{p_{g,t}(X)C_{g,t}^\ast}{1-p_{g,t}(X)}}{\mathbb{E}\left[\frac{p_{g,t}(X)C_{g,t}^\ast}{1-p_{g,t}(X)}\right]}\right)\{Y_t-Y_{g-1}-m_{g,t}(X)\}\right]
\end{align*}
$$
其中$m_{g,t}(X)=\mathbb{E}[Y_t-Y_{g-1}|X,C_{g,t}^\ast=1]$, $p_{g,t}(X)=\mathbb{P}[G_g=1|X,G_g+C_{g,t}^\ast=1]$. 在温和的正则条件下, Callaway and Sant'Anna (2021)证明了
$$
\theta(g,t)=\theta_\text{RA}(g,t)=\theta_\text{IPW}(g,t)=\theta_\text{DR}(g,t)
$$
由于RA, IPW和DR只依赖于$(Y,X,G_g,C_{g,t}^\ast)$, 因此可以通过它们得到$\theta(g,t)$​的估计量.

然而, 在长时间跨度和多处理时点的情况下, 报告每一个$\theta(g,t)$是很麻烦的事情, 并且可能不准确, Callaway and Sant'Anna (2021)提供了将不同$\theta(g,t)$进行加总的机制
$$
\theta=\sum_g\sum_{t=2}^Tw(g,t)\cdot\theta(g,t)
$$
其中$w(g,t)$是某个精心挑选的权重函数, 用以解决特定的实证问题. 特别地, 设$l=t-g$为表示在接受处置后逝去的时间, 则关于$l$的一种加总方式为
$$
\theta_{\text{es}}(l)=\sum_g1[g+l\leq T]\cdot\mathbb{P}[G=g|G+l\leq T]\cdot\theta(g,t)
$$
称为 **事件研究参数 (event-study parameter)**, 它给出了在不同处置组群中, 在接受处置后$e$个时期的处置效应的加权平均值.
