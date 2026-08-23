---
hide:
  - toc
---

# Neural Observer I

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 组会题目 | Stable Control of Uncertain Systems Using Trained Neural Network Observer |
| 时间 | 2025-10-22 |
| 关键词 | neural observer, ADRC, LMI, X-29 |

## 问题设定

我在这次组会中从飞行器受风场和其他扰动的稳定控制入手。任务分成三步：

- 建立带扰动项的飞行器模型
- 估计飞行器状态，重点是扰动状态
- 用估计状态设计控制器，实现稳定控制

无扰动模型与带扰动模型分别写作：

\[
\begin{aligned}
\dot{x}(t)&=Ax(t)+Bu(t),\\
y(t)&=Cx(t),
\end{aligned}
\qquad
\begin{aligned}
\dot{x}(t)&=Ax(t)+Bu(t)+d(t),\\
y(t)&=Cx(t).
\end{aligned}
\]

目标是让估计状态和扰动满足：

\[
\hat{x}'(t)\to x(t),\qquad
\hat{x}''(t)\to d(t).
\]

## Neural Observer

状态空间中的 \(x(t)\) 往往难以直接测量，输出 \(y(t)\) 更容易获得。因此，我采用基于输出反馈的神经网络观测器：

\[
\begin{aligned}
\dot{\hat{x}}_1(t)
&=A\hat{x}_1(t)+Bu(t)
+\pi_1(\epsilon^{-1}(y-\hat{y}))
+\hat{x}_2(t),\\
\dot{\hat{x}}_2(t)
&=\epsilon^{-1}\pi_2(\epsilon^{-1}(y-\hat{y})),\\
\hat{y}(t)&=C\hat{x}_1(t).
\end{aligned}
\]

Neural Observable 记作：

\[
\|x(t)-\hat{x}(t)\|\to0,\qquad t\to\infty.
\]

Neural Exponentially Observable 记作：

\[
\|x(t)-\hat{x}(t)\|
\le
Me^{-kt}\left(\|x(0)\|^2+\|\hat{x}(0)\|^2\right),
\qquad k>0.
\]

若能控能观的带扰动系统存在正定对称矩阵 \(P\) 满足对应 LMI，则有：

\[
\lim_{\epsilon\to0^+}\|x_i(t)-\hat{x}_i(t)\|=0,\qquad \forall t>T,
\]

\[
\lim_{t\to\infty}\|x_i(t)-\hat{x}_i(t)\|
\le O(\epsilon^{2-i}).
\]

## ADRC 连接

对于

\[
\dot{x}_1(t)=Bu(t)+f(t),\qquad \dot{f}(t)=h(t),
\]

扩张状态观测器可写成：

\[
\begin{aligned}
\dot{z}_1(t)&=Bu(t)+z_2(t)+\beta_1(x_1(t)-z_1(t)),\\
\dot{z}_2(t)&=\beta_2(x_1(t)-z_1(t)).
\end{aligned}
\]

误差动态为：

\[
\begin{aligned}
\dot{e}_1(t)&=-\beta_1e_1(t)+e_2(t),\\
\dot{e}_2(t)&=-\beta_2e_1(t)-h(t).
\end{aligned}
\]

这里 \(\beta_1,\beta_2\) 可以通过极点配置得到，用来调整观测误差收敛速度。

## X29-A 简单实验

X29-A 飞机模型使用 LTI 系统：

\[
\dot{x}=Ax+Bu+\omega,\qquad y=Cx+\nu.
\]

状态变量 4 维，控制输入 3 维，输出 5 维。 \(\omega,\nu\) 用于模拟控制和观测的高斯噪声。神经网络含 3 个隐藏层，每层 3 个神经元，激活函数为 \(\tanh(\cdot)\)。采样周期为 10 ms，初始状态为 \(x(0)=[5,5,5,5]\)，目标状态为 \(x(T)=[0,0,0,0]\)。

当网络维数扩大后，求解情况如下：

| 神经网络结构 | Solver 运行时间 | 备注 |
| --- | --- | --- |
| 3-3-3 | < 1 s | 可解 |
| 32-64-32 | 不稳定，最快 < 5 s | Marginal Infeasibility |
| 32-64-64-128-128-64-64-32 | N/A | 无法求解 |

## Point Training 与 LMI Training

Point Training 中只能随机选取离散误差代入训练。即使所有采样点满足条件，也还要说明周围区域的稳定性。

LMI Training 把负定约束和 \(M\) 中对角元素正定写进罚函数。当需要正定的矩阵出现负特征值时，罚函数给出 \(r=10^5\) 的惩罚系数。为避免权重过大，还加入范数正则化。

在实验图中，单独使用 Point Training 无法降低 MSE。Point 预训练之后再做 LMI Tuning，MSE 下降很快。只使用 LMI Training 的方法大约需要 5 倍时间。

</article>

<aside class="course-index" aria-label="白玉楼索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>论文</p><a href="neurips2026.html">NeurIPS2026（在投）</a></div>
  <div class="course-index__group"><p>组会</p><a href="lyge.html">LYGE</a><a class="is-active" href="neural-observer-i.html">Neural Observer I</a><a href="feedback-linearization.html">反馈线性化</a><a href="sima.html">SIMA</a><a href="neural-observer-ii.html">Neural Observer II</a><a href="neural-observer-iii.html">Neural Observer III</a><a href="dream2flow.html">Dream2Flow</a><a href="neural-observer-iv.html">Neural Observer IV</a></div>
</aside>
</div>
