---
forced_scheme: default
forced_primary: white
forced_accent: pink
hide:
  - toc
---

# 英语词汇学

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 语料库研究

在这门课程中，我研究的主题是 *A Brief Research on the Synonyms: Insult and Offend, Take Usage in Fictions for Example*。

我使用 COCA 语料库，对 `insult` 和 `offend` 的搭配对象、语域分布和小说类型分布进行比较。

## 名词搭配

我先分别检索两个词的匹配串，再人工分析各 200 个句子。搭配对象统计如下：

| 词 | 单个人 | 群体 | 作名词使用 | 其他 | 合计 |
| --- | ---: | ---: | ---: | ---: | ---: |
| insult | 35 | 18 | 122 | 25 | 200 |
| offend | 44 | 91 | 0 | 65 | 200 |

我的结论是：`insult` 更常搭配单个人，`offend` 更常搭配群体。

## 语域分布

COCA 中的每百万词频：

| 词 | blog | spoken | fiction | newspaper | academic |
| --- | ---: | ---: | ---: | ---: | ---: |
| insult | 17.21 | 6.99 | 11.94 | 5.91 | 4.07 |
| offend | 6.88 | 4.09 | 3.93 | 2.90 | 2.13 |

我据此转向小说语境中的细分分布：

| 词 | SciFi/Fant | Movies | Fan Fiction |
| --- | ---: | ---: | ---: |
| insult | 13.69 | 6.87 | 15.60 |
| offend | 4.28 | 2.07 | 3.25 |

我的假设是：科幻或幻想小说常写人类与异族、文明、宗教之间的冲突，`offend` 用于更大的群体或文化对象。Fan fiction 常围绕原作主角展开，`insult` 更容易落在具体人物或人物相关的抽象对象上。

## 具体用法

`offend` 在科幻或幻想小说中分为两类：

- Offend one special person。对象可能是 `God`、`Captain` 或带有从句补充说明的人物。
- Offend religions。对象常与宗教、文化、文明冲突有关。

`insult` 在 fan fiction 中分为两类：

- Insult main character or someone related to main character。
- Insult something abstract。例子包括记忆、尊严、情感等抽象对象。

## 固定搭配与特殊情况

固定搭配 `add insult to injury` 中的 `injury` 会显著抬高它与 `insult` 在 COCA 搭配统计中的共现频率。

`Islam` 更常与 `insult` 搭配，`Muslims` 更常与 `offend` 搭配。这类例外受地理、政治、文化等因素影响。

## 结论

最后，我认为区分 `insult` 和 `offend` 的用法，需要结合搭配对象的范围和文本类型。

</article>

<aside class="course-index" aria-label="寺子屋索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>基础数理</p><a href="numerical-methods.html">计算方法</a><a href="oop.html">OOP</a><a href="embedded-systems.html">嵌入式系统</a></div>
  <div class="course-index__group"><p>外语</p><a class="is-active" href="english-lexicology.html">英语词汇学</a><a href="japanese.html">日语</a></div>
  <div class="course-index__group"><p>专业课及其实践</p><a href="robotic-arm.html">机器人学：机械臂</a><a href="mobile-robot.html">机器人学：移动机器人</a><a href="pallet-robot.html">托盘机器人</a><a href="aerial-robot.html">空中机器人</a><a href="cv.html">CV</a><a href="nlp.html">NLP</a><a href="optimization-control.html">最优化控制</a><a href="thesis-sailboat.html">本科毕业设计</a></div>
  <div class="course-index__group"><p>团队竞赛</p><a href="team.html">团队竞赛</a></div>
</aside>
</div>
