# Software Design

## 目标

记录物流小车软件系统结构，包括传感器读取、电机控制、导航算法和调试接口。

## 软件模块

```text
main
├── sensor_lidar
├── encoder
├── motor_control
├── path_following
├── communication
└── debug_log
```

## 模块说明

| 模块 | 功能 | 当前状态 |
| --- | --- | --- |
| sensor_lidar | 解析 LiDAR 数据 | 待实现 / 整理 |
| encoder | 读取轮速反馈 | 待实验 |
| motor_control | PWM 调速和方向控制 | 学习中 |
| path_following | 路径跟踪控制 | 待验证 |
| communication | 串口输出和参数调试 | 学习中 |
| debug_log | 记录实验数据 | 待完善 |

## 设计原则

- 先保证底层驱动稳定，再叠加算法。
- 每次调试只改变少量参数。
- 保留关键测试数据，便于后续复盘。

