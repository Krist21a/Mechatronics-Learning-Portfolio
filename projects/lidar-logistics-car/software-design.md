# Software Design

## 目标

记录智能仓储搬运机器人的软件系统结构，包括 ROS2 上位机、STM32 下位机、传感器融合、建图导航、视觉识别、机械臂控制和调试工具。

## Software Architecture

```text
software
├── ros2_bringup
│   ├── lidar_driver
│   ├── imu_driver
│   ├── wheel_odom_bridge
│   └── robot_state / TF
├── localization_mapping
│   ├── robot_localization EKF
│   ├── slam_toolbox
│   └── map correction
├── navigation
│   ├── AMCL
│   ├── global planner
│   ├── DWB local controller
│   └── costmap parameters
├── embedded_control
│   ├── STM32F407 chassis control
│   └── STM32C8T6 arm control
├── perception
│   ├── YOLOv11 detection
│   └── point cloud segmentation / clustering
└── debug_tools
    ├── RViz2
    ├── NoMachine
    ├── serial debug
    └── parameter tuning logs
```

## ROS2 Topic Flow

建图导航部分可以按以下数据链路整理：

| 数据源 | Topic / 输出 | 后续模块 |
| --- | --- | --- |
| RPLIDAR A1M8 | `/scan` | 时间同步、`slam_toolbox`、导航感知 |
| 轮式编码器 + STM32F407 | `/wheel/odom` | EKF 融合、里程计估计 |
| MPU6050 IMU | `/imu/data_raw` | EKF 融合、姿态估计 |
| EKF 融合 | `/odometry/filtered` | 定位、导航、RViz2 |
| slam_toolbox | `/map` | RViz2 可视化、导航地图 |
| 导航栈 | velocity command | 底盘控制 |

## Module Responsibilities

| 模块 | 功能 | 当前状态 |
| --- | --- | --- |
| `lidar_driver` | 读取 LiDAR 数据并发布 `/scan` | 已用于建图 |
| `wheel_odom_bridge` | 接收 STM32 / 编码器数据，生成轮式里程计 | 已用于融合定位 |
| `imu_driver` | 发布 IMU 姿态和角速度数据 | 已用于融合输入 |
| `robot_localization` | 融合 LiDAR 时间同步、编码器和 IMU 数据 | 已搭建 |
| `slam_toolbox` | 构建二维室内地图 | 已完成建图 |
| `map_correction` | 修正 PGM / YAML 地图并添加虚拟墙 | 已验证 |
| `navigation` | 完成全局规划、局部避障和目标点导航 | 已实验 |
| `arm_control` | 根据目标坐标控制机械臂伸缩、升降和转向 | 已验证动作 |
| `vision_recognition` | 对比 YOLO 与点云识别 | 已形成方案选择 |

## Embedded Control

### Chassis Controller

底盘下位机采用 STM32F407，主要任务包括：

- 读取轮式编码器数据。
- 计算轮速与里程计信息。
- 与 ROS2 上位机进行串口通信。
- 接收速度指令并控制四个底盘电机。
- 为 EKF 和导航系统提供 `/wheel/odom` 数据。

### Arm Controller

机械臂下位机采用 STM32C8T6，主要任务包括：

- 接收上位机发送的目标坐标或单轴控制指令。
- 根据目标点计算半径、角度和高度。
- 执行行程限位，防止机构超出机械范围。
- 输出 STEP / DIR 脉冲和舵机 PWM。
- 控制 Y 轴伸缩、Z 轴升降和 Yaw 转向。

详细记录见：[arm-control.md](./arm-control.md)

## Perception Software

目标识别部分尝试了两类方案：

1. YOLOv11 目标检测
   - 使用开源数据集和自建数据集合并训练。
   - 采用 Mosaic 数据增强、自适应锚框等策略。
   - 尝试轻量化部署到树莓派。

2. 点云识别
   - 使用 RANSAC 进行平面分割。
   - 调整阈值，提取非地面障碍物点云。
   - 使用欧氏聚类分析目标点云特征。

调试结论：YOLO 方案容易受背景信息、数据集规模和训练参数影响；点云方案受光照、纹理和背景干扰更小，在目标空间定位上更稳定。

详细记录见：[vision-recognition.md](./vision-recognition.md)

## Debug and Visualization

| 工具 | 用途 |
| --- | --- |
| RViz2 | 查看地图、TF、机器人位姿、规划路径和局部轨迹 |
| NoMachine | 远程连接树莓派 / 上位机桌面，查看建图和导航结果 |
| 串口调试 | 查看 STM32 通信、编码器、机械臂指令和状态 |
| PGM / YAML 地图文件 | 手动修正地图并添加虚拟墙 |

## Design Notes

- 先保证单个传感器数据稳定，再进行 EKF 融合。
- 先完成底盘和机械臂的单模块动作验证，再串联完整搬运流程。
- 导航调参要保留参数变化、现象和结论，避免只记录最终参数。
- 地图修正需要说明原始问题、修正方法和修正后效果。

## 待补充

- ROS2 launch 文件结构。
- STM32 通信协议和数据包格式。
- 关键参数表：波特率、发布频率、控制周期、滤波参数。
- 导航参数表：`inflation_radius`、`robot_radius`、DWB 参数等。
- 机械臂与视觉模块之间的目标坐标传递格式。
