---
source: https://blog.csdn.net/u010249597/article/details/134999823
author: Print World
date: 2026-04-18
tags:
  - STM32
  - GPIO
  - C语言
  - 嵌入式
---

# STM32学习笔记——GPIO输入与C语言基础（07）

> 来源：CSDN @Print World

---

## 一、按键简介

### 1.1 按键基本概念
- **按键**：常见的输入设备，按下导通，松手断开

### 1.2 按键抖动问题
- **抖动原因**：按键内部使用机械式弹簧片进行通断
- **抖动现象**：按下和松手瞬间伴随一连串抖动（约5-10ms）
- **解决方法**：
  - 硬件消抖：并联电容滤波
  - 软件消抖：延时10-20ms后再次检测

---

## 二、传感器模块简介

### 2.1 工作原理
传感器元件（光敏电阻/热敏电阻/红外接收管等）的电阻随外界模拟量变化：

```
外界模拟量 → 电阻变化 → 分压得到模拟电压 → 电压比较器二值化 → 数字电压输出
```

### 2.2 信号转换流程
1. **传感器元件**：电阻随外界模拟量变化
2. **分压电路**：与定值电阻分压得到模拟电压
3. **电压比较器**：进行二值化得到数字电压输出（0或1）

---

## 三、光敏电阻传感器

**基本原理**：
- 光线越强，光敏电阻阻值越小
- 通过分压电路将光强转换为电压信号
- 可用于自动灯光控制、光线检测等场景

---

## 四、C语言基础

### 4.1 宏定义 #define

**关键字**：`#define`

**用途**：
- 用一个字符串代替一个数字，便于理解，防止出错
- 提取程序中经常出现的参数，便于快速修改

**定义宏定义**：
```c
#define ABC 12345
#define LED_PIN  GPIO_Pin_13
```

**引用宏定义**：
```c
int a = ABC;        // 等效于 int a = 12345;
```

---

### 4.2 typedef 类型别名

**关键字**：`typedef`

**用途**：将一个比较长的变量类型名换个名字，便于使用

**定义 typedef**：
```c
typedef unsigned char uint8_t;
typedef unsigned short uint16_t;
typedef unsigned int   uint32_t;
```

**引用 typedef**：
```c
uint8_t a;   // 等效于 unsigned char a;
uint16_t b;  // 等效于 unsigned short b;
```

---

### 4.3 结构体 struct

**关键字**：`struct`

**用途**：数据打包，不同类型变量的集合

**定义结构体变量**：
```c
struct {
    char x;
    int y;
    float z;
} StructName;
```

> 💡 因为结构体变量类型较长，通常用 typedef 更改变量类型名

**引用结构体成员**：
```c
// 方式1：结构体变量名.成员名
StructName.x = 'A';
StructName.y = 66;
StructName.z = 1.23;

// 方式2：结构体指针->成员名
pStructName->x = 'A';  // pStructName 为结构体的地址
pStructName->y = 66;
pStructName->z = 1.23;
```

**STM32 中的典型应用**：
```c
// GPIO初始化结构体
typedef struct
{
    uint16_t GPIO_Pin;
    GPIOSpeed_TypeDef GPIO_Speed;
    GPIOMode_TypeDef GPIO_Mode;
} GPIO_InitTypeDef;

// 使用
GPIO_InitTypeDef GPIO_InitStruct;
GPIO_InitStruct.GPIO_Pin = GPIO_Pin_0;
GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_Init(GPIOA, &GPIO_InitStruct);
```

---

### 4.4 枚举 enum

**关键字**：`enum`

**用途**：
- 定义一个取值受限制的整型变量，用于限制变量取值范围
- 宏定义的集合

**定义枚举变量**：
```c
enum {
    FALSE = 0,
    TRUE = 1
} EnumName;
```

> 💡 因为枚举变量类型较长，通常用 typedef 更改变量类型名

**引用枚举成员**：
```c
EnumName = FALSE;
EnumName = TRUE;
```

**STM32 中的典型应用**：
```c
// GPIO 状态枚举
typedef enum {
    Bit_RESET = 0,  // 低电平
    Bit_SET   = 1   // 高电平
} BitAction;

// 使用
GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_SET);    // 输出高电平
GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_RESET);  // 输出低电平
```

---

## 五、知识点速查表

| 概念 | 关键字 | 用途 | 示例 |
|------|--------|------|------|
| 宏定义 | `#define` | 字符串代替数字 | `#define LED_PIN 13` |
| 类型别名 | `typedef` | 简化长类型名 | `typedef unsigned char uint8_t;` |
| 结构体 | `struct` | 不同类型变量集合 | `struct {char x; int y;} s;` |
| 枚举 | `enum` | 限制变量取值范围 | `enum {OFF=0, ON=1} state;` |

---

## 六、关键要点总结

1. **按键消抖**：机械按键必须处理抖动问题，软件延时是最简单的方案

2. **传感器原理**：模拟量 → 电阻变化 → 电压变化 → 数字量

3. **C语言技巧**：
   - 用 `#define` 提高代码可读性和可维护性
   - 用 `typedef` 简化复杂类型名
   - 用 `struct` 打包相关数据
   - 用 `enum` 限制变量取值，提高代码安全性

---

## 参考链接

- [江科大STM32学习笔记汇总](https://blog.csdn.net/u010249597/article/details/134762513)
- 下一篇：[按键控制LED 光敏传感器控制蜂鸣器(08)](https://blog.csdn.net/u010249597/article/details/135009654)
