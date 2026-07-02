# Mechatronics Learning Portfolio

![Repo Size](https://img.shields.io/github/repo-size/your-username/Mechatronics-Learning-Portfolio)
![Last Commit](https://img.shields.io/github/last-commit/your-username/Mechatronics-Learning-Portfolio)
![Markdown](https://img.shields.io/badge/language-Markdown-blue)
![Topics](https://img.shields.io/badge/topics-robotics%20%7C%20embedded%20%7C%20mechatronics%20%7C%20control-green)

## About This Repository

This repository documents my engineering learning portfolio, including project practice, debugging experience, technical notes, and skill development. 内容以机电一体化、机器人、嵌入式系统、运动控制和智能感知方向为主，持续记录从课程知识到工程实践的学习过程。

它不是一次性完成的展示页，而是一个长期维护的学习档案。项目记录会尽量区分“已完成”“学习中”“正在整理”和“计划完善”的内容，避免夸大实际进展。

## About Me

我是一名关注机电 / 机器人方向的学生，目前主要围绕以下方向进行学习和实践：

- Surgical Robotics 手术机器人
- Embedded Systems 嵌入式系统
- Motion Control 运动控制
- Computer Vision 计算机视觉
- Robotics 机器人学
- Control Engineering 自动控制

我希望通过这个仓库，把课程学习、项目设计、调试记录和阶段性复盘沉淀下来，逐步形成一个可持续更新的个人工程学习作品集。

## Featured Projects

| Project | Description | Keywords | Status |
| --- | --- | --- | --- |
| [Surgical Robot Project 手术机器人项目](./projects/surgical-robot/) | 面向腹腔镜微创手术场景的手术机器人机构、运动学和嵌入式控制学习项目 | Surgical Robotics, EndoWrist, STM32, Motion Control, Kinematics | 学习中 / 持续完善 |
| [LiDAR Logistics Car 激光雷达物流小车](./projects/lidar-logistics-car/) | 基于激光雷达、传感器和嵌入式控制的自动寻迹 / 导航小车项目记录 | LiDAR, Embedded System, Path Following, Motor Control | 实践中 / 正在整理 |
| [Mini Engineering Projects 小型工程项目](./projects/mini-projects/) | 记录课程设计、实验、小型机构、传感器和控制系统练习 | Mechanical Design, Sensors, Control, C/Python | 持续更新 |

## Project Highlights

### Surgical Robot Project 手术机器人项目

- 关注腹腔镜微创手术器械的多自由度末端机构设计。
- 正在整理类 EndoWrist 机构的自由度分析、传动思路和结构约束。
- 记录 STM32 控制系统、驱动模块、编码器反馈和运动控制调试过程。
- 正在学习 Forward Kinematics、Inverse Kinematics 和 Jacobian 等机器人学基础。
- 后续计划完善运动学建模、控制策略、实验数据和问题复盘。

### LiDAR Logistics Car 激光雷达物流小车

- 围绕 LiDAR、编码器、IMU 和电机驱动模块整理移动机器人系统结构。
- 正在记录传感器数据读取、路径偏差计算和 Path Following 控制思路。
- 关注 PWM 调速、Encoder 反馈、PID 控制和左右轮速度同步问题。
- 持续补充接线记录、通信异常、电机抖动、路径偏差等调试内容。
- 后续计划加入系统结构图、实物图片、代码片段和演示链接。

### Mini Engineering Projects 小型工程项目

- 用于沉淀课程设计、实验项目和短周期工程练习。
- 计划覆盖 C / Python 编程、STM32 外设、传感器读取和基础控制实验。
- 通过统一模板记录项目背景、系统结构、核心算法和调试过程。
- 后续会把小项目与课程知识、专题笔记和综合项目建立链接。

## Technical Skills

更完整的技能记录见：[技能清单](./skills.md)。

### Programming

- C
- C++
- Python
- C#

### Embedded Systems

- STM32
- GPIO / UART / SPI / I2C
- PWM / Timer / Encoder
- Sensor Interface
- Motor Driver

### Robotics & Control

- Forward Kinematics
- Inverse Kinematics
- Jacobian
- PID Control
- Motion Control
- Trajectory Planning

### Computer Vision & AI

- OpenCV
- Image Processing
- Camera Calibration
- Object Detection
- Basic Machine Learning

### Mechanical Design

- Mechanism Design
- Transmission Design
- CAD Modeling
- Load Analysis

## Repository Structure

```text
projects/      Project practice and engineering records
courses/       Course notes and learning summaries
notes/         Topic-based technical notes
weekly-logs/   Weekly learning logs and review records
templates/     Reusable project, course, debugging, and weekly templates
assets/        Images, diagrams, and public visual materials
```

- `projects/`: 重点展示项目实践，包括手术机器人、激光雷达物流小车和小型工程项目。
- `courses/`: 记录 C 语言、Python、机器人学、自动控制、计算机视觉等课程学习。
- `notes/`: 按专题整理 STM32、电机控制、传感器、OpenCV、ROS 和控制理论。
- `weekly-logs/`: 用于持续记录每周学习进展、问题和下一步计划。
- `templates/`: 提供项目记录、课程笔记、调试记录和周记录模板。
- `assets/`: 存放后续补充的图片、结构图、流程图和展示材料。

## Learning Roadmap

学习路线从编程基础和工程工具开始，逐步进入嵌入式系统、传感器、电机控制、机器人学、计算机视觉、人工智能和综合项目实践。

查看完整路线：[学习路线](./roadmap.md)

## Recent Updates

- 2026-07: Initialized the learning portfolio structure
- 2026-07: Added surgical robot project documentation
- 2026-07: Added LiDAR logistics car project documentation

更多阶段记录可查看：[时间线](./timeline.md) 和 [周记录](./weekly-logs/)。

## Documentation Principles

- 真实记录学习过程，不把计划内容写成已经完成的结果。
- 项目复盘优先于简单结果展示，重点说明思路、问题和改进。
- 重视问题排查和调试记录，保留现象、假设、测试和结论。
- 将课程知识与项目实践联系起来，避免笔记和项目相互脱节。
- 注意保密、版权和项目资料边界，不上传学校、实验室、比赛或未公开项目的敏感资料。
- 不提交大文件、未经授权的图片、第三方代码或内部资料。

## GitHub Topics

建议添加的 GitHub Topics 见：[github-topics.md](./github-topics.md)。

## Contact / Links

- GitHub: https://github.com/your-username
- Email: your-email@example.com

