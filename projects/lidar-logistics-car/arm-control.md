# Arm Control

## 目标

记录智能仓储搬运机器人机械臂的结构自由度、坐标解算、STM32C8T6 下位机控制流程和抓取调试结果。

## Mechanism Overview

机械臂安装在移动底盘上，用于对桌面或平台上的目标物体进行抓取。当前方案包含三个主要运动方向：

| 自由度 | 作用 |
| --- | --- |
| Yaw | 控制机械臂水平转向 |
| Z | 控制末端上下升降 |
| Y | 控制末端水平伸缩 |

已记录的结构范围：

| 参数 | 范围 |
| --- | --- |
| Z 轴行程 | 0-200 mm |
| 水平半径 / 伸缩范围 | 0-180 mm |
| 控制方式 | STEP / DIR + PWM |

## Control Architecture

```text
upper computer
    -> target coordinate / single-axis command
    -> USART2
    -> STM32C8T6
    -> coordinate calculation
    -> travel limit check
    -> STEP / DIR pulse + servo PWM
    -> Y extension + Z lift + Yaw rotation
```

## Coordinate Calculation

上位机根据识别结果或人工设定给出目标点坐标 `P(X, Y, Z)`。机械臂下位机将目标点转换为半径、角度和高度控制量。

```text
r = sqrt(X^2 + Y^2)
theta = atan2(Y, X)
L = r - L0
H = Z
```

其中：

- `r`: 目标点到机械臂旋转中心的水平距离。
- `theta`: Yaw 轴目标转角。
- `L`: 水平伸缩量，需要扣除机构初始偏置 `L0`。
- `H`: Z 轴高度控制量。

## STM32C8T6 Interface

PPT 中记录的接口如下：

| 功能 | 引脚 / 接口 | 说明 |
| --- | --- | --- |
| USART2 | PA2 / PA3 | 接收上位机目标坐标或单轴控制指令 |
| 舵机 PWM | PA6 / PA7 | 输出舵机控制信号 |
| 步进电机 1 | PB0 / PB1 | STEP / DIR 控制 |
| 步进电机 2 | PB10 / PB11 | STEP / DIR 控制 |

## Control Logic

1. 上位机发送目标坐标或单轴调试指令。
2. STM32C8T6 接收并解析数据。
3. 根据 `X/Y/Z` 坐标计算 `r`、`theta`、`L`、`H`。
4. 检查 Z 轴行程和水平伸缩范围，避免超出机械极限。
5. 输出 STEP / DIR 脉冲控制步进机构。
6. 输出 PWM 控制舵机动作。
7. 机械臂完成 Y 轴伸缩、Z 轴升降和 Yaw 转向。

## Debugging and Result

已完成的机械臂调试结果：

- 单轴运动调试：分别验证 Y 轴伸缩、Z 轴升降和 Yaw 转向。
- 传动测试：验证步进 / 舵机驱动输出与机构动作是否一致。
- 抓取验证：机械臂能够抓取桌面目标物体。
- 实物联调：机械臂与底盘和上位机系统共同安装在机器人平台上。

## Problems to Record Later

后续可以继续补充：

- 机械臂末端执行器尺寸和抓取范围。
- 步进电机细分、脉冲频率、运动速度和加减速策略。
- 坐标系定义图，包括相机坐标、机器人坐标和机械臂坐标。
- 手眼标定后目标坐标到机械臂坐标的转换过程。
- 抓取失败案例，例如偏位、夹持不稳、目标遮挡或机械限位。

