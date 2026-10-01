---
forced_scheme: default
forced_primary: white
forced_accent: pink
hide:
  - toc
---

# 计算方法

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 误差

绝对误差按“真实值减去近似值”记：

\[
e(x)=x^*-x
\]

绝对误差限为：

\[
|e(x)|=|x^*-x|
\]

相对误差写成：

\[
e_r(x)=\frac{x-x^*}{x^*}
\]

绝对误差按方向取值，误差限取绝对值。

## 不稳定递推的改写

对于积分

\[
I_n=\int_0^1 \frac{x^n}{x+8}dx
\]

可化为：

\[
I_n=\frac{1}{n}-8I_{n-1}
\]

该式从小 \(n\) 向大 \(n\) 递推时容易放大舍入误差。整理成反向递推后：

\[
I_{n-1}=\frac{1}{8n}-\frac{1}{8}I_n
\]

计算方向变为由高阶项向低阶项回推。

## 非线性方程求根

二分法误差估计：

\[
|x^*-x_k|\leq\frac{b-a}{2^{k+1}}\leq\varepsilon
\]

简单迭代法写作：

\[
x=g(x)
\]

存在性与收敛性条件：

- 对任意 \(x\in[a,b]\)，有 \(g(x)\in[a,b]\)。
- 存在 \(L\in[0,1)\)，使得 \(|g(x)-g(y)|\leq L|x-y|\)。
- \(L\) 可由 \(|g'(x)|\) 的上界替代。

牛顿迭代：

\[
x_{k+1}=x_k-\frac{f(x_k)}{f'(x_k)}
\]

收敛条件：

- \(x^*\) 附近有二阶连续导数。
- \(f'(x^*)\neq0\)。
- 若 \(x_0\in[a,b]\)，且 \(f(x_0)f''(x_0)>0\)，在端点异号、单调性和凹凸性保持时收敛到区间内唯一实根。

牛顿迭代法的引申：

| 方法 | 迭代形式 |
| --- | --- |
| 简化牛顿法 | 用 \(f'(x_0)\) 替代 \(f'(x_k)\) |
| 牛顿下山法 | \(x_{k+1}=x_k-\lambda\frac{f(x_k)}{f'(x_k)}\) |
| 弦割法 | 用 \(\frac{f(x_k)-f(x_{k-1})}{x_k-x_{k-1}}\) 替代导数 |
| 艾特肯算法 | \(y_k=g(x_k)\)，\(z_k=g(y_k)\)，\(x_{k+1}=x_k-\frac{(y_k-x_k)^2}{z_k-2y_k+x_k}\) |

常见收敛阶数：

- 简单迭代法，一阶收敛。
- 牛顿迭代法，单根时平方收敛，重根时一阶收敛。
- 一阶收敛序列经艾特肯加速后可达平方收敛。

## 线性代数方程组

高斯消元法把增广矩阵 \((A^{(k)},B^{(k)})\) 逐步化为上三角形式。列主元高斯消元在每次消元前，将当前列中绝对值最大的元素换到主元位置。

LU 分解采用杜利特尔分解时：

\[
A=LU,\quad Ly=b,\quad Ux=y
\]

追赶法用于对角占优三对角方程组。若主对角线元素绝对值大于等于同行或同列其余元素绝对值之和，称为对角占优。严格大于时为严格对角占优。

平方根法用于对称正定矩阵。Jacobi、Gauss-Seidel 和 SOR 迭代的收敛性可通过谱半径或矩阵条件判断。

## 插值、拟合与积分

插值包括 Lagrange 插值、Newton 插值和差商表。最小二乘拟合以误差平方和最小为目标。数值积分包括复化梯形、复化 Simpson、Gauss 型公式及其误差估计。

</article>

<aside class="course-index" aria-label="寺子屋索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>基础数理</p><a class="is-active" href="numerical-methods.html">计算方法</a><a href="oop.html">OOP</a><a href="embedded-systems.html">嵌入式系统</a></div>
  <div class="course-index__group"><p>外语</p><a href="english-lexicology.html">英语词汇学</a><a href="japanese.html">日语</a></div>
  <div class="course-index__group"><p>专业课及其实践</p><a href="robotic-arm.html">机器人学：机械臂</a><a href="mobile-robot.html">机器人学：移动机器人</a><a href="pallet-robot.html">托盘机器人</a><a href="aerial-robot.html">空中机器人</a><a href="cv.html">CV</a><a href="nlp.html">NLP</a><a href="optimization-control.html">最优化控制</a><a href="thesis-sailboat.html">本科毕业设计</a></div>
  <div class="course-index__group"><p>团队竞赛</p><a href="team.html">团队竞赛</a></div>
</aside>
</div>
