---
forced_scheme: default
forced_primary: white
forced_accent: green
hide:
  - toc
---

# NeurIPS2026（在投）

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 标题 | Learning Provable Neural Network Observer for Uncertain Dynamical Systems |
| 状态 | 在投 |
| 会议 | NeurIPS 2026 |
| 主题 | 不确定动力系统中的可证明神经网络观测器 |

## 研究主题

在这篇稿件中，我研究带外部扰动和未建模动态的控制系统。控制器需要状态和扰动估计，神经网络观测器能拟合复杂不确定项，但 Lyapunov 稳定性证明通常要解 LMI。网络规模变大后，LMI 对应的 SDP 很慢，也容易出现数值困难。

我采用两阶段训练：

1. Point-Guided Lyapunov Pre-Training：在误差状态空间采样，先用点值 Lyapunov 损失训练高容量观测器。
2. LMI-Based Fine-Tuning：从预训练参数出发，微调到满足 \(H(\theta)\prec O\)，得到全局 Lyapunov 证书。

## 系统与观测器

不确定非线性动力系统写成：

\[
\begin{aligned}
\dot{x}(t) &= Ax(t)+Bu(t)+B_\omega K(x,d,t),\\
y(t) &= Cx(t).
\end{aligned}
\]

其中 \(K(x,d,t)\) 表示非线性动态和外部扰动。观测器用于同时估计状态和扰动：

\[
\begin{aligned}
\dot{\hat{x}}_1(t) &= A\hat{x}_1(t)+Bu(t)+B_\omega\hat{x}_2(t)+\pi_{\theta,1}\!\left(\epsilon^{-1}(y(t)-\hat{y}(t))\right),\\
\dot{\hat{x}}_2(t) &= \epsilon^{-1}\pi_{\theta,2}\!\left(\epsilon^{-1}(y(t)-\hat{y}(t))\right),\\
\hat{y}(t)&=C\hat{x}_1(t).
\end{aligned}
\]

误差变量定义为 \(\eta_1=\epsilon^{-1}(x-\hat{x}_1)\)、\(\eta_2=K(x,d,t)-\hat{x}_2\)。误差系统为：

\[
\dot{\eta}(t)=A_\epsilon\eta(t)-\nu(\eta,\theta)+\epsilon w(t).
\]

对二次 Lyapunov 函数 \(V(\eta)=\eta^\top P\eta\)，稿件中使用的点值上界为：

\[
F_V(\eta,\theta)
=\eta^\top(A_\epsilon^\top P+PA_\epsilon+\rho P)\eta
-2\nu(\eta,\theta)^\top P\eta
+2\epsilon M_w\|P\eta\|.
\]

若 \(F_V(\eta,\theta)\le 0\)，则有 \(\dot{V}\le -\rho V\)。

## 两阶段训练

第一阶段的损失函数为：

\[
L_{\mathrm{point}}(\theta)=
\frac{1}{N}\sum_{i=1}^{N}\left[F_V(\eta_i,\theta)+\xi\right]_+
+\gamma\|\theta\|^2.
\]

这里 \(\xi>0\) 是严格稳定裕度。若数据项为零，则每个采样点满足：

\[
F_V(\eta_i,\theta^\*)\le -\xi<0.
\]

第二阶段从 \(\theta_{\mathrm{pre}}\) 出发，使用 LMI 罚函数：

\[
L_{\mathrm{LMI}}(\theta)=
\phi\!\left(\lambda_{\max}(H(\theta))\right)+\beta\|\theta\|^2.
\]

训练目标是让 \(H(\theta)\prec O\)。这个条件成立后，观测误差在 Lyapunov 意义下收敛。

## 理论记录

局部稳定半径来自采样点附近的连续性。若 \(F_V(\eta_s,\theta)\le-\xi<0\)，在 Lipschitz 条件下存在邻域 \(B(\eta_s,r_s)\cap\mathcal{X}\)，使其中的点仍满足 \(F_V(\eta,\theta)\le0\)。当 \(C_2>0\) 时，半径可取二次方程正根：

\[
C_2r_s^2+C_1(\eta_s)r_s+F_V(\eta_s,\theta)=0.
\]

采样点形成的稳定区域记作：

\[
U_T=\bigcup_{i=1}^{T} B(\eta_i,r_i)\cap\mathcal{X}.
\]

若成功样本独立来自支撑整个 \(\mathcal{X}\) 的分布，则存在常数 \(N_{\mathrm{cov}}\) 和 \(q>0\)，满足：

\[
\mathbb{P}(\mathcal{X}\subset U_T)\ge 1-N_{\mathrm{cov}}(1-q)^T.
\]

因此，成功样本数量增加时，采样稳定区域覆盖整个误差状态域的概率趋近于 1。

## 实验结果

Quad-UAV 在地面效应扰动下的垂直跟踪误差如下：

| 方法 | 数据需求 | 训练时间 | 降落误差 / m | 起飞误差 / m |
| --- | --- | --- | --- | --- |
| Basic PID | 不需要 | — | \(2.2006\pm0.1456\) | \(0.1255\pm0.0266\) |
| PID + Neural Lander | 需要 | 88.2 s，每个任务重训 | \(0.5732\pm0.1188\) | \(0.2444\pm0.0297\) |
| PID + Neural Network Observer | 不需要 | 233.8 s，一次训练 | \(0.1471\pm0.0602\) | \(0.0002\pm0.0000\) |

WaterLily 流体环境中，AUV 在 von Kármán 涡街中跟踪轨迹。跟踪误差如下：

| 方法 | 跟踪误差 / m |
| --- | --- |
| Basic NMPC | \(1.4932\pm0.0199\) |
| NMPC + ESO | \(0.0959\pm0.0088\) |
| NMPC + Neural Network Observer | \(0.0499\pm0.0019\) |

X-29 飞机消融实验在 \(\sigma=0.5\) 扰动下记录了不同容量观测器的表现：

| 观测器 | 跟踪误差 / m | 成功率 |
| --- | --- | --- |
| Linear | \(0.2052\pm0.0402\) | 0.49 |
| Tiny NN | \(0.2056\pm0.0402\) | 0.49 |
| Small NN | \(0.0558\pm0.0312\) | 0.52 |
| Large NN | \(0.0118\pm0.0028\) | 0.56 |

训练时间消融如下：

| 方法 | 时间 / s | MSE / m |
| --- | --- | --- |
| Point-Guided | 99.53 | 1.0645 |
| LMI Gradient Descent | 2210.39 | 0.0707 |
| Point & LMI Tuning | 895.31 | 0.0499 |

最后，我把结论收束到神经网络观测器的训练效率、LMI 稳定证书和复杂扰动下的跟踪精度。两阶段训练让大网络可以进入 LMI 可行域，实验中也能减少 Quad-UAV 和 WaterLily 场景的跟踪误差。

</article>

<aside class="course-index" aria-label="白玉楼索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>论文</p><a class="is-active" href="neurips2026.html">NeurIPS2026（在投）</a></div>
  <div class="course-index__group"><p>组会</p><a href="lyge.html">LYGE</a><a href="neural-observer-i.html">Neural Observer I</a><a href="feedback-linearization.html">反馈线性化</a><a href="sima.html">SIMA</a><a href="neural-observer-ii.html">Neural Observer II</a><a href="neural-observer-iii.html">Neural Observer III</a><a href="dream2flow.html">Dream2Flow</a><a href="neural-observer-iv.html">Neural Observer IV</a></div>
</aside>
</div>
