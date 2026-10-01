---
hide:
  - toc
---

# Neural Observer IV

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 组会题目 | Point-to-Certificate Warm Starts for LMI-Constrained Learning |
| 时间 | 2026-08-17 |
| 关键词 | point-to-certificate, LMI-constrained learning, warm start, WaterLily |

## Background

神经网络观测器采用两阶段训练：

### Stage I：Point-Guided 预训练

在误差状态空间中采样 \(\eta_i\)，优化：

\[
L_{\mathrm{Point}}(\theta)=
\frac{1}{N}\sum_i\left[F_V(\eta_i,\theta)+\xi\right]_+
+\gamma\|\theta\|^2.
\]

其中 \(F_V(\eta_i,\theta)\) 是 Lyapunov 导数上界，\(\xi\) 是稳定裕度。该阶段用常规梯度训练快速获得具有局部稳定结构的高容量神经网络观测器。

### Stage II：LMI 微调

从预训练参数 \(\theta_{\mathrm{Pre}}\) 出发，优化：

\[
L_{\mathrm{LMI}}(\theta)
=\Phi(\lambda_{\max}(H(\theta)))+\beta\|\theta\|^2.
\]

当 \(H(\theta)\prec O\) 时，神经网络观测器满足全局 LMI 条件。

## LMI Problems: Optimization

我将两阶段训练拓展到控制器和其他 LMI 约束优化问题。闭环系统写为：

\[
\dot{x}=Ax+B(\pi(x)+e).
\]

RL 策略需要位于梯度安全集合：

\[
\frac{\partial \pi_i(x)}{\partial x_j}\le \xi_{ij}.
\]

稳定性 LMI 约束写成：

\[
\min_{P,\lambda,\gamma}\gamma
\quad \mathrm{s.t.}\quad
H(P,\lambda,\gamma;\xi)\prec O.
\]

\(\gamma\) 的搜索流程：

```text
γ = 0.25 → γ = γ × 1.8 → bisec(γ)
```

## RNN Controller

RNN Controller 与 Two-Stage 的可行条件如下：

| 情况 | RNN Controller \(D_4=0\) | RNN Controller \(D_4\ne0\) | Two-Stage \(D_4=0\) | Two-Stage \(D_4\ne0\) |
| --- | --- | --- | --- | --- |
| \(N_{\mathrm{DOF}}=N_{\mathrm{control}}\) | √ | × | √ | √ |
| \(N_{\mathrm{DOF}}>N_{\mathrm{control}}\) | √ | × | √ | × |

Inverted Pendulum 满足 \(D_4=0\)。Cart-Pole 中：

\[
D_4=\frac{1}{2}D_{\psi_1}D_{G_2}D_{K_1}\ne0.
\]

## 稳定半径与覆盖率

WaterLily 场景的稳定半径与概率：

| 场景 | \(r_{\min}\) | \(P(r>0.01)\) | 概率 |
| --- | --- | --- | --- |
| WaterLily | 0.0067 | 99.995% | 0.2865 |

覆盖率：

| \(r_{\min}\) | 90% Cov |
| --- | --- |
| 0.01 | \(1.18\times10^6\) |
| 0.05 | \(3.94\times10^4\) |

</article>

<aside class="course-index" aria-label="白玉楼索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>论文</p><a href="neurips2026.html">NeurIPS2026（Poster）</a></div>
  <div class="course-index__group"><p>组会</p><a href="lyge.html">LYGE</a><a href="neural-observer-i.html">Neural Observer I</a><a href="feedback-linearization.html">反馈线性化</a><a href="sima.html">SIMA</a><a href="neural-observer-ii.html">Neural Observer II</a><a href="neural-observer-iii.html">Neural Observer III</a><a href="dream2flow.html">Dream2Flow</a><a class="is-active" href="neural-observer-iv.html">Neural Observer IV</a></div>
</aside>
</div>
