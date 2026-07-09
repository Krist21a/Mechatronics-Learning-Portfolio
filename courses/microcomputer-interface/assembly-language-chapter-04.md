# 第 4 章 汇编语言基础知识笔记总结

> 本章复习主线：先理解 Cortex-M3 的 Thumb-2 指令体系，再掌握指令格式、条件标志、寻址方式、常用指令和基础程序设计模板。复习标准可以分为三层：必须背下来、必须会判断、必须会写代码。

## 学习目标

- 理解机器语言、汇编语言和高级语言之间的关系。
- 掌握 Cortex-M3 只支持 Thumb-2 指令集这一核心特点。
- 熟悉汇编指令格式、标号规则、S 后缀、条件码和 APSR 标志位。
- 掌握伪指令、立即数、寻址方式、数据传送、数据处理和跳转指令。
- 能够写出分支、循环、子程序、数据块复制等基础汇编程序。
- 能够把汇编知识用于理解 STM32 启动文件、异常入口、寄存器操作和底层调试。

## 总体框架

```text
汇编语言基础
├── 指令体系：Cortex-M3 只支持 Thumb-2
├── 指令格式：标号、操作码、S 后缀、条件码、操作数
├── 判断依据：APSR 的 N、Z、C、V 标志位
├── 操作对象：立即数、寄存器、内存地址、堆栈
├── 常用指令：LDR/STR、ADD/SUB、CMP/TST、B/BL/BX
└── 程序结构：顺序、分支、循环、子程序、数据块复制
```

一句话记忆：

```text
汇编 = 用助记符直接描述 CPU 要做的寄存器和内存操作。
```

## 编程语言与 Cortex-M3 指令体系

| 类型 | 记忆点 |
| --- | --- |
| 机器语言 | 用二进制数表示，是 CPU 唯一能直接识别和执行的语言 |
| 汇编语言 | 用助记符表示机器指令，地址和数据可以用符号表示 |
| 高级语言 | 如 C/C++、Java，通用性强，但最终要转成机器指令执行 |

记忆句：

```text
机器语言 CPU 直接执行
汇编语言接近硬件
高级语言接近人
```

### Cortex-M3 指令集特点

必须记住：

- Cortex-M3 只支持 Thumb-2 指令集。
- Cortex-M3 不支持传统 ARM 指令。
- Thumb-2 同时包含 16 位 Thumb 指令和 32 位扩展指令。
- Thumb-2 比普通 Thumb 更强，但 Cortex-M3 并不是完整支持所有 Thumb-2 指令。

Thumb-2 的优势：

- 16 位指令提高代码密度。
- 32 位指令提高性能和表达能力。
- 不需要 ARM / Thumb 状态切换。
- 适合嵌入式系统对代码体积和执行效率的双重要求。

## 汇编指令基本格式

通用格式：

```asm
{Label} <opcode> {S}{cond} <operand1>, <operand2> {, operand3} ; comment
```

| 部分 | 含义 |
| --- | --- |
| Label | 标号，表示地址，通常顶格写 |
| opcode | 操作码 / 助记符，如 MOV、ADD、LDR |
| S | 是否更新 APSR 条件标志位 |
| cond | 条件码，如 EQ、NE、GT、LT |
| operand | 操作数 |
| `;` | 分号后是注释 |

例子：

```asm
LOOP
    ADDS R0, R0, #1
    BNE LOOP
```

记忆：

- 标号表示位置。
- 助记符表示操作。
- 操作数表示操作对象。
- S 决定是否更新标志位。
- cond 决定当前指令是否执行。

### 标号与缩进

```asm
START
    MOV R0, #1
```

规则：

- 标号通常顶格写。
- 指令通常缩进，不能顶格当作标号混淆。
- 注释用分号 `;` 开始。
- 汇编语言通常不区分大小写，但建议统一风格。

### 操作数个数

| 操作数数量 | 示例 | 说明 |
| --- | --- | --- |
| 0 个 | `NOP` | 无操作 |
| 1 个 | `BX LR` | 跳转到 LR |
| 2 个 | `CMP R1, R2` | 比较 |
| 3 个 | `ADD R1, R2, R3` | `R1 = R2 + R3` |
| 4 个 | `MLA R1, R2, R3, R4` | `R1 = R2 * R3 + R4` |

通常第一个操作数是目的寄存器，但 `STR`、`LDM`、`STM` 等指令不完全符合这个规律。

## APSR 条件标志位

APSR 中最重要的是 `N Z C V`。

| 标志位 | 名称 | 含义 |
| --- | --- | --- |
| N | Negative | 结果为负数时置 1 |
| Z | Zero | 结果为 0 时置 1 |
| C | Carry | 加法进位 / 减法无借位 |
| V | Overflow | 有符号溢出 |

易错点：

- N 看结果最高位。
- Z = 1 表示结果为 0。
- 加法中 C = 1 表示无符号加法产生进位。
- 减法中 C = 1 表示没有借位，C = 0 表示发生借位。
- C 管无符号范围，V 管有符号范围。

例子：

```asm
CMP R0, #0
BEQ ZERO
```

`CMP R0, #0` 本质是 `R0 - 0`，如果 R0 等于 0，则 Z = 1，`BEQ` 跳转。

## 条件码

### 相等 / 不等

| 条件码 | 条件 | 含义 |
| --- | --- | --- |
| EQ | Z = 1 | Equal，相等 |
| NE | Z = 0 | Not Equal，不相等 |

```asm
CMP R0, R1
BEQ SAME
BNE DIFFERENT
```

### 无符号比较

无符号比较主要看 C 和 Z。

| 条件码 | 条件 | 含义 |
| --- | --- | --- |
| CS / HS | C = 1 | 无符号 >= |
| CC / LO | C = 0 | 无符号 < |
| HI | C = 1 且 Z = 0 | 无符号 > |
| LS | C = 0 或 Z = 1 | 无符号 <= |

记忆：

- HS：Higher or Same，高或相同。
- LO：Lower，低于。
- HI：Higher，高于。
- LS：Lower or Same，低或相同。

### 有符号比较

有符号比较主要看 N、V、Z。

| 条件码 | 条件 | 含义 |
| --- | --- | --- |
| GE | N = V | 有符号 >= |
| LT | N != V | 有符号 < |
| GT | Z = 0 且 N = V | 有符号 > |
| LE | Z = 1 或 N != V | 有符号 <= |

记忆：

- GE / LT 看 N 和 V。
- GT / LE 还要加 Z。

### 正负和溢出

| 条件码 | 条件 | 含义 |
| --- | --- | --- |
| MI | N = 1 | 结果为负 |
| PL | N = 0 | 结果非负 |
| VS | V = 1 | 溢出 |
| VC | V = 0 | 未溢出 |
| AL | 任意 | 总是执行 |

## S 后缀

带 S 后缀表示指令执行后会更新 APSR 条件标志位。

```asm
ADDS R0, R1, R2
SUBS R3, R3, #1
MOVS R0, #0
```

不带 S 通常不更新标志位：

```asm
ADD R0, R1, R2
```

循环中高频错误：

```asm
SUB R0, R0, #1
BNE LOOP
```

`SUB` 不更新 Z 标志，`BNE` 可能不能正确判断。正确写法：

```asm
SUBS R0, R0, #1
BNE LOOP
```

记忆句：

```text
后面要用条件跳转，前面运算通常要带 S。
```

## 统一汇编语言 UAL

UAL 全称 Unified Assembly Language，用于统一 Thumb-2 中 16 位和 32 位指令写法。

| 后缀 | 含义 |
| --- | --- |
| `.N` | Narrow，指定 16 位窄指令 |
| `.W` | Wide，指定 32 位宽指令 |

例子：

```asm
ADDS R0, #1
ADDS.N R0, #1
ADDS.W R0, #1
```

是否使用 16 位或 32 位指令通常可由汇编器决定，必要时用 `.N` 或 `.W` 强制指定。

## 伪指令

伪指令不是 CPU 执行的真正指令，而是给汇编器看的。

| 伪指令 | 作用 | 记忆点 |
| --- | --- | --- |
| AREA | 定义代码段 / 数据段 | 一个汇编程序至少有一个代码段 |
| ENTRY | 指定入口点 | 类似 C 语言 main |
| END | 源文件结束 | 每个汇编源文件最后需要 |
| EQU | 定义常量 | 常量名 EQU 表达式 |
| DCB | 定义字节数据 | 8 位 |
| DCW | 定义半字数据 | 16 位 |
| DCD | 定义字数据 | 32 位 |
| SPACE | 分配空间并清零 | 常用于缓冲区 |
| ALIGN | 地址对齐 | 对齐到特定边界 |
| EXPORT | 声明外部可见符号 | 供其他文件引用 |
| IMPORT | 引用外部符号 | 当前文件使用外部定义 |
| PROC / ENDP | 子程序开始 / 结束 | 成对使用 |
| INCLUDE | 包含其他文件 | 类似 C 的 include |

### AREA

```asm
AREA RESET, CODE, READONLY
AREA MyData, DATA, READWRITE
```

常见属性：

- `CODE`：代码段。
- `DATA`：数据段。
- `STACK`：栈。
- `HEAP`：堆。
- `READONLY`：只读。
- `READWRITE`：可读写。

记忆：CODE 默认 READONLY，DATA 默认 READWRITE。

### EQU 和数据定义

```asm
STACK_TOP EQU 0x20002000

DATA1 DCB 0x12
DATA2 DCW 0x1234
DATA3 DCD 0x12345678
BUF   SPACE 80
```

| 伪指令 | 单位 | 记忆 |
| --- | --- | --- |
| DCB | 8 位字节 | B = Byte |
| DCW | 16 位半字 | W 可理解为半字定义 |
| DCD | 32 位字 | D 常用于 32 位字 |
| SPACE | 字节数 | 分配空间并初始化为 0 |

### ADR 与 LDR 伪指令

```asm
ADR R1, TextMessage
LDR R0, =0x12345678
```

- `ADR` 适合小范围地址读取。
- `LDR Rd, =const` 可以把任意 32 位常数加载到寄存器中。
- MOV / MVN 放不下的大立即数，可以用 `LDR Rd, =const`。

高频易错：

```asm
LDR R0, =label   ; R0 = label 的地址值
LDR R0, label    ; R0 = memory[label]
```

记忆句：

```text
有等号取地址或常量；无等号取地址中的内容。
```

## 基本汇编程序框架

```asm
STACK_TOP EQU 0x20002000

AREA RESET, CODE, READONLY
    DCD STACK_TOP
    DCD START

ENTRY
START
    LDR R0, =0x20002000
    MOV R1, #0x5A
    STR R1, [R0]

DEADLOOP
    B DEADLOOP

END
```

必须理解：

| 语句 | 作用 |
| --- | --- |
| `STACK_TOP EQU 0x20002000` | 定义栈顶地址 |
| `AREA RESET, CODE, READONLY` | 定义只读代码段 |
| `DCD STACK_TOP` | 地址 0 处存初始 MSP |
| `DCD START` | 地址 4 处存复位向量 |
| `ENTRY` | 程序入口 |
| `START` | 起始标号 |
| `B DEADLOOP` | 程序结束后死循环 |
| `END` | 文件结束 |

特别记忆：STM32 启动时，`0x00000000` 处放初始 SP，`0x00000004` 处放复位入口地址。

## 立即数

| 格式 | 含义 |
| --- | --- |
| `#64` | 十进制立即数 |
| `#0xAF` | 十六进制立即数 |
| `#&AF` | 十六进制立即数，ARM 语法 |
| `2_1101` | 二进制数 1101 |
| `8_27` | 八进制数 27 |

例子：

```asm
ADD R1, #0xAF
ADD R1, #&AF
ADD R1, #2_1101
```

有些立即数不能直接写进普通指令。非法立即数或大常数用：

```asm
LDR R0, =0x12345678
```

## 寻址方式

寻址方式的本质：指令如何找到操作数。

| 寻址方式 | 示例 | 含义 |
| --- | --- | --- |
| 立即寻址 | `MOV R0, #64` | 操作数直接在指令中 |
| 寄存器寻址 | `ADD R0, R1, R2` | 操作数在寄存器中 |
| 寄存器间接寻址 | `LDR R0, [R1]` | R1 中存的是地址 |
| 寄存器移位寻址 | `ADD R0, R2, R3, LSL #1` | 先移位再参与运算 |
| 基址变址寻址 | `LDR R0, [R1, #4]` | 用基址加偏移访问 |
| 多寄存器寻址 | `LDMIA R0, {R1-R4}` | 连续内存与多个寄存器传送 |
| 相对寻址 | `BL NEXT` | 常以 PC 为基准 |
| 堆栈寻址 | `PUSH {R0, LR}` | 通过 SP 访问栈 |

### 中括号规则

```asm
LDR R0, [R1]    ; R0 = memory[R1]
STR R0, [R1]    ; memory[R1] = R0
```

记忆：

```text
有中括号 [ ]，说明访问内存。
```

### 基址变址寻址

偏移寻址：

```asm
LDR R0, [R1, #4]    ; R0 = memory[R1 + 4]，R1 不变
```

预索引寻址：

```asm
LDR R0, [R1, #4]!   ; R1 = R1 + 4，再访问 memory[R1]
```

后索引寻址：

```asm
LDR R0, [R1], #4    ; 先访问 memory[R1]，再 R1 = R1 + 4
```

寄存器偏移寻址：

```asm
LDR R0, [R1, R2]
LDR R0, [R1, R2, LSL #2]
```

数组访问常用：

```asm
LDR R0, [R1, R2, LSL #2]
```

若 R1 是数组首地址，R2 是下标，因为一个字是 4 字节，所以 `R2 << 2`。

### 移位方式

| 移位 | 含义 | 记忆 |
| --- | --- | --- |
| LSL | 逻辑左移 | 低位补 0 |
| ASL | 算术左移 | 与 LSL 相同 |
| LSR | 逻辑右移 | 高位补 0，适合无符号数 |
| ASR | 算术右移 | 高位补符号位，适合有符号数 |
| ROR | 循环右移 | 低位移到高位 |
| RRX | 带扩展循环右移 | C 标志参与循环 |

### 多寄存器与块拷贝

```asm
LDMIA R0, {R1-R4}
STMIA R0!, {R1-R4}
```

- LDM：Load Multiple，多个内存数据加载到多个寄存器。
- STM：Store Multiple，多个寄存器存到连续内存。
- `!` 表示最终地址写回基址寄存器。

IA / IB / DA / DB：

| 后缀 | 含义 | 地址变化 |
| --- | --- | --- |
| IA | Increment After | 传送后地址加 4 |
| IB | Increment Before | 传送前地址加 4 |
| DA | Decrement After | 传送后地址减 4 |
| DB | Decrement Before | 传送前地址减 4 |

## 堆栈

堆栈特点：

| 特点 | 说明 |
| --- | --- |
| FILO | 先进后出 |
| SP | 栈指针，指向栈顶 |
| Full Stack | SP 指向最后压入的数据 |
| Empty Stack | SP 指向下一个空位置 |
| Ascending Stack | 栈向高地址增长 |
| Descending Stack | 栈向低地址增长 |

Cortex-M3 常用满递减堆栈。

常见写法：

```asm
PUSH {R0, R4-R7, LR}
POP {R0, R4-R7, PC}
```

必须记住：PUSH / POP 必须带 `{}`。

```asm
PUSH R0      ; 错误
PUSH {R0}    ; 正确
```

## 数据传送类指令

### Load-Modify-Store 思想

必须背：

```text
ALU 不能直接操作内存数据。
内存中的变量必须先 LDR 到寄存器，运算后再 STR 回内存。
```

C 语言：

```c
x = x + 1;
```

汇编：

```asm
LDR R0, [R1]
ADD R0, R0, #1
STR R0, [R1]
```

三步：Load 读到寄存器，Modify 在寄存器中运算，Store 写回内存。

### MOV / MVN / MOVW / MOVT

| 指令 | 作用 |
| --- | --- |
| MOV | 数据传送 |
| MVN | 目的寄存器 = NOT 源操作数 |
| MOVW | 把 16 位立即数写入低 16 位，高 16 位清 0 |
| MOVT | 把 16 位立即数写入高 16 位，低 16 位不变 |

例子：

```asm
MOVW R3, #0x5678
MOVT R3, #0x1234
```

结果：

```text
R3 = 0x12345678
```

记忆：MOVW 写低半字，MOVT 写高半字。

### LDR / STR

| 指令 | 方向 | 含义 |
| --- | --- | --- |
| LDR | 内存 -> 寄存器 | Load |
| STR | 寄存器 -> 内存 | Store |

```asm
LDR R0, [R1]    ; R0 = memory[R1]
STR R0, [R1]    ; memory[R1] = R0
```

数据宽度：

| 指令 | 功能 | 对应 C 类型 |
| --- | --- | --- |
| LDR | 读 32 位字 | int / uint32_t |
| LDRB | 读 8 位字节，零扩展 | uint8_t |
| LDRH | 读 16 位半字，零扩展 | uint16_t |
| LDRSB | 读 8 位字节，符号扩展 | int8_t |
| LDRSH | 读 16 位半字，符号扩展 | int16_t |
| STR | 存 32 位字 | int / uint32_t |
| STRB | 存低 8 位 | byte |
| STRH | 存低 16 位 | halfword |
| LDRD | 读双字，64 位 | 两个寄存器 |
| STRD | 存双字，64 位 | 两个寄存器 |

记忆：B = Byte，H = Halfword，无后缀 = Word，SB / SH = Signed Byte / Signed Halfword。

### MRS / MSR

| 指令 | 方向 |
| --- | --- |
| MRS | 特殊寄存器 -> 通用寄存器 |
| MSR | 通用寄存器 -> 特殊寄存器 |

```asm
MRS R1, APSR
MSR APSR, R0
```

记忆：MRS 读到普通寄存器，MSR 写到特殊寄存器。

## 数据处理类指令

### 算术运算

| 指令 | 含义 |
| --- | --- |
| ADD | `Rd = Rn + Op2` |
| ADC | `Rd = Rn + Op2 + C` |
| SUB | `Rd = Rn - Op2` |
| SBC | `Rd = Rn - Op2 - NOT(C)` |
| RSB | `Rd = Op2 - Rn` |
| MUL | `Rd = Rn * Rm` |
| MLA | `Rd = Rn * Rm + Ra` |
| MLS | `Rd = Ra - Rn * Rm` |
| SMULL | 有符号 32 x 32 得 64 位 |
| UMULL | 无符号 32 x 32 得 64 位 |
| SDIV | 有符号除法 |
| UDIV | 无符号除法 |

注意：

- 乘法操作数必须是寄存器，不能直接用立即数。
- `ADC` 常用于多字长加法。
- ARM 没有单独 borrow 标志，减法借位通过 C 标志反映。
- `SDIV` / `UDIV` 只保存商，余数丢弃。

### 逻辑运算

| 指令 | 含义 | 常见用途 |
| --- | --- | --- |
| AND | `Rd = Rn AND Op2` | 保留某些位 |
| ORR | `Rd = Rn OR Op2` | 置位 |
| ORN | `Rd = Rn OR NOT(Op2)` | 或非 |
| EOR | `Rd = Rn XOR Op2` | 翻转位 |
| BIC | `Rd = Rn AND NOT(Op2)` | 清零某些位 |
| NEG | `Rd = ~Rm + 1` | 取负数 |

例子：

```asm
AND R0, R0, #3       ; 保留 bit0、bit1
BIC R0, R0, #0x0B    ; 清除 bit0、bit1、bit3
```

记忆：BIC = Bit Clear，掩码中为 1 的位被清除。

### 比较与测试

| 指令 | 本质 | 作用 |
| --- | --- | --- |
| CMP | `Rn - Op2` | 比较，不保存结果，只更新标志位 |
| CMN | `Rn + Op2` | 负向比较，不保存结果，只更新标志位 |
| TST | `Rn AND Op2` | 测试某些位是否为 1 |
| TEQ | `Rn XOR Op2` | 测试两个数按位是否相等 |

例子：

```asm
TST R1, #1
```

如果 R1 最低位为 0，则结果为 0，Z = 1。如果最低位为 1，则结果不为 0，Z = 0。

## 数据翻转、扩展和位段处理

### 数据翻转

| 指令 | 作用 |
| --- | --- |
| REV | 反转 32 位字中的 4 个字节顺序 |
| REV16 | 分别交换高半字和低半字内部的两个字节 |
| REVSH | 反转低半字字节序，并进行符号扩展 |
| RBIT | 将 32 位数据按位完全反转 |

例子：

```text
REV:   0x12345678 -> 0x78563412
REV16: 0x12345678 -> 0x34127856
```

### 数据扩展

| 指令 | 含义 |
| --- | --- |
| SXTB | 8 位符号扩展到 32 位 |
| SXTH | 16 位符号扩展到 32 位 |
| UXTB | 8 位零扩展到 32 位 |
| UXTH | 16 位零扩展到 32 位 |

记忆：

- S = Signed，有符号扩展。
- U = Unsigned，无符号扩展。
- B = Byte，8 位。
- H = Halfword，16 位。

若低 8 位为 `0x80`：

```text
SXTB -> 0xFFFFFF80
UXTB -> 0x00000080
```

### 位段处理

| 指令 | 作用 |
| --- | --- |
| CLZ | 计算从最高位开始连续 0 的个数 |
| BFC | 将指定连续位段清零 |
| BFI | 将 Rn 的低 width 位插入 Rd 的指定位段 |
| SBFX | 有符号位段提取，提取后符号扩展 |
| UBFX | 无符号位段提取，提取后零扩展 |

格式：

```asm
BFC.W Rd, #lsb, #width
BFI Rd, Rn, #lsb, #width
SBFX Rd, Rn, #lsb, #width
UBFX Rd, Rn, #lsb, #width
```

参数：

- `lsb`：位段最低位位置。
- `width`：位段宽度。

## 跳转指令

| 指令 | 作用 |
| --- | --- |
| B | 普通跳转 |
| BL | 带返回地址的跳转，子程序调用 |
| BX | 跳转到寄存器给出的地址，常用于返回 |
| BLX | 带返回地址的寄存器跳转 |
| CBZ | Rn 为 0 则跳转 |
| CBNZ | Rn 非 0 则跳转 |
| TBB | 字节查表跳转 |
| TBH | 半字查表跳转 |
| IT | If-Then 条件执行块 |

### B / BL / BX / BLX

```asm
B LOOP
BEQ EQUAL
BNE NOTEQUAL
```

`B label` 表示 `PC = label`。

```asm
BL FUNC
...
FUNC
    BX LR
```

`BL` 用于调用子程序，LR 保存返回地址。`BX LR` 用于从子程序返回。

### CBZ / CBNZ

```asm
CBZ Rn, label
CBNZ Rn, label
```

| 指令 | 含义 |
| --- | --- |
| CBZ | Rn == 0 则跳转 |
| CBNZ | Rn != 0 则跳转 |

特点：

- 不改变条件标志位。
- 适合 while / 循环出口判断。
- 近似等价于 `CMP Rn, #0` 加 `BEQ` / `BNE`，但不破坏 APSR。

### IT 指令

IT = If-Then，允许最多 4 条后续指令条件执行。

```asm
CMP R0, R1
ITTEE EQ
ADDEQ R3, R4, R5
LSLEQ R3, R3, #1
ADDNE R3, R6, R7
ASRNE R3, R3, #1
```

记忆：

- IT 后面的指令必须带条件后缀。
- T 表示 Then，用同条件。
- E 表示 Else，用反条件。
- IT 块最多控制 4 条指令。

## 异常与其他指令

| 指令 | 含义 |
| --- | --- |
| SVC | 产生系统调用异常 |
| BKPT | 断点中断 |
| CPSID i | 关闭中断 |
| CPSIE i | 开启中断 |
| SEV | 发送事件 |
| WFE | 等待事件 |
| WFI | 等待中断 |
| NOP | 无操作，常用于延时 |

记忆：

```asm
CPSID i   ; Disable Interrupt
CPSIE i   ; Enable Interrupt
```

## 程序设计模板

### 分支程序

C 语言：

```c
if (R5 == 10)
    R1 = R5;
else
    R0 = R5;
```

汇编：

```asm
CMP R5, #10
BEQ EQUAL
MOV R0, R5
B ENDIF
EQUAL
MOV R1, R5
ENDIF
```

或条件执行：

```asm
CMP R5, #10
MOVEQ R1, R5
MOVNE R0, R5
```

记忆：分支 = CMP + 条件跳转 / 条件执行。

### 多分支程序

判断 R1 是否为 10、15、20、25。若是，R0 = 0x5A；否则 R0 = 0xFF。

```asm
CMP R1, #10
BEQ HIT
CMP R1, #15
BEQ HIT
CMP R1, #20
BEQ HIT
CMP R1, #25
BEQ HIT
MOV R0, #0xFF
B DONE
HIT
MOV R0, #0x5A
DONE
```

### 跳转表

```asm
MOV R0, N
ADR R5, JPTAB
LDR PC, [R5, R0, LSL #2]
JPTAB
DCD FUNC0
DCD FUNC1
DCD FUNC2
```

记忆：跳转表中每个地址占 4 字节，所以索引要 `LSL #2`。

### 循环程序

```asm
MOV R0, #COUNT
LOOP
    ; loop body
    SUBS R0, R0, #1
    BNE LOOP
```

必须记：`SUBS` 更新 Z 标志，`BNE` 判断是否继续循环。

### 数据块复制

复制 80 字节，从 `0x20000000` 到 `0x20000100`。一次复制 4 个字，也就是 16 字节，需要循环 5 次。

```asm
LDR R0, =0x20000000
LDR R1, =0x20000100
MOV R10, #5
LOOP
    LDMIA R0!, {R2-R5}
    STMIA R1!, {R2-R5}
    SUBS R10, R10, #1
    BNE LOOP
```

记忆：

- 源地址用 LDMIA。
- 目标地址用 STMIA。
- `!` 让地址自动后移。
- `SUBS + BNE` 控制循环。

### 子程序

基础模板：

```asm
MAIN
    BL FUNC
    B END
FUNC
    ; subroutine body
    BX LR
END
    B END
```

保护返回地址：

```asm
FUNC
    PUSH {LR}
    ; subroutine body
    POP {PC}
```

保护多个寄存器：

```asm
FUNC
    PUSH {R4-R7, LR}
    ; subroutine body
    POP {R4-R7, PC}
```

记忆：调用用 `BL`，返回用 `BX LR` 或 `POP {PC}`。

### 求 1 到 100 的和

递增计数：

```asm
MOV R0, #0      ; sum
MOV R1, #1      ; i
LOOP
    ADD R0, R0, R1
    ADD R1, R1, #1
    CMP R1, #101
    BNE LOOP
```

递减计数：

```asm
MOV R0, #0      ; sum
MOV R1, #100    ; i
LOOP
    ADD R0, R0, R1
    SUBS R1, R1, #1
    BNE LOOP
```

## 与项目的联系

- STM32 启动文件中的向量表、栈顶、Reset_Handler 与本章的 `DCD`、`ENTRY`、`B`、`BL`、`BX LR` 密切相关。
- 调试 HardFault、异常返回、栈溢出时，需要理解 LR、PC、SP、PUSH / POP 和异常入口。
- 外设寄存器配置本质上是 Load-Modify-Store：读寄存器、改位、写回寄存器。
- GPIO、USART、Timer、DMA 的底层驱动都离不开位操作、掩码、移位、`AND`、`ORR`、`BIC` 等指令思想。
- 优化数据复制、数组遍历、缓冲区处理时，`LDM` / `STM`、索引寻址和循环模板非常有用。

## 高频记忆清单

- Cortex-M3 只支持 Thumb-2，不支持传统 ARM 指令。
- Thumb-2 同时包含 16 位和 32 位指令。
- 指令格式：`{Label} opcode {S}{cond} operand1, operand2, operand3 ; comment`。
- N 表示负数，Z 表示零，C 表示进位 / 无借位，V 表示有符号溢出。
- EQ：Z = 1，NE：Z = 0。
- CS / HS：C = 1，无符号 >=。
- CC / LO：C = 0，无符号 <。
- GE：N = V，有符号 >=。
- LT：N != V，有符号 <。
- GT：Z = 0 且 N = V，有符号 >。
- LE：Z = 1 或 N != V，有符号 <=。
- 后面要用条件跳转，前面运算通常要带 S。
- `LDR` 是内存到寄存器，`STR` 是寄存器到内存。
- 内存数据不能直接进 ALU，必须先加载到寄存器。
- `LDR R0, =label` 是取地址，`LDR R0, label` 是取地址中的内容。
- 有中括号 `[]` 表示访问内存。
- `PUSH {}` 和 `POP {}` 必须带花括号。
- Cortex-M3 常用满递减堆栈。
- `BL` 调用子程序，LR 保存返回地址。
- `BX LR` 返回，`POP {PC}` 也可返回。
- 循环常用 `SUBS Rn, Rn, #1` 和 `BNE LOOP`。
- 大立即数或非法立即数用 `LDR Rd, =const`。
- `BIC` 用于清除掩码中为 1 的位。
- `TST` 常用于测试某一位是否为 1。
- 数据块复制常用 `LDMIA` 和 `STMIA`。
