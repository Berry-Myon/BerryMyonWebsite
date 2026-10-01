---
forced_scheme: slate
forced_primary: black
forced_accent: amber
hide:
  - toc
---

# SRTP

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 项目 | SRTP |
| 结果 | 校级良好 |
| 顺位 | 3/3 |
| 主题 | 模拟三维眼动的视觉系统设计 |

我们模仿眼球运动机理，设计三自由度并联机器人的硬件结构，建立运动学模型并实现视觉跟踪算法。项目英文题目为 *Reproduction of 3D Eye Movement System Based on Parallel Kinematics and Visual Tracking Algorithm*。

## 系统目标

我们建立了 Stewart 机器人的逆运动学模型，在 OpenMV 平台上设计基于模板匹配的物体识别与定位算法，构建视觉伺服系统。系统可以对指定物体进行实时跟踪和位置定位，并根据物体位置控制摄像头位姿，使目标尽量保持在摄像头视野中心。

## 硬件与运动学

硬件部分先用 Inventor 对并联结构仿真，再通过 3D 打印验证结构。后期实物使用亚克力板、6 个带万向球头的连接杆和 6 个 \(180^\circ\) 舵机搭建 Stewart 平台。

我们针对 6-SPS 并联机构做逆运动学解算，并在 MATLAB 中可视化。姿态旋转关系如下：

\[
v_i=Rv_i'=R_z(\phi)R_y(\theta)R_x(\psi)v_i'
\]

6-SPS 解算中，杆长向量写为：

\[
l=T+{}^PR_B\cdot p_i-b_i
\]

继续写为：

\[
L=l^2-(s^2-a^2)
\]

\[
M=2a(z_p-z_b)
\]

\[
N=2a[\cos\beta(x_p-x_b)+\sin\beta(y_p-y_b)]
\]

舵机角度为：

\[
\alpha=\arcsin\left(\frac{L}{\sqrt{M^2+N^2}}\right)-\arctan\left(\frac{N}{M}\right)
\]

## 视觉识别

前期在笔记本电脑上运行模型，使用 COCO 数据集上训练的 YOLOv5 做检测，使用 StrongSORT 做跟踪。后期搭建嵌入式视觉系统时，我们改用 OpenMV4 H7，完成对特征明显的彩色物体、光源、人脸等目标的识别。

联调时，OpenMV4 H7 固定在并联机构动平台上，作为上位机检测目标位置。STM32F407 接收上位机信号，输出 PWM 控制舵机，使并联机构带着摄像头转向目标。

## STM32 舵机控制

实物调试中的 PWM 启动与舵机占空比设置如下：

```c
HAL_TIM_PWM_Start(&htim11, TIM_CHANNEL_1);
HAL_TIM_PWM_Start(&htim10, TIM_CHANNEL_1);
HAL_TIM_PWM_Start(&htim2, TIM_CHANNEL_1);
HAL_TIM_PWM_Start(&htim13, TIM_CHANNEL_1);
HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);
HAL_TIM_PWM_Start(&htim14, TIM_CHANNEL_1);

to_initial();
while (1)
{
    disable_useless();
    __HAL_TIM_SetCompare(&htim2, TIM_CHANNEL_1, 1000);
    __HAL_TIM_SetCompare(&htim13, TIM_CHANNEL_1, 1500);
    __HAL_TIM_SetCompare(&htim3, TIM_CHANNEL_1, 2500);
    HAL_Delay(2000);

    __HAL_TIM_SetCompare(&htim2, TIM_CHANNEL_1, 2500);
    __HAL_TIM_SetCompare(&htim13, TIM_CHANNEL_1, 1000);
    __HAL_TIM_SetCompare(&htim3, TIM_CHANNEL_1, 1500);
    HAL_Delay(2000);
}
```

辅助函数负责关闭未使用通道与回到初始位置：

```c
void disable_useless(void)
{
    __HAL_TIM_SetCompare(&htim11, TIM_CHANNEL_1, 0);
    __HAL_TIM_SetCompare(&htim10, TIM_CHANNEL_1, 0);
    __HAL_TIM_SetCompare(&htim14, TIM_CHANNEL_1, 0);
    HAL_Delay(1);
}

void to_initial(void)
{
    __HAL_TIM_SetCompare(&htim2, TIM_CHANNEL_1, 2500);
    __HAL_TIM_SetCompare(&htim3, TIM_CHANNEL_1, 2500);
    __HAL_TIM_SetCompare(&htim13, TIM_CHANNEL_1, 2500);
    HAL_Delay(1000);
}
```

## 调试问题与限制

- Inventor 建模时出现过过配合，最后通过单独打印零件验证结构可行性。
- OpenMV 受芯片算力和图像分辨率限制，不能运行复杂算法。光线变化会带来目标识别误差，并影响运动学控制。
- 实物中主要使用三个舵机控制，没有完全利用六个舵机。
- 没有为 Stewart 平台单独设计控制器，平台快速运动能力和跟踪实时性没有完全发挥出来。

</article>

<aside class="course-index" aria-label="竹林隐居索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>竞赛</p><a href="zhongkong.html">“中控杯”机器人竞赛</a><a href="cumcm.html">大学生数模</a><a href="mcm.html">美赛</a><a href="npmcm.html">研究生数模</a></div>
  <div class="course-index__group"><p>项目</p><a class="is-active" href="srtp.html">SRTP</a></div>
  <div class="course-index__group"><p>实践</p><a href="alumni.html">回访母校</a><a href="social-practice.html">社会实践</a></div>
</aside>
</div>
