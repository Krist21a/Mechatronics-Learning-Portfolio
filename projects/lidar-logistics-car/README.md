# LiDAR Logistics Car 激光雷达物流小车

## Project Overview

本项目用于记录基于 LiDAR、嵌入式控制和电机驱动的自动寻迹 / 导航物流小车学习过程。当前重点是整理系统结构、硬件模块、软件模块、路径跟踪思路和调试记录。

项目仍在实践和文档完善中，后续会逐步补充实物图片、系统结构图、接线表、代码片段、测试数据和演示材料。

## Background

自动物流小车是移动机器人和自动化系统中的典型学习项目，涉及底盘结构、传感器感知、路径跟踪、运动控制、嵌入式软件和现场调试。

LiDAR 可以用于距离测量、障碍物检测、环境轮廓感知和路径辅助判断。将 LiDAR 与编码器、电机驱动和控制算法结合，有助于理解移动机器人从感知到执行的完整链路。

## My Role

- 整理小车硬件系统组成，包括主控、LiDAR、电机驱动、编码器和电源模块。
- 记录传感器接口、通信方式、接线关系和数据读取过程。
- 学习并实践左右轮电机控制、PWM 调速和编码器速度反馈。
- 整理 Navigation / Path Following 的基础算法思路和调试参数。
- 记录通信异常、电机抖动、路径偏差、供电问题等工程调试过程。
- 持续完善项目文档，后续补充图片、视频、代码和实验数据。

## Hardware System

计划整理和维护的硬件模块包括：

| Module | Purpose | Notes |
| --- | --- | --- |
| Main Controller | 控制逻辑、传感器读取和电机控制 | STM32 或其他嵌入式控制器 |
| LiDAR | 距离测量、障碍物检测和路径辅助判断 | 记录通信协议和数据格式 |
| Motor Driver | 驱动左右轮电机 | 关注 PWM、方向控制和保护 |
| Encoder | 速度 / 里程反馈 | 用于闭环控制和运动状态估计 |
| IMU / Other Sensors | 姿态或辅助避障信息 | 根据实际硬件持续补充 |
| Power Module | 给主控、传感器和电机供电 | 关注电压、电流和干扰问题 |
| Chassis | 底盘、轮组、支架和安装结构 | 后续补充尺寸和图片 |

详细记录：[hardware-design.md](./hardware-design.md)

## Software System

软件系统计划按模块整理，便于后续调试和复盘：

```text
main
├── lidar_data
├── encoder_feedback
├── motor_control
├── path_following
├── communication_debug
└── system_state
```

- `lidar_data`: 读取和解析 LiDAR 数据。
- `encoder_feedback`: 读取编码器脉冲并计算轮速。
- `motor_control`: 控制左右轮速度和方向。
- `path_following`: 根据路径偏差调整运动状态。
- `communication_debug`: 通过串口输出调试信息和关键参数。
- `system_state`: 管理启动、运行、停止和异常状态。

详细记录：[software-design.md](./software-design.md)

## Navigation / Path Following

当前导航与寻迹部分处于学习和初步实现思路整理阶段，重点包括：

- 从 LiDAR 或其他传感器数据中提取路径、边界或障碍物信息。
- 计算小车与目标路径之间的偏差。
- 根据偏差调整左右轮速度差。
- 尝试使用比例控制或 PID 思路改善路径跟踪稳定性。
- 记录路径丢失、障碍物检测和紧急停车等异常场景。

详细记录：[navigation-algorithm.md](./navigation-algorithm.md)

## Motor Control

电机控制是本项目的核心实践内容之一，当前关注：

- PWM 调速和方向控制。
- 编码器测速和速度单位换算。
- 左右轮速度同步。
- PID 速度闭环的参数记录和调试。
- 启动、制动、转向和低速抖动问题。

后续会补充电机参数、驱动模块型号、控制周期和实验数据。

## Sensor System

传感器系统计划记录：

- LiDAR 数据格式、通信方式和异常数据处理。
- 编码器 A / B 相计数、方向判断和测速方法。
- IMU 或其他传感器的接口和使用场景。
- 传感器安装位置、标定方法和数据滤波思路。

相关专题笔记会同步整理到：[Sensors Notes](../../notes/sensors/)

## Debugging Record

项目会重点记录工程调试过程，而不只记录最终结果。当前计划覆盖：

- LiDAR 无数据、数据异常或通信不稳定。
- 电机方向与控制指令不一致。
- 编码器计数方向错误或测速跳变。
- 小车直行偏航、转弯不稳定或路径跟踪振荡。
- 供电不足、电机干扰或系统复位。
- 参数修改前后的测试现象和结论。

详细记录：[debugging-log.md](./debugging-log.md)

## Current Progress

- 已建立项目文档结构和主要模块入口。
- 正在整理硬件系统组成、传感器接口和软件模块划分。
- 正在学习 LiDAR 数据读取、路径偏差计算和电机闭环控制思路。
- 后续会补充接线表、数据样例、调试日志和实验结果。

## Future Work

- 补充小车实物图片、接线图和系统结构图。
- 整理 LiDAR 数据解析流程和样例数据。
- 建立电机控制实验表，记录不同参数下的运行现象。
- 添加路径跟踪算法调试过程和测试场景说明。
- 补充代码片段、演示视频链接或 GIF，并避免上传大文件。

