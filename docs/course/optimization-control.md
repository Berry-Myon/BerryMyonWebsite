---
forced_scheme: default
forced_primary: white
forced_accent: pink
hide:
  - toc
---

# 最优化控制

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 控制对象

我使用的线性系统如下：

```matlab
A = [0 0 0 1 0 0;
     0 0 0 0 1 0;
     0 0 0 0 0 1;
     0 0 0 0 0 0;
     0 190 -117 0 0 0;
     0 -91 85.5 0 0 0];
B = [0; 0; 0; 1; 7.4; -0.55];
C = [1 0 0 0 0 0;
     0 1 0 0 0 0;
     0 0 1 0 0 0];
D = [0; 0; 0];
```

## PID

我在 Simulink 中得到的 PID 调参结果为：

```ini
P = -0.2588
I = -0.001057
D = -14.09
```

MATLAB PID tuner 提示：`PID tuner could not find an initial stabilizing controller`。之后我改用 LQR 和 MPC。

## 非约束 LQR

非约束 LQR 的权重矩阵：

```matlab
Q = [100 0 0 0 0 0;
     0 10 0 0 0 0;
     0 0 1 0 0 0;
     0 0 0 100 0 0;
     0 0 0 0 10 0;
     0 0 0 0 0 1];
R = 1;
K = lqr(A,B,Q,R);
```

另一组 LQR 参数与反馈增益：

```matlab
Q = diag([100, 10, 1, 100, 10, 1])
R = 0.1
K = [31.62, 360.79, -606.49, 51.43, 2.42, -77.61]
```

## 带约束 LQR

带约束 LQR 先给出加速度约束，再循环寻找合适的 \(R\)：

```matlab
limit_acceleration = 0.15;
assert(limit_acceleration >= 0.1 && limit_acceleration <= 75);

R = 96;
while 1
    K = lqr(A,B,Q,R);
    A0 = (A - B * K);
    PHI0 = tf(ss(A0,B,C,D));
    x = step(PHI0, 10);
    x = x(:,1);
    dt = 1 / length(x(:));
    ddx_dt = diff(x,2);
    max_ddx = max(ddx_dt) / dt^2;
    if max_ddx <= limit_acceleration
        break
    end
    R = R / 2;
end
```

我测试过的约束值包括 \(75m/s^2\)、\(10m/s^2\)、\(0.15m/s^2\)，对应搜索得到的 \(R\) 有 24、0.0117、0.0000229。

## MPC

MPC 先以 LQR 反馈构造闭环系统，再交给 `mpcDesigner`：

```matlab
Q = [100 0 0 0 0 0;
     0 10 0 0 0 0;
     0 0 1 0 0 0;
     0 0 0 100 0 0;
     0 0 0 0 10 0;
     0 0 0 0 0 1];
R = 10;
K = lqr(A,B,Q,R);

A = A - B * K;
PHI = ss(A,B,C,D);
% mpcDesigner
```

我在 `mpcDesigner` 中设置采样时间 \(T_s=0.08s\)，并设置加速度约束 \(-20m/s^2 \leq u \leq 20m/s^2\)。

</article>

<aside class="course-index" aria-label="寺子屋索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>基础数理</p><a href="numerical-methods.html">计算方法</a><a href="oop.html">OOP</a><a href="embedded-systems.html">嵌入式系统</a></div>
  <div class="course-index__group"><p>外语</p><a href="english-lexicology.html">英语词汇学</a><a href="japanese.html">日语</a></div>
  <div class="course-index__group"><p>专业课及其实践</p><a href="robotic-arm.html">机器人学：机械臂</a><a href="mobile-robot.html">机器人学：移动机器人</a><a href="pallet-robot.html">托盘机器人</a><a href="aerial-robot.html">空中机器人</a><a href="cv.html">CV</a><a href="nlp.html">NLP</a><a class="is-active" href="optimization-control.html">最优化控制</a><a href="thesis-sailboat.html">本科毕业设计</a></div>
  <div class="course-index__group"><p>团队竞赛</p><a href="team.html">团队竞赛</a></div>
</aside>
</div>
