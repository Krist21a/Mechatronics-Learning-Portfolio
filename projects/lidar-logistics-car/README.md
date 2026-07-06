# LiDAR Logistics Car 智能仓储搬运机器人

## Project Overview

本项目记录一台面向室内仓储搬运场景的智能物流小车 / 搬运机器人。系统集成四麦克纳姆轮移动底盘、机械臂抓取机构、视觉与点云识别、LiDAR 建图导航、ROS2 上位机和 STM32 下位机控制。

项目目标不是只做一台能动的小车，而是尽量打通移动机器人从“机械结构 - 嵌入式控制 - 传感器感知 - 地图构建 - 路径规划 - 目标抓取”的完整工程链路。

## Scenario

仓储搬运机器人需要在室内空间中完成自主移动、目标识别、路径规划、避障和末端抓取。这个项目以实验室 / 教室环境为测试场景，重点验证以下能力：

- 在室内环境中使用 LiDAR 构建二维地图。
- 结合编码器和 IMU 进行里程计与姿态估计。
- 使用 ROS2 完成地图可视化、定位、全局规划和局部避障。
- 使用四麦克纳姆轮底盘实现灵活移动。
- 使用视觉 / 点云方法识别待抓取目标。
- 使用三轴机械臂执行伸缩、升降和 Yaw 转向抓取。

## System Architecture

```text
Smart Logistics Robot
├── Mechanical System
│   ├── four-mecanum-wheel chassis
│   ├── aluminum base plate
│   └── three-axis arm: Y extension, Z lift, Yaw rotation
├── Embedded Control
│   ├── STM32F407 chassis controller
│   └── STM32C8T6 arm controller
├── Perception
│   ├── RPLIDAR A1M8
│   ├── MPU6050 IMU
│   ├── wheel encoders
│   └── camera / point cloud sensing
├── ROS2 System
│   ├── Ubuntu 22.04 + ROS2 Humble
│   ├── slam_toolbox
│   ├── robot_localization EKF
│   ├── AMCL
│   ├── DWB controller
│   └── RViz2 visualization
└── Task Layer
    ├── map building
    ├── map correction
    ├── navigation
    ├── object localization
    └── grasp execution
```

## My Role

- 参与智能仓储搬运机器人整体方案整理与系统模块拆分。
- 整理四麦克纳姆轮底盘结构设计、装配过程、工程图输出和干涉检查。
- 梳理底盘正 / 逆运动学，用于机器人速度与四轮线速度之间的转换。
- 整理 STM32 下位机控制逻辑，包括机械臂坐标解算、行程限位和 STEP / DIR / PWM 输出。
- 对比 YOLO 视觉识别与点云识别效果，记录最终采用点云方案的原因。
- 整理 ROS2 建图导航流程，包括 LiDAR、编码器、IMU、EKF、slam_toolbox 和 RViz2。
- 记录地图修正、虚拟墙构建、导航参数调整和实验结果。

## Key Features

| Feature | Implementation | Result |
| --- | --- | --- |
| 四麦克纳姆轮底盘 | 独立驱动、支持横移与原地旋转 | 适合狭小室内仓储通道 |
| 机械臂抓取 | Y 轴伸缩、Z 轴升降、Yaw 转向 | 可完成桌面目标抓取动作 |
| 底盘运动学 | 四轮正 / 逆运动学解算 | 支持由目标速度计算轮速 |
| LiDAR 建图 | RPLIDAR A1M8 + slam_toolbox | 完成室内二维地图构建 |
| 多传感器融合 | 编码器 + IMU + EKF | 输出融合里程计用于导航 |
| 地图修正 | PGM 栅格编辑 + 虚拟墙 | 提升可通行区域与真实环境的一致性 |
| 导航实验 | AMCL + 全局规划 + DWB 局部控制 | 完成短程与长距离目标点导航 |
| 目标识别 | YOLOv11 与点云识别对比 | 最终选择点云方案提高定位可靠性 |

## Hardware System

| Module | Model / Method | Purpose |
| --- | --- | --- |
| Main computer | Raspberry Pi 4B | ROS2 上位机、建图导航和任务调度 |
| Chassis controller | STM32F407 | 底盘电机、编码器和下位机通信 |
| Arm controller | STM32C8T6 | 三轴机械臂坐标解算与驱动输出 |
| LiDAR | RPLIDAR A1M8 | 二维激光建图与导航感知 |
| IMU | MPU6050 | 姿态数据输入 |
| Encoder | wheel encoder | 轮速 / 里程计反馈 |
| Chassis | four mecanum wheels | 横移、旋转和全向移动 |
| Arm | Y / Z / Yaw mechanism | 目标抓取与搬运 |

详细记录：[hardware-design.md](./hardware-design.md)

## Software System

系统软件分为 ROS2 上位机、STM32 下位机、识别模块和调试工具：

- `ros2_navigation`: 建图、定位、规划、避障和 RViz2 可视化。
- `sensor_fusion`: 同步 `/scan`、`/wheel/odom`、`/imu/data_raw`，通过 EKF 输出 `/odometry/filtered`。
- `chassis_control`: 解析速度指令，完成四麦克纳姆轮运动控制。
- `arm_control`: 接收目标坐标，完成机械臂半径、角度和高度解算。
- `vision_recognition`: 对比 YOLOv11 与点云分割 / 聚类方案。
- `debug_tools`: 远程桌面、串口输出、地图编辑和参数调试。

详细记录：[software-design.md](./software-design.md)

## Navigation and Mapping

建图导航部分围绕 ROS2 构建：

1. RPLIDAR A1M8 发布 `/scan`。
2. STM32F407 与轮式编码器生成 `/wheel/odom`。
3. MPU6050 发布 `/imu/data_raw`。
4. 使用 `robot_localization` 的 EKF 融合多源传感器数据。
5. 使用 `slam_toolbox` 构建二维地图。
6. 使用 RViz2 查看建图与导航状态。
7. 对玻璃、门口和空隙区域进行地图修正与虚拟墙标注。
8. 使用 AMCL、全局规划和 DWB 局部控制完成目标点导航。

详细记录：[navigation-algorithm.md](./navigation-algorithm.md)

## Vision and Grasping

视觉识别部分对比了 YOLOv11 与点云识别：

- YOLOv11 能识别箱体目标，但容易受背景信息、数据集规模和训练参数影响。
- 点云方案通过 RANSAC 平面分割、阈值筛选和欧氏聚类提取目标，受光照和纹理干扰更小。
- 最终方案倾向于使用点云识别输出目标空间位置，再由机械臂控制系统执行抓取。

详细记录：[vision-recognition.md](./vision-recognition.md) 和 [arm-control.md](./arm-control.md)

## Debugging Summary

项目调试中记录的关键问题包括：

- 玻璃 / 空隙区域导致 LiDAR 地图误判。
- 原始地图可通行区域与真实障碍不一致。
- 局部边界存在虚假开放区域，影响全局路径规划。
- 里程计尺度标定不准确，导致定位和导航误差。
- 姿态存在漂移，门口通行场景容易出现路径跳跃。
- 视觉检测受背景干扰，点云识别在实际定位中更稳定。

详细记录：[debugging-log.md](./debugging-log.md)

## Current Progress

- 已完成四麦克纳姆轮底盘结构设计、装配模型和实物组装。
- 已完成机械臂三轴控制方案整理，并实现抓取动作验证。
- 已完成底盘正 / 逆运动学思路整理。
- 已完成 ROS2 建图系统搭建和二维地图生成。
- 已完成原始地图问题分析、虚拟墙构建和修正地图输出。
- 已完成目标点导航实验，验证全局路径规划、局部避障和短程 / 长距离导航。
- 已完成 YOLO 与点云识别方案对比，明确点云识别更适合当前实验环境。

## Future Work

- 补充经过压缩处理的实物图片、系统框图、导航截图和演示 GIF。
- 整理 STM32 端关键代码片段和通信协议。
- 补充底盘尺寸、轮距、轮半径、控制周期和电机参数。
- 建立导航参数表，记录 `ticks_per_rev`、Yaw 比例、`inflation_radius`、`robot_radius` 等参数调整过程。
- 将机械臂抓取、目标识别和导航任务进一步串联成完整搬运流程。
