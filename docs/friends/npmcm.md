---
forced_scheme: slate
forced_primary: black
forced_accent: amber
hide:
  - toc
---

# 研究生数学建模竞赛

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 竞赛 | 研究生数学建模竞赛 |
| 结果 | 国家级二等奖 |
| 顺位 | 1/3 |
| 题目 | X 射线脉冲星光子到达时间建模 |

这次题目围绕 XPNAV-1 卫星、Crab 脉冲星、光子到达时间与空间坐标系转换，记录模型、公式、关键数值和仿真代码。

## 轨道位置建模

先按椭圆轨道关系写卫星在轨道平面内的位置。若半长轴为 \(a\)，偏心率为 \(e\)，真近点角为 \(\theta\)，则：

\[
\rho=\frac{a(1-e^2)}{1+e\cos\theta}
\]

轨道平面到 GCRS 的转换采用 \(ZXZ\) 欧拉旋转：

\[
R=R_z(-\Omega)R_x(-i)R_z(-\omega)
\]

卫星在轨道平面中的位置写为：

\[
{}^{Nav}P_{Sate}=
\begin{bmatrix}
\rho\cos\theta\\
\rho\sin\theta\\
0
\end{bmatrix}
\]

再由旋转矩阵得到 GCRS 下的位置。

## Roemer 延迟

脉冲星方向向量由赤经 \(\alpha\) 和赤纬 \(\delta\) 得到：

\[
n=
\begin{bmatrix}
\cos\delta\cos\alpha\\
\cos\delta\sin\alpha\\
\sin\delta
\end{bmatrix}
\]

Roemer 延迟按投影距离除以光速计算：

\[
t_{\mathrm{Roemer}}=\frac{n\cdot{}^{Solar}P_{Sate}}{c_0}
\]

报告中前一组计算得到：

\[
t_{\mathrm{Roemer}}=277.92485917502177s
\]

后续更新 Crab 脉冲星方向与卫星位置后，方向向量记录为：

\[
n=
\begin{bmatrix}
0.10280982\\
0.92137086\\
0.37484113
\end{bmatrix}
\]

并得到：

\[
t_{\mathrm{Roemer}}=473.8982778489122s
\]

## Shapiro 修正

Shapiro 延迟使用太阳系引力场修正。近似式之一为：

\[
t_{\mathrm{Shapiro1}}
=
\frac{4GM_S}{c_0^3}
\left\{
\ln\left(\frac{4x_ex_p}{d^2}\right)
-\frac{3x_e+x_p}{2x_e}
\right\},
\quad d\ll x_e,x_p
\]

代入报告中的几何量：

| 量 | 数值 |
| --- | ---: |
| \(\phi_1\) | \(0.1818190985345287rad\) |
| \(x_e\) | \(147055775.76015148km\) |
| \(x_p\) | \(940626.9444033394km\) |
| \(d\) | \(172933.78174977514km\) |

得到：

\[
t_{\mathrm{Shapiro1}}\approx0.00016401613841124908s
\]

最终时间修正写为：

\[
\Delta t=t_{\mathrm{Roemer}}+t_{\mathrm{Shapiro}}+t_{\mathrm{GR}}+t_{\mathrm{SR}}
=473.898447102698s
\]

报告中对 Shapiro 近似式的相对误差记录为 \(0.199\%\)。

## 光子到达时间仿真

仿真部分使用非齐次泊松过程。相位函数按脉冲星频率及其导数计算：

\[
\phi(t)=\left(v(t-t_0)+\frac{1}{2}\dot{v}(t-t_0)^2+\frac{1}{6}\ddot{v}(t-t_0)^3\right)\bmod 1
\]

代码中使用的参数为：

| 参数 | 数值 |
| --- | ---: |
| \(\lambda_b\) | 1.54 |
| \(\lambda_s\) | 15.4 |
| \(S\) | \(250cm^2\) |
| \(T_{obs}\) | \(10s\) |
| \(v\) | \(29.647854750036593s^{-1}\) |
| \(\dot{v}\) | \(-368970.96\times10^{-15}s^{-2}\) |

到达率函数：

\[
\mu(t)=\lambda_bS+\lambda_sS h(\phi(t))
\]

仿真代码使用指数间隔采样：

```python
def phase_function(t):
    delta_t = t - t0
    phase = (
        v * delta_t
        + 0.5 * v_dot * delta_t ** 2
        + (1 / 6) * v_ddot * delta_t ** 3
    )
    return phase % 1


def mu_t(t):
    phi_t = phase_function(t)
    h_phi = np.interp(phi_t, phases, values)
    return lambda_b * S + lambda_s * S * h_phi


def simulation(T_obs):
    t = 0
    results = []
    while t < T_obs:
        u = np.random.uniform(0, 1)
        rate = mu_t(t)
        delta_t = -np.log(u) / rate
        t += delta_t
        if t < T_obs:
            results.append(t)
    return np.array(results)
```

仿真输出的到达时间从 \(0.00013434228142051132s\) 开始，末尾到 \(9.999783647058715s\)。

## 定位误差记录

定位误差写为：

\[
\delta_c=\lVert p-p_0\rVert
\]

报告中基准记录为：

\[
\delta_{c0}=3.4283
\]

改变观测时间 \(T_{obs}\) 后：

| \(T_{obs}\) | \(\delta_c\) |
| ---: | ---: |
| \(10s\) | 3.4283 |
| \(50s\) | 3.1467 |
| \(100s\) | 3.1594 |

固定 \(T_{obs}=10s\)，改变探测器面积 \(S\) 后：

| \(S\) | \(\delta_c\) |
| ---: | ---: |
| \(250cm^2\) | 3.4283 |
| \(300cm^2\) | 3.0432 |
| \(400cm^2\) | 2.8269 |

</article>

<aside class="course-index" aria-label="竹林隐居索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>竞赛</p><a href="zhongkong.html">“中控杯”机器人竞赛</a><a href="cumcm.html">大学生数模</a><a href="mcm.html">美赛</a><a class="is-active" href="npmcm.html">研究生数模</a></div>
  <div class="course-index__group"><p>项目</p><a href="srtp.html">SRTP</a></div>
  <div class="course-index__group"><p>实践</p><a href="alumni.html">回访母校</a><a href="social-practice.html">社会实践</a></div>
</aside>
</div>
