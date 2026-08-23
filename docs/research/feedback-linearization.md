---
forced_scheme: default
forced_primary: white
forced_accent: green
hide:
  - toc
---

# 反馈线性化

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 组会题目 | Feedback Linearization Through the Lens of Data |
| 时间 | 2025-12-17 |
| 关键词 | feedback linearization, data-driven control, null space, inverted pendulum |

## 问题设定

对于非线性系统：

\[
\dot{x}=f(x)+g(x)u,
\]

反馈线性化希望找到变换：

\[
u=\alpha(x)+\beta(x)v,\qquad z=T(x),
\]

使系统变为：

\[
\dot{z}=Az+Bv.
\]

在 \(f(x)\) 和 \(g(x)\) 未知时，我在汇报中采用数据集：

\[
\mathcal{D}=\{(x_i,u_i,\dot{x}_i)\}
\]

去求满足线性化条件的 \(\tau(x)\)、\(\delta(x)\)、\(\gamma(x)\)。对应条件写成：

\[
\frac{\partial \tau}{\partial x}\left(f(x)+g(x)u\right)
=A_C\tau(x)+B_C\left(\delta(x)+\gamma(x)u\right).
\]

## 基函数与零空间

核心假设是未知解存在于已知基函数张成的空间中。把问题化为线性方程后，需要求：

\[
F(\mathcal{D})v=0.
\]

我在讨论中按零空间维数分成三种情况：

| 情况 | 记录 |
| --- | --- |
| Null Space = 1 | 对 SISO 系统，任意 \(v\in\ker(F(\mathcal{D}))\) 可给出候选解。 |
| Null Space = 0 | 基函数选择不恰当，或数据测量误差导致无非平凡解。 |
| Null Space > 1 | MIMO 系统需要在所有基底线性组合中，寻找使最终 \(T\) 和 \(M\) 满足雅可比矩阵可逆的组合。 |

噪声数据下，我记录了三条处理：

- 惩罚复杂度，减少过拟合
- 每次更新后将 \(v\) 归一化，避免平凡解
- 用 \(L_1\) 范数代替 \(L_0\) 范数

## 倒立摆实验

我选取倒立摆实验，参数为：

| 参数 | 数值 |
| --- | --- |
| \(m\) | 1 |
| \(l\) | 1 |
| \(x_1\) | \(\theta\) |
| \(x_2\) | \(\dot{\theta}\) |
| \(g\) | 10 |
| 摩擦系数 | \(\zeta_0=0.50\)，与角速度成正比 |

仿真动力学写成：

\[
\dot{x}_1=x_2,\qquad
\dot{x}_2=10\sin(x_1)-0.5x_2+u+\varepsilon.
\]

控制器采用 PID：

\[
u=K_pe+K_i\int e\,dt+K_d\dot{e}.
\]

实验中使用的参数为 \(K_p=850.0\)、\(K_i=0.0\)、\(K_d=5.0\)，仿真步长 \(dt=0.001\) s，总时长 1 s。

## 训练实现

训练中先构造 \(F(\mathcal{D})\) 的行，再把 \(v\) 作为可训练参数。损失函数由残差和稀疏惩罚组成：

```python
v_norm = self.v / torch.norm(self.v)
Fmat = F_torch(x1, x2, u, dx1, dx2)
loss = torch.norm(Fmat @ v_norm) ** 2 + alpha * torch.sum(torch.abs(v_norm))
```

训练参数为：

| 项目 | 数值 |
| --- | --- |
| 优化器 | Adam |
| 学习率 | \(10^{-2}\) |
| epoch | 5000 |
| 稀疏惩罚 \(\alpha\) | 0.1 |
| 数据划分 | 训练集 60%，验证集 30%，测试集 10% |

训练后得到的 \(v\) 向量前几项为：

\[
v=(0.2936,\ 0.1914,\ 0.1722,\ 0.2392,\ 0.2548,\ 0.2246,\ldots).
\]

## 数据多样性

我对不同数据集构造 \(F(\mathcal{D})\)，从大到小绘制奇异值曲线，并记录条件数。条件数按最大奇异值与最小非零奇异值的比值计算：

\[
\kappa(F)=\frac{\sigma_{\max}(F)}{\sigma_{\min}^{+}(F)}.
\]

| 数据类型 | 条件数 |
| --- | --- |
| 平衡点附近 | \(1.0\times10^{22}\) |
| 单边数据 | \(1.3\times10^8\) |
| 双边数据 | \(1.2\times10^8\) |
| 振荡数据 | \(3.8\times10^{13}\) |

我在讨论中认为，\(F(\mathcal{D})\) 的奇异值能反映数据多样性。条件数越大，矩阵越病态，求解线性方程和后续优化都会更敏感。

</article>

<aside class="course-index" aria-label="白玉楼索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>论文</p><a href="neurips2026.html">NeurIPS2026（在投）</a></div>
  <div class="course-index__group"><p>组会</p><a href="lyge.html">LYGE</a><a href="neural-observer-i.html">Neural Observer I</a><a class="is-active" href="feedback-linearization.html">反馈线性化</a><a href="sima.html">SIMA</a><a href="neural-observer-ii.html">Neural Observer II</a><a href="neural-observer-iii.html">Neural Observer III</a><a href="dream2flow.html">Dream2Flow</a><a href="neural-observer-iv.html">Neural Observer IV</a></div>
</aside>
</div>
