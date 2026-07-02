# Embedded System

## 目标

记录手术机器人控制原型中的嵌入式系统学习和模块设计。

## 计划模块

| 模块 | 作用 | 当前状态 |
| --- | --- | --- |
| GPIO | 按键、限位、状态灯 | 学习中 |
| UART | 串口调试、上位机通信 | 学习中 |
| Timer / PWM | 电机或舵机控制 | 学习中 |
| Encoder | 速度和位置反馈 | 待实验 |
| Motor Driver | 驱动执行机构 | 待选型 |
| Safety Logic | 限位、急停、异常保护 | 待设计 |

## 软件结构设想

```text
Application Layer
  -> Motion Task
  -> Safety Task
Driver Layer
  -> Motor Driver
  -> Encoder Driver
  -> UART Driver
Hardware Layer
  -> STM32 Peripheral Configuration
```

## 待补充

- STM32 型号选择。
- 引脚分配表。
- 通信协议。
- 控制周期与任务调度方式。

