---
title: 随机控制实验
published: 2024-05-16
tags: [计量经济学, 因果推断]
category: 经济学
draft: false
---

### 基本概念

考虑如下场景, 某大型制药公司研发了一款新药并想测试其疗效, 病人服用该药后病情果 真得到明显改善, 我们可以据此得出该药有效的结论吗? 显然是不能的, 因为根本不能明确病 情改善得益于药物, 而非其他混杂因素. 那么究竟应该怎么做才能真正测试疗效? 这就是因果推断的出发点.

设$D_i\in\{0,1\}$表示个体$i$是否接受处置 (服药), 其中$D_i=1$表示接受, $D_i=0$表示不接受. 再设$Y_{i,t}$代表个体$i$在$t$时刻的结果变量 (病人健康), 如果个体$i$在基期未接受处置且下一期也未接受, 此时$Y_{i,t}(0)$表示个体$i$在$t$时刻的 **潜在结果 (potential outcome)** 如果个体$i$在基期未接受处置而在下一期接受, 则此时个体$i$在$t$时刻的潜在结果为$Y_{i,t}(1)$. 为了简化符号. 特别地, 当只有两期数据时, 下标$t$可以省略, $Y_{i,2}(1)$和$Y_{i,2}(0)$分别简化为$Y_i(1)$和$Y_i(0)$.

下面我们只考虑两期数据的情形. 对于个体$i$, 处置$D$​对她的 **处置效应 (treatment effect)** 为[^1]
$$
\Delta_{i}=Y_{i}(1)-Y_{i}(0)
$$
值得注意的是, 由于我们无法同时观测到$Y_{i}(0)$和$Y_{i}(1)$, 故而$\Delta_{i}$是完全不可知的. 事实上, 我们只能观测到
$$
Y_{i}=D_iY_{i}(1)+(1-D_i)Y_{i}(0)
$$
然而, 我们或许仍能用 **随机控制实验 (Randomized Controlled Trial, RCT)** 来获取$\Delta_{i}$​​的某些性质. 在某些情况下, 大型的随机实验可以让我们得到 **平均处置效应 (Average Treatment Effect, ATE)**
$$
\begin{equation}
\tau=\mathbb{E}[Y_{i}(1)-Y_{i}(0)] \tag{2}
\end{equation}
$$
这里的$\tau$指的是$\Delta_{i}$在总体上的平均值. 继续之前的例子, 个体服药效果$\Delta_{i}$等于个体$i$在$t$时刻服药后的健康程度减去个体$i$在$t$时刻没有服药的健康程度, 因为她不能既服药又不服药, 所以$\Delta_{i}$无法观测, 但是在一定假设下, RCT使得我们能够得到个体服药效果在总体中的平均值$\tau$​.

什么样的假设? 只需要求i.i.d.样本$\{Y_{i},D_i\}_{i=1}^n$​满足 **个体稳定处置效应假设 (SUTVA)** 以及 **随机处置指派 (random treatment assignment)**, 也即
$$
\begin{align*}
Y_{i}&=Y_{i}(D_i) \\
\{Y_{i}(0),Y_{i}(1)\} &\perp D_i
\end{align*}
$$
其中SUTVA表明任何个体的潜在结果不随其它个体是否接受处置而变化, 并且每一个体所接受的处置水平是唯一的, 因此处置所导致的潜在结果也是唯一 (该假设无法验证是否成立); 随机处置指派表明处置独立于潜在结果, 是完全随机化的.

基于SUTVA和随机处置指派, ATE可以被识别为
$$
\begin{equation}
  \tau^\ast=\mathbb{E}[Y_i|D_i=1]-\mathbb{E}[Y_i|D_i=0]
\end{equation}
$$
这是因为可以将$\tau^\ast$分解为
$$
\tau^\ast=\tau+\text{bias}
$$
其中
$$
\begin{align*}
\text{bias}&=(\mathbb{E}[Y_i(1)|D_i=1]-\mathbb{E}[Y_i(1)|D_i=0])\mathbb{P}[D_i=0] \\
&\quad +(\mathbb{E}[Y_i(0)|D_i=1]-\mathbb{E}[Y_i(0)|D_i=0])\mathbb{P}[D_i=1]
\end{align*}
$$
根据随机处置指派可知$\text{bias}=0$, 从而$\tau^\ast=\tau$. 根据式(2) 我们可以得到 **均值差 (Difference-in-Means, DM)** 估计量
$$
\hat{\tau}_{\text{DM}}=n_1^{-1}\sum_{D_i=1}Y_i-n_0^{-1}\sum_{D_i=0}Y_i
$$
这里的$n_d=|\{i:D_i=d\}|$. DM估计量具有无偏性
$$
\mathbb{E}[\hat{\tau}_\text{DM}]=\mathbb{E}[Y_i(1)]-\mathbb{E}[Y_i(0)]=\tau
$$
进一步, 在标准的CLT条件下可以证明
$$
\sqrt{n}(\hat{\tau}_{\text{DM}}-\tau)\xrightarrow{d}\mathcal{N}(0,V_{\text{DM}})
$$
其中$V_\text{DM}=\mathrm{var}[Y_i(0)]/\mathbb{P}[D_i=0]+\mathrm{var}[Y_i(1)]/\mathbb{P}[D_i=1]$.

[^1]: 这里暗含了$\Delta_{i}$是存在的, 对个体$i$的处置或不处置仅影响自己, 不会影响到其他个体. 这个隐含的假设在医学实验中是可行的, 但是在社会科学或经济学中似乎不太合理.

### 线性回归模型与RCT的关系

初级的计量经济学早已讲授过线性回归模型，这里将介绍它与RCT的关系. 仍考虑两期数据, 假设 **数据生成过程(DGP)** 为
$$
\begin{equation}
  Y_i(d)=c_{(d)}+X_i'\beta_{(d)}+e_i(d)
\end{equation}
$$
并且满足$\mathbb{E}[e_i(d)|X_i]=0$且$\mathrm{var}[e_i(d)|X_i]=\sigma^2$. 简单起见, 我们还假设
$$
\begin{equation}
  \mathbb{P}[D_i=0]=\mathbb{P}[D_i=1]=\frac{1}{2}
\end{equation}
$$
此时
$$
\begin{align*}
V_\text{DM}&=\mathrm{var}[Y_i(0)]/\mathbb{P}[D_i=0]+\mathrm{var}[Y_i(1)]/\mathbb{P}[D_i=1] \\
&=4\sigma^2+||\beta_{(0)}+\beta_{(1)}||_A^2+||\beta_{(0)}-\beta_{(1)}||_A^2
\end{align*}
$$
最后, 我们还可以不失一般性地假设$\mathbb{E}[X_i]=0$. 另一方面, 根据DGP可知ATE可以表示为
$$
\tau=\mathbb{E}[Y_i(1)-Y_i(0)]=c_{(1)}-c_{(0)}+\mathbb{E}[X_i]\cdot[\beta_{(1)}-\beta_{(0)}]
$$
自然而然, 可以得到$\tau$的OLS估计量
$$
\hat{\tau}_\text{OLS}=\hat{c}_{(1)}-\hat{c}_{(0)}+\bar{X}'[\hat{\beta}_{(1)}-\hat{\beta}_{(0)}]
$$
这里的$\bar{X}=n^{-1}\sum_{i=1}^{n}X_i$. 进一步可以证明
$$
\sqrt{n}(\hat{\tau}_\text{OLS}-\tau)\xrightarrow{d} \mathcal{N}(0,V_\text{OLS})
$$
并且$V_\text{DM}=V_\text{OLS}+||\beta_{(0)}+\beta_{(1)}||_A^2$​, 可见使用OLS减少了线性DGP情况下的渐近误差. 这一结论毫不令人惊讶, 因为我们已经假设了DGP是线性的.

进一步, 对于任意DGP
$$
Y_i(d)=\mu_{d}(X_i)+e_i(d)
$$
仍然假定$\mathbb{E}[e_i(d)|X_i]=0$, $\mathrm{var}[e_i(d)|X_i]=\sigma^2$, $\mathbb{P}[D_i=1]=1/2$. 易知
$$
\sqrt{n}(\hat{\tau}_\text{DM}-\tau)\xrightarrow{d} \mathcal{N}(0,V_\text{DM2})
$$
$
其中
$
$$
V_\text{DM2}=4\sigma^2+2\mathrm{var}[\mu_{0}(X_i)]+2\mathrm{var}[\mu_{1}(X_i)]
$$
另一方面, 考虑如下OLS估计量
$$
\begin{align*}
\hat{\tau}_\text{OLS2}&=n^{-1}\sum_{i=1}^{n}\left[\hat{\mu}_{1}(X_i)-\hat{\mu}_{0}(X_i)\right] \\
\hat{\mu}_{d}(X_i)&=\hat{c}_{(d)}+X_i'\hat{\beta}_{(d)}
\end{align*}
$$
可以证明
$$
\sqrt{n}(\hat{\tau}_\text{OLS2}-\tau)\xrightarrow{d} \mathcal{N}(0,V_\text{OLS2})
$$
并且$V_\text{DM2}=V_\text{OLS2}+||\beta^\ast_{(0)}+\beta^\ast_{(1)}||^2_A$, 这里的$\beta^\ast_{(d)}$满足
$$
[c^\ast_{(d)},\beta^\ast_{(d)}]=\arg\min_{c,\beta}\mathbb{E}[(Y_i(d)-X_i'\beta-c)^2]
$$
换言之, 无论$\mu_d(x)$是否是线性函数, OLS几乎总能减少DM的渐近方差! 这也解释了为何在RCT中使用线性回归模型来推断ATE: **在SUTVA和随机处置指派下, RCT可以正确识别ATE, 并且通过OLS得到的估计量通常比DM得到估计量更加渐近有效.**
