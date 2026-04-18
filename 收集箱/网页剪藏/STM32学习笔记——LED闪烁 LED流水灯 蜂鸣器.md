---
source: https://blog.csdn.net/u010249597/article/details/134975834
author: Print World
date: 2023-12-13
created: 2026-04-18
tags:
  - STM32
  - GPIO
  - LED
  - 蜂鸣器
  - 嵌入式
---

# STM32学习笔记——LED闪烁、LED流水灯、蜂鸣器

> 来源：CSDN博客 @Print World
> 
> 原文阅读量：1.8万 | 点赞：48 | 收藏：82

---

## 一、LED闪烁程序

### 硬件接线
- LED正极 → 3.3V（通过限流电阻）
- LED负极 → PA0

### 代码示例

```c
#include "stm32f10x.h"
#include "delay.h"

int main(void)
{	
    GPIO_InitTypeDef GPIO_InitStruct;
    
    // 1. 使能GPIOA时钟
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
    
    // 2. GPIO初始化配置
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;    // 推挽输出
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_0;           // PA0
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_10MHz;   // 10MHz
    
    GPIO_Init(GPIOA, &GPIO_InitStruct);
    
    while(1)
    {
        // 低电平点亮LED
        GPIO_WriteBit(GPIOA, GPIO_Pin_0, (BitAction)0);
        delay_ms(100);

        // 高电平熄灭LED
        GPIO_WriteBit(GPIOA, GPIO_Pin_0, (BitAction)1);
        delay_ms(100);
    }
    
    return 0;
}
```

---

## 二、LED流水灯程序

### 硬件接线
- 8个LED分别接 PA0 ~ PA7

### 代码示例

```c
#include "stm32f10x.h"
#include "delay.h"

int main(void)
{	
    GPIO_InitTypeDef GPIO_InitStruct;
    
    // 使能GPIOA时钟
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
    
    // 初始化所有引脚（PA0~PA15）
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_All;         // 所有引脚
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_10MHz;
    
    GPIO_Init(GPIOA, &GPIO_InitStruct);
    
    while(1)
    {
        // 依次点亮每个LED（低电平点亮，所以取反）
        GPIO_Write(GPIOA, ~0x0001);  // 点亮PA0
        delay_ms(100);
        
        GPIO_Write(GPIOA, ~0x0002);  // 点亮PA1
        delay_ms(100);
        
        GPIO_Write(GPIOA, ~0x0004);  // 点亮PA2
        delay_ms(100);
        
        GPIO_Write(GPIOA, ~0x0008);  // 点亮PA3
        delay_ms(100);
        
        GPIO_Write(GPIOA, ~0x0010);  // 点亮PA4
        delay_ms(100);
        
        GPIO_Write(GPIOA, ~0x0020);  // 点亮PA5
        delay_ms(100);
        
        GPIO_Write(GPIOA, ~0x0040);  // 点亮PA6
        delay_ms(100);
        
        GPIO_Write(GPIOA, ~0x0080);  // 点亮PA7
        delay_ms(100);
    }
    
    return 0;
}
```

### 要点说明

| 操作 | 说明 |
|------|------|
| `GPIO_Write(GPIOA, ~0x0001)` | 向整个GPIOA端口写入数据，~0x0001 = 0xFFFE，只有PA0为0（低电平） |
| `GPIO_Pin_All` | 初始化所有16个引脚 |

---

## 三、蜂鸣器程序

### 硬件接线
- 蜂鸣器正极 → PB12
- 蜂鸣器负极 → GND

### 代码示例

```c
#include "stm32f10x.h"
#include "delay.h"

int main(void)
{	
    GPIO_InitTypeDef GPIO_InitStruct;
    
    // 使能GPIOB时钟
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
    
    // 初始化PB12
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_12;
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_10MHz;
    
    GPIO_Init(GPIOB, &GPIO_InitStruct);
    
    while(1)
    {
        // 短-短-长-停 的节奏
        GPIO_SetBits(GPIOB, GPIO_Pin_12);      // 高电平
        delay_ms(100);
        
        GPIO_ResetBits(GPIOB, GPIO_Pin_12);    // 低电平
        delay_ms(100);
        
        GPIO_SetBits(GPIOB, GPIO_Pin_12);
        delay_ms(100);
        
        GPIO_ResetBits(GPIOB, GPIO_Pin_12);
        delay_ms(700);                          // 长停顿
    }
    
    return 0;
}
```

---

## 四、核心要点总结

### GPIO输出控制三种方式

| 方式 | 函数 | 用途 |
|------|------|------|
| 位写入 | `GPIO_WriteBit()` | 控制单个引脚 |
| 位置位/复位 | `GPIO_SetBits()` / `GPIO_ResetBits()` | 设置高/低电平 |
| 端口写入 | `GPIO_Write()` | 控制整个端口16个引脚 |

### 编程步骤

```
1. 使能GPIO时钟 (RCC_APB2PeriphClockCmd)
2. 定义初始化结构体 (GPIO_InitTypeDef)
3. 配置参数 (Mode / Pin / Speed)
4. 初始化GPIO (GPIO_Init)
5. 循环控制输出
```

---

## 相关资源

- 原文链接：https://blog.csdn.net/u010249597/article/details/134975834
- 系列汇总：https://blog.csdn.net/u010249597/article/details/134762513
- 所属专栏：[STM32F103](https://blog.csdn.net/u010249597/category_12516232.html)

---

## 我的思考

> （此处填写学习心得或实验记录）

---
*整理于 2026-04-18*
