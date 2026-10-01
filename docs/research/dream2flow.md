---
hide:
  - toc
---

# Dream2Flow

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 组会题目 | Dream2Flow: Bridging Video Generation and Open-World Manipulation with 3D Object Flow |
| 时间 | 2026-06-17 |
| 关键词 | VLA, video generation, 3D object flow, manipulation |

## Background

传统 Vision-Language-Action 模型写作：

```text
Vision + Language → Action
```

在机器人任务中，动作与物体运动联系紧密。Dream2Flow 把任务改写成：

```text
Vision + Language + Object Motion → Robot Action
```

视频生成模型根据环境和指令生成物体运动，再由 3D Object Flow 转换成机器人动作。

## From Video to 3D Object Flow

第一步是深度估计。使用 SpatialTrackerV2 估计每一帧的深度：

\[
\{Z_t\}_{t=1}^{T}\in\mathbb{R}^{H\times W}.
\]

用真实机器人观测中的初始深度 \(D_0\) 对视频第一帧做校准，得到全局尺度和偏移：

\[
(s^\*,b^\*),
\]

再校准其他帧：

\[
Z_t=s^\*Z_t+b^\*.
\]

第二步是定位物体并采样。系统使用 Grounding DINO 根据初始图像和语言指令得到 bounding box，再用 SAM 2 得到二值 mask。若像素 \((u,v)\) 属于目标物体，则：

\[
M(u,v)=1.
\]

在视频第一帧从 mask 中采样点：

\[
c_i^1=(u_i^1,v_i^1),\qquad i=1,\ldots,N.
\]

CoTracker3 在整段生成视频中跟踪：

\[
\{c_i^t\}_{i=1:N,t=1:T},
\]

并输出可见性：

\[
\{\mathcal{V}_i^t\}_{i=1:N,t=1:T}.
\]

平均每帧移动超过阈值的点会被判断为 movable part。

## 2D 到 3D

根据相机内参：

\[
K=
\begin{bmatrix}
f_x&0&c_x\\
0&f_y&c_y\\
0&0&1
\end{bmatrix},
\]

相机坐标系下的 3D 点为：

\[
X_i^t=\frac{(u_i^t-c_x)z_i^t}{f_x},\qquad
Y_i^t=\frac{(v_i^t-c_y)z_i^t}{f_y},\qquad
Z_i^t=z_i^t.
\]

最后根据相机外参得到机器人坐标系中的 3D 点：

\[
P_{1:T}\in\mathbb{R}^{T\times N\times 3}.
\]

## Video Generation Model

汇报中记录了 Wan 2.1 和 Veo 3 两类视频生成模型，以及几类任务：

| 任务 | 记录 |
| --- | --- |
| Push-T | 在 OmniGibson 中推动 T 形积木到木板中心，位置误差小于 2 cm，方向误差小于 15°。 |
| Put Bread in Bowl | 在真实场景中把面包放进碗里。 |
| Open Oven | 在真实场景中打开烤箱门，大于 60°。 |
| Cover Bowl | 用桌面上的折叠围巾盖住碗，覆盖顶部面积大于 25%。 |
| Open Door | 在 Robosuite 中转动门把手并拉开门，大于 17°。 |

## Control Policy

状态空间写成：

\[
x_t=(x_t^{\mathrm{obj}},r_t),
\]

其中 \(x_t^{\mathrm{obj}}\) 为物体点位置，\(r_t\) 为机器人状态。动力学模型为：

\[
x_{t+1}=f(x_t,u_t).
\]

优化目标为：

\[
\min_{\{u_t\in\mathcal{U}\}_{t=0}^{H-1}}
\sum_{t=0}^{H-1}
\lambda_{\mathrm{task}}(x_t^{\mathrm{obj}},P_t)
+\lambda_{\mathrm{control}}(x_t,u_t)
\]

\[
\mathrm{s.t.}\quad x_{t+1}=f(x_t,u_t),\qquad x_0=x_0.
\]

任务项为：

\[
\lambda_{\mathrm{task}}(x_t^{\mathrm{obj}},P_t)
=\sum_{i=1}^{n}\|x_t^{\mathrm{obj}}[i]-P_t[i]\|_2^2.
\]

Push 动作记作：

\[
u=(p_x,p_y,\Delta p_x,\Delta p_y,l),
\]

其中 \(p_x,p_y\) 为推动起点，\(\Delta p_x,\Delta p_y\) 为单位推动方向，\(l\) 为推动距离。Random Shooting 随机采样 \(r\) 个 push skill 参数，用粒子动力学模型预测点云，选择 cost 最小的动作执行。

抓取类任务中，AnyGrasp 生成候选位置，HaMeR 检测人手，选择最近位置。刚体动力学假设机器人抓住的物体部分为刚性，该部分点随末端执行器做同一个刚体变换，未抓住的点保持不动。末端轨迹经 B 样条插值优化后，使用 PyBullet IK 求目标关节角。

## Conclusion

汇报最后记录了三点：

- 将物体运动和机器人动作解耦，不依赖具体机器人形态。
- 3D Object Flow 能更灵活地描述物体运动。
- 可与粒子动力学、轨迹优化、强化学习等多种控制方式结合。

我认为限制也很直接：系统依赖上游 Video Generation Model 的质量，视频生成成本高，串联结构会让误差逐级传递。

</article>

<aside class="course-index" aria-label="白玉楼索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>论文</p><a href="neurips2026.html">NeurIPS2026（Poster）</a></div>
  <div class="course-index__group"><p>组会</p><a href="lyge.html">LYGE</a><a href="neural-observer-i.html">Neural Observer I</a><a href="feedback-linearization.html">反馈线性化</a><a href="sima.html">SIMA</a><a href="neural-observer-ii.html">Neural Observer II</a><a href="neural-observer-iii.html">Neural Observer III</a><a class="is-active" href="dream2flow.html">Dream2Flow</a><a href="neural-observer-iv.html">Neural Observer IV</a></div>
</aside>
</div>
