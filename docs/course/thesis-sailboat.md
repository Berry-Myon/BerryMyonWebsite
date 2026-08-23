---
forced_scheme: default
forced_primary: white
forced_accent: pink
hide:
  - toc
---

# 本科毕业设计

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 题目

基于强化学习的无人帆船运动控制。

## 摘要整理

无人帆船是一种高效且环保的海上智能移动机器人，被广泛应用于海洋科学研究、资源开发等领域。在毕业设计中，我结合强化学习理论，设计并改进 PPO 算法用于无人帆船的运动控制，实现无人帆船的自主运动。

我把研究内容收成三部分：

1. 分析无人帆船和强化学习研究现状，建立无人帆船的简化机械模型、运动学模型和动力学模型，使用 Lyapunov 稳定性判别法第二法验证模型稳定性。
2. 使用反馈控制法设计控制率，构建并测试仿真环境。对控制率进行优化设计，并从能量角度评估改进前后的方法。
3. 介绍 A2C、PPO、SAC 三种强化学习算法，设计状态空间、动作空间和奖励函数，通过数值仿真实验评估和对比三个模型。

关键词：无人帆船、强化学习、反馈控制法、PPO 算法。

## 无人帆船模型

我建立的机械模型包括船体、船帆及桅杆、龙骨和船舵四部分。船体采用 V 字形窄船体的单体船结构，用于降低水阻并提升运动效率与稳定性。

模型假设：

- 忽略浮沉和俯仰运动，研究平面平动、翻滚与偏航运动，简化成 4 自由度模型。
- 忽略船帆、龙骨和船舵形状变化，将其视为流体中理想水翼结构。
- 假设流体速度和方向在同一水翼结构上保持一致。

我用 Lyapunov 第二法分析模型稳定性。构造得到的 Lyapunov 函数正定，一次导数负定，并且系统在 \(\|(u,v,p)\|\to\infty\) 时满足 \(V\to\infty\)，因此模型大范围渐近稳定。

## 反馈控制法

我的反馈控制率写为：

\[
\begin{cases}
v=k_\rho\rho+k_\beta\beta\\
\omega=k_\alpha\Delta\theta
\end{cases}
\]

控制量含义：

- \(v\)：平动速度。
- \(\omega\)：角速度。
- \(\rho\)：无人帆船到目标点的距离。
- \(\beta\)：航向与目标方向的夹角。
- \(\Delta\theta\)：当前朝向偏差。

优化反馈控制法考虑高速转向时的稳定性。我从能量角度对两种反馈控制方法做评估，帆船长度取 1.5m，宽度取 0.8m。结果是：优化反馈控制法需要更长行进距离，能量消耗比传统反馈控制法增加约 30%，但能够避免高速行进时快速转向。

## 强化学习控制

我使用 A2C、PPO 和 SAC 三种方法。

| 方法 | 分类 | 动作空间 |
| --- | --- | --- |
| A2C | 基于值和策略 | 离散或连续 |
| SAC | 基于值和策略 | 连续 |
| PPO | 基于策略 | 离散或连续 |

状态空间和动作空间：

\[
\begin{cases}
s=[\Delta x,\Delta y]^T\\
a=[v,\omega]^T
\end{cases}
\]

奖励函数由三部分组成：

\[
R=R_p+R_v+R_0
\]

- \(R_p\)：距离奖励。
- \(R_v\)：速度和角速度奖励。
- \(R_0\)：每步固定奖励。

速度奖励部分记录为：

\[
R_v=K_aa^2+K_bb^2+K_vv^2+K_\omega\omega^2
\]

## 仿真结果

实验设置初始位置为 \((70,15)\)，目标位置为 \((45,25)\)，最大迭代更新次数为 1000。

三种模型平均运行时长：

| 模型 | 平均运行时长 |
| --- | ---: |
| A2C | 1.90 s |
| PPO | 2.72 s |
| SAC | 16.21 s |

最后我的结果记录为：

- 三种算法成功率相近，SAC 略高。
- SAC 训练时间明显长于 A2C 和 PPO。
- A2C 的能量损失明显高于另外两种算法。
- PPO 的能量损失最少，约为 A2C 的三分之二。
- 综合稳定性、运行效率和性能，PPO 表现最佳。

</article>

<aside class="course-index" aria-label="寺子屋索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>基础数理</p><a href="numerical-methods.html">计算方法</a><a href="oop.html">OOP</a><a href="embedded-systems.html">嵌入式系统</a></div>
  <div class="course-index__group"><p>外语</p><a href="english-lexicology.html">英语词汇学</a><a href="japanese.html">日语</a></div>
  <div class="course-index__group"><p>专业课及其实践</p><a href="robotic-arm.html">机器人学：机械臂</a><a href="mobile-robot.html">机器人学：移动机器人</a><a href="pallet-robot.html">托盘机器人</a><a href="aerial-robot.html">空中机器人</a><a href="cv.html">CV</a><a href="nlp.html">NLP</a><a href="optimization-control.html">最优化控制</a><a class="is-active" href="thesis-sailboat.html">本科毕业设计</a></div>
  <div class="course-index__group"><p>团队竞赛</p><a href="team.html">团队竞赛</a></div>
</aside>
</div>
