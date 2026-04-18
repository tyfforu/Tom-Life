---
source: https://blog.csdn.net/u010249597/article/details/134952613
author: Print World
date: 2023-12-12
created: 2026-04-18
tags:
  - STM32
  - GPIO
  - API
  - 嵌入式
  - 学习笔记
---

# STM32学习笔记——GPIO相关API概述

> 来源：CSDN博客 @Print World
> 
> 原文阅读量：1.8万 | 点赞：70 | 收藏：106

---

## 一、数据类型定义

### 1. GPIOSpeed_TypeDef - 输出频率选择

```c
typedef enum
{
    GPIO_Speed_10MHz = 1,  // 10MHz
    GPIO_Speed_2MHz,       // 2MHz
    GPIO_Speed_50MHz       // 50MHz
} GPIOSpeed_TypeDef;
```

### 2. GPIOMode_TypeDef - 8种工作模式

| 宏定义 | 值 | 模式 |
|--------|-----|------|
| `GPIO_Mode_AIN` | 0x00 | 模拟输入 |
| `GPIO_Mode_IN_FLOATING` | 0x04 | 浮空输入 |
| `GPIO_Mode_IPD` | 0x28 | 下拉输入 |
| `GPIO_Mode_IPU` | 0x48 | 上拉输入 |
| `GPIO_Mode_Out_OD` | 0x14 | 开漏输出 |
| `GPIO_Mode_Out_PP` | 0x10 | 推挽输出 |
| `GPIO_Mode_AF_OD` | 0x1C | 复用功能开漏输出 |
| `GPIO_Mode_AF_PP` | 0x18 | 复用功能推挽输出 |

### 3. GPIO_InitTypeDef - 初始化结构体

```c
typedef struct
{
    uint16_t GPIO_Pin;        // 引脚选择
    GPIOSpeed_TypeDef GPIO_Speed;  // 速度配置
    GPIOMode_TypeDef GPIO_Mode;    // 模式配置
} GPIO_InitTypeDef;
```

### 4. BitAction - 位操作枚举

```c
typedef enum
{
    Bit_RESET = 0,  // 清零/低电平
    Bit_SET         // 置1/高电平
} BitAction;
```

---

## 二、引脚和端口定义

### GPIO引脚定义

```c
#define GPIO_Pin_0      ((uint16_t)0x0001)   // Pin 0
#define GPIO_Pin_1      ((uint16_t)0x0002)   // Pin 1
// ... Pin_2 ~ Pin_14
#define GPIO_Pin_15     ((uint16_t)0x8000)   // Pin 15
#define GPIO_Pin_All    ((uint16_t)0xFFFF)   // 所有引脚
```

### 端口源定义

```c
#define GPIO_PortSourceGPIOA    ((uint8_t)0x00)
#define GPIO_PortSourceGPIOB    ((uint8_t)0x01)
// ... GPIO_PortSourceGPIOG
```

---

## 三、GPIO相关函数汇总（24个）

### 初始化函数

| 函数 | 功能 |
|------|------|
| `GPIO_DeInit(GPIO_TypeDef* GPIOx)` | 重置GPIO寄存器为默认值 |
| `GPIO_AFIODeInit(void)` | 重置复用功能寄存器 |
| `GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct)` | 初始化GPIO外设 |
| `GPIO_StructInit(GPIO_InitTypeDef* GPIO_InitStruct)` | 初始化结构体为默认值 |

### 输入读取函数

| 函数 | 功能 |
|------|------|
| `GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)` | 读取单个引脚输入 |
| `GPIO_ReadInputData(GPIO_TypeDef* GPIOx)` | 读取整个端口输入（16位） |

### 输出读取函数

| 函数 | 功能 |
|------|------|
| `GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)` | 读取单个引脚输出 |
| `GPIO_ReadOutputData(GPIO_TypeDef* GPIOx)` | 读取整个端口输出（16位） |

### 输出控制函数

| 函数 | 功能 |
|------|------|
| `GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)` | 设置引脚为高电平 |
| `GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)` | 设置引脚为低电平 |
| `GPIO_WriteBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, BitAction BitVal)` | 写入单个引脚状态 |
| `GPIO_Write(GPIO_TypeDef* GPIOx, uint16_t PortVal)` | 写入整个端口值（16位） |

### 时钟控制函数

| 函数 | 功能 |
|------|------|
| `RCC_APB2PeriphClockCmd(uint32_t RCC_APB2Periph, FunctionalState NewState)` | 使能/失能APB2外设时钟 |

---

## 四、常用代码模板

### GPIO初始化模板

```c
// 1. 开启时钟
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);

// 2. 定义初始化结构体
GPIO_InitTypeDef GPIO_InitStruct;

// 3. 配置参数
GPIO_InitStruct.GPIO_Pin = GPIO_Pin_0;           // Pin_0
GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;    // 推挽输出
GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;   // 50MHz

// 4. 初始化
GPIO_Init(GPIOA, &GPIO_InitStruct);
```

### LED闪烁模板

```c
while(1)
{
    GPIO_SetBits(GPIOA, GPIO_Pin_0);      // 高电平
    Delay_ms(500);
    GPIO_ResetBits(GPIOA, GPIO_Pin_0);    // 低电平
    Delay_ms(500);
}
```

---

## 五、速查卡片

```
【GPIO操作四步走】
1. 开时钟：RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOx, ENABLE)
2. 填结构体：GPIO_InitTypeDef 变量
3. 配参数：Pin / Mode / Speed
4. 初始化：GPIO_Init(GPIOx, &变量)

【输出控制三方式】
- SetBits/ResetBits：设置/清除（推荐）
- WriteBit：写入指定值
- Write：写入整个端口（16位）

【8种模式速记】
输入：AIN(模拟)、IN_FLOATING(浮空)、IPD(下拉)、IPU(上拉)
输出：Out_OD(开漏)、Out_PP(推挽)、AF_OD(复用开漏)、AF_PP(复用推挽)
```

---

## 相关链接

- 原文链接：https://blog.csdn.net/u010249597/article/details/134952613
- 作者：Print World
- 系列：江科大STM32学习笔记

---

## 我的思考

> （此处填写学习心得）

---
*整理于 2026-04-18*
