# 第 3 章 STM32F1 系列微控制器知识笔记总结

> 本章复习主线：STM32F1 是什么、内部有什么、时钟怎么来和分配、最小系统怎样搭建。复习时先背框架，再背表格和易考问答。

## 学习目标

- 了解 STM32F1 系列微控制器的定位、内核和型号命名规则。
- 熟悉 STM32F1 的系统架构、总线结构、内部资源和存储器映像。
- 掌握 STM32F1 的时钟系统、启动配置和外设时钟使能思想。
- 理解 STM32F103RBT6 的常用引脚、最小系统、JTAG / SWD 调试接口。
- 能够把课程知识连接到 STM32 外设配置、嵌入式调试和机器人控制项目中。

## 总体框架

```text
STM32F1 = Cortex-M3 内核 + 存储器 + 总线 + 外设
外设通过 AHB / APB 总线连接
所有模块工作都离不开 RCC 时钟
芯片能运行必须有电源、时钟、复位
```

四条主线：

- 产品：STM32F1 是 ST 公司基于 ARM Cortex-M3 的 32 位 MCU。
- 架构：Cortex-M3、Flash、SRAM、DMA、RCC、AHB、APB1、APB2 和各类外设。
- 时钟：HSI、HSE、LSI、LSE、PLL，常用 HSE 8 MHz 经 PLL x9 得到 72 MHz。
- 硬件：BOOT 配置、电源、晶振、复位、去耦电容、SWD / JTAG。

## STM32F1 系列简介

STM32F1 是 ST 公司推出的基于 ARM Cortex-M3 内核的 32 位微控制器系列。课件中提到，ST 在 2007 年 6 月发布了早期基于 Cortex-M3、内嵌 32KB 到 128KB Flash 的 STM32 微控制器产品。

必须记住：

- STM32F1 使用 ARM Cortex-M3 内核。
- Cortex-M3 是 32 位内核。
- STM32F1 是早期经典 Cortex-M3 系列，常用于嵌入式控制和教学实验。

### Cortex-M3 特点回顾

| 特点 | 记忆说明 |
| --- | --- |
| Thumb-2 指令集 | 提高代码密度和执行效率 |
| 哈佛结构 | 指令和数据通路分开 |
| 三级流水线 | 取指、译码、执行 |
| 32 位单周期乘法 | 乘法速度快 |
| 硬件除法 | 支持硬件级除法 |
| 低延迟中断 | 中断响应快 |
| 线程模式和处理模式 | 普通程序与异常处理分开 |
| 调试支持 | 便于在线下载和调试 |

简答模板：

STM32F1 基于 ARM Cortex-M3 内核。Cortex-M3 是 32 位内核，采用 Thumb-2 指令集、哈佛结构和三级流水线，支持硬件乘除法、低延迟中断和调试功能，适合嵌入式控制系统。

## 型号命名规则

以 `STM32F103RBT6` 和 `STM32F103RCT6` 为重点。

| 字段 | 含义 |
| --- | --- |
| STM32 | ST 公司的 32 位 ARM 微控制器 |
| F | 通用型产品系列 |
| 103 | 增强型系列 |
| R | 64 引脚 |
| B | 128KB Flash |
| C | 256KB Flash |
| T | LQFP 封装 |
| 6 | 工作温度 -40℃ 到 85℃ |

重点区分：

- `STM32F103RBT6`：64 引脚，128KB Flash，LQFP 封装。
- `STM32F103RCT6`：64 引脚，256KB Flash，LQFP 封装。

容量字段：

| 字段 | Flash 容量 |
| --- | --- |
| 4 | 16KB |
| 6 | 32KB |
| 8 | 64KB |
| B | 128KB |
| C | 256KB |
| D | 384KB |
| E | 512KB |

最常考：`B = 128KB`，`C = 256KB`。

## 系统架构

STM32F1 内部主要组成：

- 内核：Cortex-M3。
- 存储器：Flash、SRAM。
- 数据搬运：DMA1 / DMA2。
- 总线：AHB、APB1、APB2。
- 时钟复位：RCC。
- 外设：GPIO、EXTI、AFIO、ADC、TIM、USART、SPI、I2C、CAN、USB、RTC、看门狗等。

记忆方式：

```text
内核负责执行
存储器负责保存程序和数据
总线负责连接
RCC 负责时钟和复位
DMA 负责数据搬运
外设负责输入输出和控制
```

### AMBA 总线

STM32F1 使用 ARM AMBA 总线结构。

| 总线 | 英文 | 作用 |
| --- | --- | --- |
| AHB | Advanced High-performance Bus | 高性能系统总线 |
| APB | Advanced Peripheral Bus | 外设总线 |

记忆：

- AHB：高速系统总线。
- APB：外设总线。

### APB1 与 APB2

| 总线 | 类型 | 最大频率 | 常见外设 |
| --- | --- | --- | --- |
| APB1 | 低速外设总线 | 36 MHz | TIM2-TIM7、USART2/3、UART4/5、SPI2/3、I2C1/2、CAN、USB、DAC、PWR、BKP、WWDG |
| APB2 | 高速外设总线 | 72 MHz | GPIOA-E、AFIO、EXTI、USART1、SPI1、ADC1/2、TIM1 |

容易考：

- GPIO 通常挂在 APB2。
- USART1 挂在 APB2。
- USART2、USART3 挂在 APB1。
- APB2 高速，APB1 低速。

### DMA

DMA 全称 Direct Memory Access，即直接存储器访问。

作用：DMA 可以在不占用 CPU 的情况下，实现外设和存储器之间、存储器和存储器之间的数据搬运，从而减少 CPU 参与，提高系统效率。

常见支持 DMA 的外设：

- 定时器。
- ADC。
- SPI。
- I2C。
- USART。

记忆句：DMA 是数据搬运工，可以减轻 CPU 负担。

## STM32F103RBT6 内部资源

| 项目 | 记忆点 |
| --- | --- |
| 内核 | ARM Cortex-M3 |
| 位数 | 32 位 |
| 最高主频 | 72 MHz |
| Flash | 128KB |
| SRAM | 20KB |
| 工作电压 | 2.0V 到 3.6V |
| 常用电压 | 3.3V |
| ADC | 12 位 |
| 调试接口 | JTAG / SWD |
| 常用启动方式 | BOOT0 = 0，从主 Flash 启动 |

补充：

- STM32F103RCT6 的 Flash 为 256KB，SRAM 通常为 48KB，具体以数据手册为准。
- STM32F103RBT6 是本章实验板重点，优先记 `128KB Flash + 20KB SRAM`。

### ADC

| 参数 | 值 |
| --- | --- |
| ADC 位数 | 12 位 |
| 输入电压范围 | 0 到 3.6V |
| 通道数 | 最多 16 通道 |
| 转换时间 | 可达 1 us |

容易考：STM32F103 的 ADC 是 12 位。

### GPIO

记忆点：

- STM32F103RBT6 约 51 个 GPIO。
- 部分 I/O 可容忍 5V。
- 多数 I/O 可映射到外部中断。
- GPIO 不只是普通输入输出，还可以复用为 USART、SPI、I2C、ADC、定时器等功能。

### 定时器

常见定时器类型：

- 通用定时器。
- 高级控制定时器。
- 独立看门狗。
- 窗口看门狗。
- SysTick 定时器。

SysTick 是 Cortex-M3 内核自带的 24 位递减计数器，常用于系统节拍。

### 通信接口

STM32F103 常见通信接口：

| 接口 | 数量或特点 |
| --- | --- |
| I2C | 最多 2 个 |
| USART | 最多 3 个 |
| SPI | 最多 2 个 |
| CAN | 支持 CAN 2.0B |
| USB | USB 2.0 全速接口 |

## 存储器映像

STM32 将程序存储器、数据存储器、寄存器和输入输出端口统一组织在一个 4GB 线性地址空间中。

关键词：

- 4GB。
- 线性地址空间。
- 统一编址。
- 存储器映像。

常用地址：

| 区域 | 起始地址 |
| --- | --- |
| 启动映射区 | `0x00000000` |
| Flash | `0x08000000` |
| SRAM | `0x20000000` |
| 外设寄存器 | `0x40000000` |
| Cortex-M3 内部外设 | `0xE0000000` |

最常考：

- Flash：`0x08000000`。
- SRAM：`0x20000000`。
- 外设寄存器：`0x40000000`。

### Flash 容量与地址范围

地址从 0 开始，所以最大地址 = 容量 - 1。

| 地址范围 | 容量 |
| --- | --- |
| `0x0000-0x03FF` | 1KB |
| `0x0000-0x3FFF` | 16KB |
| `0x0000-0x4FFF` | 20KB |
| `0x0000-0xFFFF` | 64KB |
| `0x0000-0x1FFFF` | 128KB |
| `0x0000-0x3FFFF` | 256KB |

重点：

- 128KB = `0x20000` 字节，地址范围 `0x00000-0x1FFFF`。
- 256KB = `0x40000` 字节，地址范围 `0x00000-0x3FFFF`。

### 启动地址映射

STM32 启动时，CPU 从 `0x00000000` 地址开始取指，但该地址映射到哪里由 BOOT0 / BOOT1 决定。

如果从 Flash 启动：

- `0x00000000` 映射到主 Flash。
- 主 Flash 仍然可以从 `0x08000000` 访问。

记忆句：`0x00000000` 是启动映射地址，`0x08000000` 是 Flash 本来地址。

## 时钟系统

STM32F1 时钟系统是本章重点。必须理解：不同模块对时钟频率要求不同，RCC 负责选择、倍频、分频和使能时钟。

### 五个时钟源

| 名称 | 含义 | 典型频率 |
| --- | --- | --- |
| HSI | 高速内部时钟 | 8 MHz |
| HSE | 高速外部时钟 | 4-16 MHz，常用 8 MHz |
| LSI | 低速内部时钟 | 约 40 kHz |
| LSE | 低速外部时钟 | 32.768 kHz |
| PLL | 锁相环倍频输出 | 由 HSI 或 HSE 倍频得到 |

口诀：

```text
高速内外 HSI、HSE
低速内外 LSI、LSE
倍频靠 PLL
```

必须记：

- HSI = 8 MHz。
- HSE 常用 8 MHz。
- LSI 约 40 kHz。
- LSE = 32.768 kHz。
- SYSCLK 最大 72 MHz。

### SYSCLK 来源

SYSCLK 可以来自：

- HSI。
- HSE。
- PLL。

LSI 和 LSE 一般作为低速时钟源，常用于 RTC、独立看门狗等低速模块。

### 常见 72 MHz 配置

最常用配置：

```text
外部 8 MHz HSE -> PLL x9 -> 72 MHz SYSCLK
```

记忆：`8 MHz x 9 = 72 MHz`。

### 时钟分配关系

```text
SYSCLK -> AHB -> HCLK
HCLK -> APB1 -> PCLK1
HCLK -> APB2 -> PCLK2
PCLK1 -> APB1 外设
PCLK2 -> APB2 外设
```

频率限制：

| 时钟 | 最大频率 |
| --- | --- |
| SYSCLK | 72 MHz |
| HCLK | 72 MHz |
| PCLK1 | 36 MHz |
| PCLK2 | 72 MHz |
| ADC 时钟 | 14 MHz |
| USB 时钟 | 48 MHz |

### 定时器时钟倍频规则

课件中的重要规则：

```text
APB 预分频 = 1：TIMxCLK = PCLKx
APB 预分频 != 1：TIMxCLK = 2 x PCLKx
```

例题：

如果 PCLK1 = 36 MHz，且 APB1 预分频系数不是 1，则 TIM2-TIM7 时钟为 72 MHz。

### 为什么使用多个时钟

简答模板：

STM32 内部包含 CPU、总线、ADC、定时器、通信接口等多个模块，不同模块对时钟频率要求不同。高速模块使用高速时钟，低速外设使用低速时钟。同时，各模块时钟可以独立控制，不使用时关闭时钟，有利于降低功耗。

### 使用外设前为什么要开时钟

STM32 为了降低功耗，各外设时钟可以独立开关。外设没有时钟时不能正常工作，因此使用 GPIO、USART、ADC、TIM 等外设前，必须先通过 RCC 使能对应的外设时钟。

例子：

```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
```

## BOOT0 / BOOT1 启动配置

| BOOT1 | BOOT0 | 启动区域 | 使用场景 |
| --- | --- | --- | --- |
| X | 0 | Main Flash Memory | 正常运行用户程序 |
| 0 | 1 | System Memory | ST 固化 Bootloader，常用于 ISP 下载 |
| 1 | 1 | Embedded SRAM | 调试或特殊测试 |

最常用：`BOOT0 = 0`，从主 Flash 启动。

实验板常见默认配置：BOOT0 默认短接到从 Flash 启动。

## STM32F103RBT6 常用引脚

### 引脚编号

芯片有第 1 引脚标记。找到圆点或缺口标记，即第 1 脚，从第 1 脚开始逆时针编号。

### 供电脚

| 引脚 | 含义 |
| --- | --- |
| VDD | 数字电源正 |
| VSS | 数字地 |
| VDDA | 模拟电源正 |
| VSSA | 模拟地 |
| VBAT | 备用电池供电 |

记忆：DD 是电源，SS 是地，A 是模拟，BAT 是备用电池。

### 晶振引脚

| 引脚 | 作用 |
| --- | --- |
| OSC_IN / OSC_OUT | 高速外部晶振 HSE |
| OSC32_IN / OSC32_OUT | 低速外部晶振 LSE，通常 32.768 kHz |

### 复位引脚

`NRST` 是低电平有效复位引脚。N 表示低电平有效。

### 调试引脚

| 引脚 | 功能 |
| --- | --- |
| PA13 | JTMS / SWDIO |
| PA14 | JTCK / SWCLK |
| PA15 | JTDI |
| PB3 | JTDO |
| PB4 | NJTRST |
| NRST | 复位 |

最重要：

- PA13 = SWDIO。
- PA14 = SWCLK。

## 最小系统

微控制器最小系统，也叫最小应用系统，是指用最少元器件组成的可以运行的系统。

最核心三要素：

```text
电源、时钟、复位
```

记忆：电源供能，时钟驱动，复位启动。

实际 STM32 最小系统通常包括：

- 3.3V 电源。
- 去耦电容。
- 外部晶振。
- 复位电路。
- BOOT0 / BOOT1 配置。
- SWD / JTAG 下载调试接口。

### 电源

STM32F103 工作电压为 2.0V 到 3.6V，实验板一般使用 3.3V。

注意：芯片供电不是 5V。虽然部分 I/O 可容忍 5V，但 MCU 工作电压仍以 3.3V 为主。

### 去耦电容

去耦电容用于滤除电源干扰、稳定供电，应靠近 MCU 电源引脚放置。

常见值：

- 0.1 uF。
- 100 nF。

### 复位电路

常见组成：

- NRST 引脚。
- 上拉电阻。
- 电容。
- 复位按键。

NRST 被拉低时，STM32 复位。

### 晶振电路

无源晶体：

- 晶体接在 OSC_IN 和 OSC_OUT 之间。
- 两端各接一个电容到地。
- 常见负载电容为 22 pF。
- 晶体和电容应尽量靠近 MCU。

有源晶振：

- 输出接 OSC_IN。
- OSC_OUT 悬空。

记忆：无源晶体接两脚，有源晶振只进 OSC_IN。

## 系统设计原理图模块

实验板原理图常见模块：

| 模块 | 作用 |
| --- | --- |
| MCU 主控 | STM32F103RBT6 核心 |
| POWER | 提供 3.3V 电源 |
| RESET | 手动或上电复位 |
| BOOT0 | 选择启动方式 |
| JTAG / SWD | 下载和调试程序 |
| LED | I/O 输出实验 |
| BEEP | 蜂鸣器控制实验 |
| EXTEND IO | 扩展外部接口 |

考试通常不要求完整画出实验板原理图，但要能说出每个模块的作用。

## JTAG 与 SWD

### JTAG

JTAG 全称 Joint Test Action Group，是标准测试访问端口和边界扫描接口，符合 IEEE 1149.1 标准。

常见信号：

- TMS。
- TCK。
- TDI。
- TDO。
- TRST。
- NRST。
- VDD。
- GND。

### SWD

SWD 全称 Serial Wire Debug，即串行线调试接口。

| 信号 | 作用 |
| --- | --- |
| SWDIO | 串行数据线，用于数据读写 |
| SWCLK | 串行时钟线，由调试器提供 |
| NRST | 复位 |
| GND | 地 |
| 3.3V | 参考电压 |

SWD 相比 JTAG 的优点：只需要 SWDIO 和 SWCLK 两根主要信号线，占用 GPIO 更少，因此在 STM32 下载和调试中更常用。

## 开发环境

硬件开发通常参考实验板或成熟原理图进行设计。

常见软件开发工具：

- Keil MDK / uVision5。
- IAR EWARM。
- C 语言。
- 汇编语言。
- J-Link / ST-Link / CMSIS-DAP。

最常见组合：Keil MDK + C 语言 + ST-Link / J-Link + SWD。

## 简答题模板

### STM32 最小系统包括哪些部分？

STM32 最小系统是指用最少元器件组成的可运行系统，主要包括电源电路、时钟电路和复位电路三部分。实际设计中还通常包括 BOOT 启动配置电路、去耦电容以及 SWD / JTAG 下载调试接口。

### STM32 为什么使用多个时钟？

STM32 内部有 CPU、总线、定时器、ADC、通信接口等多个模块，不同模块对时钟频率要求不同。高速模块使用高速时钟，低速外设使用低速时钟。同时，各模块时钟可以独立控制，不使用时关闭时钟，有利于降低功耗。

### 为什么使用外设前要先开启时钟？

STM32 为了降低功耗，各外设时钟可以独立开关。外设没有时钟时不能正常工作，因此使用 GPIO、USART、ADC、TIM 等外设前，必须先通过 RCC 使能对应的外设时钟。

### STM32 的存储器映像有什么特点？

STM32 将程序存储器、数据存储器、寄存器和输入输出端口统一组织在一个 4GB 的线性地址空间内。Flash、SRAM 和外设寄存器都有固定地址范围，例如 Flash 起始地址为 `0x08000000`，SRAM 起始地址为 `0x20000000`，外设寄存器起始地址为 `0x40000000`。

### BOOT0 / BOOT1 有什么作用？

BOOT0 和 BOOT1 用于选择 STM32 的启动区域。当 BOOT0 = 0 时，从主 Flash 启动；当 BOOT0 = 1、BOOT1 = 0 时，从系统存储器启动；当 BOOT0 = 1、BOOT1 = 1 时，从内置 SRAM 启动。正常运行时通常选择从主 Flash 启动。

### JTAG 和 SWD 有什么区别？

JTAG 是标准测试访问端口和边界扫描接口，功能完整但占用引脚较多。SWD 是串行调试接口，主要使用 SWDIO 和 SWCLK 两根线，占用引脚少，因此在 STM32 开发中更常用。

## 与项目的联系

- 物流小车和手术机器人控制原型中的 STM32 下位机，本质上都依赖 GPIO、Timer、USART、I2C、SPI、ADC、DMA 和 NVIC 等基础资源。
- 上电后程序是否正常运行，首先要检查最小系统：3.3V 电源、晶振、复位、BOOT0、SWD 下载接口。
- 外设初始化失败时，要优先检查 RCC 外设时钟是否使能，以及外设挂在哪条 APB 总线上。
- Timer、PWM、编码器测速与 APB 定时器时钟倍频规则密切相关。
- 串口调试、传感器读取和电机控制都需要把“外设引脚复用 + RCC 时钟 + GPIO 配置 + 中断 / DMA”作为完整链路检查。

## 高频记忆清单

- STM32F1 基于 ARM Cortex-M3 内核。
- STM32F103 最高主频为 72 MHz。
- STM32F103RBT6：R = 64 脚，B = 128KB Flash，T = LQFP，6 = -40℃ 到 85℃。
- STM32F103RBT6 的 SRAM 为 20KB。
- STM32 工作电压一般为 2.0V 到 3.6V，常用 3.3V。
- APB1 是低速外设总线，最大 36 MHz。
- APB2 是高速外设总线，最大 72 MHz。
- GPIO、USART1、SPI1、ADC1、TIM1 常见于 APB2。
- USART2/3、I2C、SPI2/3、普通定时器、CAN、USB 常见于 APB1。
- Flash 起始地址为 `0x08000000`。
- SRAM 起始地址为 `0x20000000`。
- 外设寄存器起始地址为 `0x40000000`。
- HSI 是高速内部时钟，8 MHz。
- HSE 是高速外部时钟，实验板常用 8 MHz。
- LSI 是低速内部时钟，约 40 kHz。
- LSE 是低速外部时钟，32.768 kHz。
- PLL 是锁相环倍频输出。
- 常用系统时钟配置是 HSE 8 MHz 经 PLL x9 得到 72 MHz。
- ADC 最大时钟约 14 MHz。
- USB 需要 48 MHz 时钟。
- 使用任何外设前必须先开启对应外设时钟。
- BOOT0 = 0 时，从主 Flash 启动。
- BOOT0 = 1、BOOT1 = 0 时，从系统存储器启动。
- BOOT0 = 1、BOOT1 = 1 时，从 SRAM 启动。
- 最小系统三要素是电源、时钟、复位。
- NRST 是低电平有效复位。
- SWD 主要使用 SWDIO 和 SWCLK。
- PA13 = SWDIO，PA14 = SWCLK。
