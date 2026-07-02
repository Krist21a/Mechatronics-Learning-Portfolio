# Surgical Robot Project 手术机器人项目

## Project Overview

本项目用于记录面向腹腔镜微创手术场景的手术机器人学习与实践过程。当前重点不是完成完整系统，而是围绕机构设计、运动学分析、嵌入式控制和调试记录建立一个可持续更新的项目档案。

项目内容会随着学习进展逐步补充，包括结构方案分析、控制系统框架、STM32 外设实验、电机驱动思路、运动学建模和问题复盘。

## Background

腹腔镜微创手术器械通常需要在有限空间内完成夹持、牵引、旋转、弯曲等操作，对末端机构自由度、传动方式、运动控制和安全边界都有较高要求。

手术机器人方向涉及 Mechanical Design、Robotics、Embedded Systems、Control Engineering 和 Human-Machine Interaction 等知识。本项目将这些内容拆分成可学习、可记录、可复盘的模块。

## Motivation

- 学习 Surgical Robotics 的基本系统组成和技术路线。
- 理解类 EndoWrist 末端机构的自由度设计和传动思路。
- 将机器人学、嵌入式系统和运动控制知识结合到一个长期项目中。
- 通过文档记录真实的学习过程、设计思考、调试问题和后续改进。

## My Role

- 调研手术机器人末端器械的常见运动形式和机构方案。
- 整理腕部自由度、传动结构、工作空间和负载约束。
- 学习并记录 Forward Kinematics、Inverse Kinematics 和 Jacobian 基础。
- 规划基于 STM32 的控制系统框架，包括驱动、反馈和安全信号。
- 记录电机控制、编码器反馈、限位保护和串口调试过程。
- 持续维护项目文档，区分已整理内容、学习中内容和计划完善内容。

## System Architecture

```text
Operator Input / Control Command
        |
        v
Motion Planning / Control Logic
        |
        v
STM32 Embedded Controller
        |
        +--> Motor Driver
        +--> Encoder / Sensor Feedback
        +--> Limit / Safety Signal
        +--> UART Debug Interface
        |
        v
Mechanical Transmission and Surgical Instrument Prototype
```

当前架构仍处于学习和设计整理阶段，后续会结合具体实验补充控制周期、接口定义、传感器反馈和安全逻辑。

## Mechanical Design

机械部分重点关注末端器械的自由度设计和传动方式分析：

- 正在整理 pitch、yaw、roll、grip 等运动自由度的实现思路。
- 对比绳驱、连杆、齿轮和微型传动结构的特点与限制。
- 关注结构尺寸、装配空间、传动间隙、负载能力和可制造性。
- 后续计划补充 CAD 草图、机构简图、自由度分析表和载荷估算。

详细记录：[mechanism-design.md](./mechanism-design.md)

## Kinematics Analysis

运动学部分用于建立“关节变量到末端位姿”的分析能力：

- 正在学习坐标系建立、齐次变换矩阵和旋转矩阵。
- 计划基于简化机构模型进行 Forward Kinematics 推导。
- 后续继续整理 Inverse Kinematics、Jacobian 和工作空间分析。
- 运动学结果会尽量和具体机构参数、控制变量联系起来。

详细记录：[kinematics-analysis.md](./kinematics-analysis.md)

## Embedded Control System

嵌入式控制系统计划以 STM32 为主要学习平台，逐步整理以下模块：

- GPIO：按键、限位开关、状态指示和安全输入。
- UART：串口调试、上位机通信和数据输出。
- Timer / PWM：电机或舵机控制信号生成。
- Encoder：速度和位置反馈。
- Motor Driver：执行机构驱动和方向控制。
- Safety Logic：限位保护、异常状态和急停思路。

详细记录：[embedded-system.md](./embedded-system.md) 和 [control-system.md](./control-system.md)

## Current Progress

- 已建立项目文档结构和主要模块入口。
- 正在整理手术机器人系统组成、末端机构自由度和传动方案。
- 正在学习机器人学基础，包括坐标变换、运动学建模和 Jacobian 概念。
- 已规划 STM32 控制系统的初步模块划分。
- 后续将逐步补充实验记录、结构示意、控制框图和调试数据。

## Problems & Debugging

当前重点记录的问题包括：

- 末端机构自由度设计与实际传动结构之间的对应关系。
- 简化运动学模型如何和真实结构参数连接。
- 电机、编码器、限位信号和控制算法之间的接口设计。
- 控制系统中安全边界、异常状态和调试信息的组织方式。

问题复盘入口：[problems-and-solutions.md](./problems-and-solutions.md)

## Future Work

- 补充机构草图、CAD 截图和自由度分析表。
- 建立简化运动学模型，并使用 Python / MATLAB 进行验证。
- 完善 STM32 控制原型的软件分层和接口说明。
- 记录电机控制、编码器反馈、限位保护和串口调试实验。
- 添加阶段性复盘，总结学习收获、问题和下一步计划。

## Notes on Confidentiality

本项目仅用于个人学习与项目记录。涉及学校、实验室、比赛或未公开项目的资料时，会避免上传敏感图纸、内部代码、实验数据、未授权图片和非公开文档。公开内容以学习总结、通用原理、个人复盘和可公开展示的材料为主。

