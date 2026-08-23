---
hide:
  - toc
---

# SIMA

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 组会题目 | A generalist AI agent for 3D virtual environments |
| 时间 | 2025-12-19 |
| 关键词 | SIMA, 3D virtual environments, behavioral cloning, CFG |

## Background

汇报以 Moravec's Paradox 开头：高级推理对 AI 较容易，感知和运动对 AI 较难。SIMA 的目标对应“抽象化、通用化、grounding”三件事。

SIMA 的核心目标写作：

> 构建一个智能体，它能听懂任意自然语言指令，在任何虚拟 3D 环境中完成任何人类能做的事情。

智能体约束为：

- 使用键盘和鼠标操作
- 使用开放式自然语言
- 只能看到屏幕信息，不能访问游戏内部特权信息
- 在复杂 video games 环境中实时运行
- 专注遵循语言指令

## Environments

SIMA 团队使用 Commercial Video Games 和 Research Environments 的组合。商业游戏提供开放世界、视觉丰富度和复杂交互，研究环境提供可控与可靠评估。

汇报中记录的两个例子：

| 环境 | 记录 |
| --- | --- |
| Goat Simulator 3 | 第三人称游戏，玩家可以舔、撞、攀爬、驾驶、装备多种视觉与功能物体。 |
| Construction Lab | 研究环境，智能体需要用可连接积木搭建物体、斜坡、桥和动态结构。 |

## Data

SIMA 使用人类专家的游戏数据，通过 Behavioral Cloning 进行大规模训练。

数据采集分两类：

- Single-Player：Free Play + Annotate
- Two-Player Setter-Solver：一名玩家给出指令，另一名玩家在同一视角下完成任务

数据预处理包括：

- Resize
- Filter
- Remixing and Weighting

## Agent

SIMA 构建了从视觉观察和语言指令到键盘、鼠标动作的模型。视觉部分使用预训练模型提取通用特征，再针对环境微调。

| 模型 | 记录 |
| --- | --- |
| SPARC | 用于细粒度图像-文本对齐，经过 Behavioral Cloning 微调。 |
| Phenaki | 视频预测模型，通过视频预测任务微调。 |
| Transformer-XL | 处理过去记忆状态，构建状态表示。 |

最终状态表示输入策略网络。训练目标包括 Behavioral Cloning 和 Predicting goal completion。网络输出一个包含 8 个动作的短动作序列。

推理阶段使用 Classifier-Free Guidance 改善 language-conditionality：

\[
\pi_{\mathrm{CFG}}
=\pi(\mathrm{image},\mathrm{language})
+\lambda\left(
\pi(\mathrm{image},\mathrm{language})
-\pi(\mathrm{image},\varnothing)
\right).
\]

## Evaluation

评估方式分三类：

| 方法 | 记录 |
| --- | --- |
| Ground-Truth | 适用于 research environments，可通过程序判断任务是否完成，如 “lift the green cube”。 |
| OCR | 通过识别屏幕文字判断智能体是否成功。 |
| Human Evaluation | 熟悉游戏的人类裁判观看录像，判断是否完成指令。 |

Initial results 中比较了四种设置：

| 设置 | 记录 |
| --- | --- |
| Environment-Specialized | 只在某个特定环境训练，作为性能评估基准。 |
| Zero-Shot | 在 \(N-1\) 个环境训练，直接在未见过的第 \(N\) 个环境测试。 |
| No Pretraining | 去掉 SPARC 和 Phenaki 的预训练，改用从头训练的 ResNet。 |
| No Language | 去掉语言指令输入。 |

汇报中还记录了人工评估分歧：含糊任务里，有些失败来自智能体在完成任务前执行了额外行为，例如在 “recharge the mining beam” 指令下先打开 starship menu，或在 “mine oxygen” 指令下扫描后进入 analysis mode。

## SIMA 2

最后的参考资料转向 SIMA 2。我的记录写下两条线索：SIMA 1 的 generalist 3D agent，以及 SIMA 2 中“plays, reasons and learns with you”的进一步版本。

</article>

<aside class="course-index" aria-label="白玉楼索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>论文</p><a href="neurips2026.html">NeurIPS2026（在投）</a></div>
  <div class="course-index__group"><p>组会</p><a href="lyge.html">LYGE</a><a href="neural-observer-i.html">Neural Observer I</a><a href="feedback-linearization.html">反馈线性化</a><a class="is-active" href="sima.html">SIMA</a><a href="neural-observer-ii.html">Neural Observer II</a><a href="neural-observer-iii.html">Neural Observer III</a><a href="dream2flow.html">Dream2Flow</a><a href="neural-observer-iv.html">Neural Observer IV</a></div>
</aside>
</div>
