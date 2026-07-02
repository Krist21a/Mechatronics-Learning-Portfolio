# Surgical Robot Project 手术机器人项目

## Project Overview

本项目用于记录面向腹腔镜微创手术场景的手术机器人学习与实践过程，重点关注机械结构、腕部自由度、运动学分析、嵌入式控制和电机运动控制等内容。

当前定位是“学习与实践中的项目记录”，不是完整商业级手术机器人系统。仓库中会持续记录方案调研、机构分析、控制实验、问题排查和阶段性复盘。

## Background

腹腔镜微创手术通常需要器械通过较小切口进入人体，完成夹持、牵引、切割、缝合等操作。手术机器人系统能够通过机构设计和运动控制提升操作灵活性、稳定性和精度。

学习该方向需要综合机械设计、机器人学、嵌入式系统、自动控制、传感器反馈和人机交互等知识。

## Motivation

- 理解 Surgical Robotics 的基本系统组成。
- 学习 EndoWrist 类腕部机构的自由度设计思想。
- 将机器人运动学、STM32 控制和电机驱动知识结合起来。
- 建立一个适合本科阶段持续扩展的工程学习项目。

## My Responsibilities

- 调研手术机器人机构形式和典型运动自由度。
- 整理机械结构设计思路和传动方案。
- 学习并推导基础运动学模型。
- 规划 STM32 嵌入式控制系统框架。
- 记录电机控制、传感器反馈和调试问题。
- 持续维护项目文档和复盘记录。

## System Architecture

```text
Operator Input / Control Command
        |
        v
Control Algorithm Layer
        |
        v
STM32 Embedded Controller
        |
        +--> Motor Driver
        +--> Encoder / Sensor Feedback
        +--> Limit / Safety Signal
        |
        v
Mechanical Transmission and Surgical Instrument Prototype
```

## Mechanical Design

当前机械设计重点包括：

- 手术器械末端自由度分析。
- 腕部弯曲、旋转和夹持动作的机构方案比较。
- 绳驱、连杆、齿轮或微型传动方案的可行性学习。
- 结构尺寸、运动范围、负载和装配方式的初步分析。

详细记录见：[mechanism-design.md](mechanism-design.md)

## Control System

控制系统当前以学习和原型验证为主，重点关注：

- 电机开环与闭环控制差异。
- 位置控制、速度控制和限位保护。
- PID 控制的参数整定方法。
- 控制指令到执行机构动作之间的映射关系。

详细记录见：[control-system.md](control-system.md)

## Embedded System

嵌入式系统计划基于 STM32 进行学习和实验，模块包括：

- GPIO：按键、限位、状态指示。
- UART：调试输出和上位机通信。
- Timer / PWM：电机或舵机控制。
- Encoder：位置或速度反馈。
- Sensor Interface：安全信号与状态检测。

详细记录见：[embedded-system.md](embedded-system.md)

## Kinematics Analysis

运动学分析重点包括：

- 坐标系建立。
- 正运动学 Forward Kinematics。
- 逆运动学 Inverse Kinematics。
- Jacobian 与速度映射。
- 奇异位形和运动范围限制。

详细记录见：[kinematics-analysis.md](kinematics-analysis.md)

## Current Progress

- 已建立项目文档结构。
- 正在整理手术机器人系统组成和典型机构方案。
- 正在学习机器人学基础，包括坐标变换和运动学建模。
- 后续将补充结构草图、控制框图、实验数据和调试记录。

## Problems and Solutions

当前主要问题：

- 对手术器械腕部机构的自由度实现方式理解还不够深入。
- 运动学模型和实际机构之间的映射需要进一步验证。
- 嵌入式控制系统需要明确硬件接口和安全保护逻辑。

问题复盘见：[problems-and-solutions.md](problems-and-solutions.md)

## Future Improvements

- 补充机构草图和 CAD 建模截图。
- 建立简化运动学模型并使用 Python / MATLAB 进行验证。
- 完成 STM32 控制原型的模块划分。
- 加入电机驱动、编码器反馈和限位保护实验。
- 形成阶段性项目总结和可展示的技术文档。

