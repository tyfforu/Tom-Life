---
source: https://blog.csdn.net/u010249597/article/details/135019005
author: Print World
date: 2026-04-18
tags:
  - STM32
  - OLED
  - 调试
  - I2C
  - SPI
  - 嵌入式
---

# STM32学习笔记——OLED调试工具（09）

> 来源：CSDN @Print World

---

## 一、STM32调试方式

| 调试方式 | 原理 | 特点 |
|---------|------|------|
| **串口调试** | 通过串口通信发送调试信息到电脑 | 电脑用串口助手显示，信息量大 |
| **显示屏调试** | 显示屏直接连接单片机 | 信息直接显示在屏幕上，方便观察 |
| **Keil调试模式** | 借助Keil软件的调试功能 | 单步运行、设置断点、查看寄存器及变量 |

---

## 二、OLED简介

### 2.1 什么是OLED？
**OLED**（Organic Light Emitting Diode）：有机发光二极管

**特点**：
- ✅ 自发光，不需背光源
- ✅ 对比度高、厚度薄
- ✅ 视角广、反应速度快
- ✅ 可用于挠曲性面板
- ✅ 使用温度范围广

**与LCD对比**：
- LCD需要背光，OLED不需要
- 同样显示效果，OLED更好
- OLED尺寸难大型化，但分辨率可做很高

### 2.2 0.96寸OLED显示屏特点

| 参数 | 说明 |
|------|------|
| 颜色 | 黄蓝、白、蓝三种可选 |
| 分辨率 | 128×64 |
| 接口 | 6800/8080并行、3线/4线SPI、I2C（共5种）|
| 驱动IC | SSD1306 |

**颜色说明**：
- **黄蓝**：屏上1/4黄光，下3/4蓝光（固定区域，不可修改）
- **白光**：黑底白字
- **蓝光**：黑底蓝字

---

## 三、OLED模块接口

### 3.1 SPI接口（7针）

| 引脚 | 名称 | 功能 |
|------|------|------|
| 1 | GND | 电源地 |
| 2 | VCC | 电源正（3~5.5V）|
| 3 | D0 | 时钟管脚（SCL）|
| 4 | D1 | 数据管脚（SDA/MOSI）|
| 5 | RES | 复位（低电平复位）|
| 6 | DC | 数据/命令控制管脚 |
| 7 | CS | 片选管脚 |

### 3.2 I2C接口（4针）

| 引脚 | 名称 | 功能 |
|------|------|------|
| 1 | GND | 电源地 |
| 2 | VCC | 电源正（3~5.5V）|
| 3 | SCL | 时钟管脚（D0）|
| 4 | SDA | 数据管脚（D1）|

> 💡 I2C只需要2根线即可控制OLED，接线更简单

---

## 四、SSD1306驱动IC

### 4.1 基本参数
- **内部升压**：不需要额外设计升压电路
- **显存结构**：每页128字节，共8页 = 128×64点阵

### 4.2 与SSD1106的区别
| 参数 | SSD1306（0.96寸）| SSD1106（1.3寸）|
|------|-----------------|-----------------|
| 每页字节数 | 128 | 132 |
| 页数 | 8 | 8 |
| 移植注意 | 1.3寸屏需地址偏移2 | - |

> ⚠️ 用0.96寸程序移植到1.3寸时，1.3寸屏右边会有4个像素点显示不正常

---

## 五、OLED驱动函数

### 5.1 常用函数列表

| 函数 | 功能 |
|------|------|
| `OLED_Init()` | OLED初始化 |
| `OLED_Clear()` | 清屏 |
| `OLED_ShowChar()` | 显示一个字符 |
| `OLED_ShowString()` | 显示字符串 |
| `OLED_ShowNum()` | 显示十进制数字 |
| `OLED_ShowSignedNum()` | 显示有符号数字 |
| `OLED_ShowHexNum()` | 显示十六进制数字 |
| `OLED_ShowBinNum()` | 显示二进制数字 |
| `OLED_SetCursor()` | 设置光标位置 |

### 5.2 函数参数说明

```c
// 显示字符
void OLED_ShowChar(uint8_t Line, uint8_t Column, char Char);
// Line: 行位置（1~4）
// Column: 列位置（1~16）
// Char: ASCII可见字符

// 显示字符串
void OLED_ShowString(uint8_t Line, uint8_t Column, char *String);

// 显示数字
void OLED_ShowNum(uint8_t Line, uint8_t Column, uint32_t Number, uint8_t Length);
// Number: 要显示的数字
// Length: 显示长度（1~10）
```

---

## 六、I2C通信协议

### 6.1 I2C时序
```
起始信号：SCL高电平时，SDA从高变低
停止信号：SCL高电平时，SDA从低变高
数据传输：SCL低电平时改变SDA，SCL高电平时读取SDA
```

### 6.2 OLED I2C地址
- **从机地址**：0x78（写模式）
- **写命令**：0x00
- **写数据**：0x40

---

## 七、OLED配置步骤

```c
// 1. 引脚配置（PB8-SCL, PB9-SDA）
#define OLED_W_SCL(x)  GPIO_WriteBit(GPIOB, GPIO_Pin_8, (BitAction)(x))
#define OLED_W_SDA(x)  GPIO_WriteBit(GPIOB, GPIO_Pin_9, (BitAction)(x))

// 2. I2C引脚初始化
void OLED_I2C_Init(void)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
    
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD;  // 开漏输出
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_8 | GPIO_Pin_9;
    GPIO_Init(GPIOB, &GPIO_InitStructure);
    
    OLED_W_SCL(1);
    OLED_W_SDA(1);
}

// 3. OLED初始化
void OLED_Init(void)
{
    OLED_I2C_Init();
    // 发送一系列初始化命令...
    OLED_Clear();
}
```

---

## 八、显示示例

```c
#include "stm32f10x.h"
#include "delay.h"
#include "oled.h"

int main(void)
{    
    OLED_Init();
    
    OLED_ShowChar(1, 1, 'A');           // 第1行第1列显示'A'
    OLED_ShowString(1, 3, "Hello!");    // 第1行第3列显示字符串
    OLED_ShowNum(2, 1, 12345, 5);       // 第2行显示数字12345
    OLED_ShowSignedNum(2, 7, -66, 2);   // 显示有符号数-66
    OLED_ShowHexNum(3, 1, 0xAA55, 4);   // 显示十六进制0xAA55
    OLED_ShowBinNum(4, 1, 0xAA55, 16);  // 显示二进制
    
    while(1);
}
```

---

## 九、知识点速查

| 概念 | 说明 |
|------|------|
| OLED | 有机发光二极管，自发光 |
| SSD1306 | 0.96寸OLED常用驱动IC |
| I2C | 两线串行通信协议（SCL+SDA）|
| SPI | 四线串行通信协议（SCK+MOSI+MISO+CS）|
| 显存 | 128×64像素，分8页，每页128字节 |
| 从机地址 | 0x78（OLED的I2C地址）|

---

## 参考链接

- [江科大STM32学习笔记汇总](https://blog.csdn.net/u010249597/article/details/134762513)
- 下一篇：[OLED显示屏(10)](https://blog.csdn.net/u010249597/article/details/135033843)
