---
source: https://blog.csdn.net/u010249597/article/details/135009654
author: Print World
date: 2026-04-18
tags:
  - STM32
  - GPIO
  - 按键
  - 传感器
  - 嵌入式
---

# STM32学习笔记——按键控制LED与光敏传感器（08）

> 来源：CSDN @Print World

---

## 一、按键控制LED

### 1.1 硬件接线
- LED 接 GPIOA Pin1、Pin2
- 按键接 GPIOB Pin1、Pin11

### 1.2 LED驱动代码

**led.h**
```c
#ifndef __LED_H__
#define __LED_H__

void led_init(void);
void led1_on(void);
void led1_off(void);
void led1_turn(void);
void led2_on(void);
void led2_off(void);
void led2_turn(void);

#endif
```

**led.c**
```c
#include "led.h"
#include "stm32f10x.h"

// LED初始化
void led_init(void)
{
    GPIO_InitTypeDef GPIO_InitStruct;
    
    // 使能时钟
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
    
    // A1、A2 推挽输出
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_1 | GPIO_Pin_2;
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOA, &GPIO_InitStruct);
    
    // 初始化为高电平（LED灭）
    GPIO_SetBits(GPIOA, GPIO_Pin_1);
    GPIO_SetBits(GPIOA, GPIO_Pin_2);
}

// LED1亮（低电平点亮）
void led1_on(void)  { GPIO_ResetBits(GPIOA, GPIO_Pin_1); }
void led1_off(void) { GPIO_SetBits(GPIOA, GPIO_Pin_1); }

// LED1翻转
void led1_turn(void)
{
    if (GPIO_ReadOutputDataBit(GPIOA, GPIO_Pin_1) == 0)
        GPIO_SetBits(GPIOA, GPIO_Pin_1);
    else
        GPIO_ResetBits(GPIOA, GPIO_Pin_1);
}
```

### 1.3 按键驱动代码

**key.h**
```c
#ifndef __KEY_H__
#define __KEY_H__
#include "stm32f10x.h"

void key_init(void);
uint8_t key_scan(void);

#endif
```

**key.c**
```c
#include "key.h"
#include "delay.h"

// 按键初始化
void key_init(void)
{
    GPIO_InitTypeDef GPIO_InitStruct;
    
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
    
    // B1、B11 上拉输入
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPU;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_1 | GPIO_Pin_11;
    GPIO_Init(GPIOB, &GPIO_InitStruct);
}

// 按键扫描（带消抖）
uint8_t key_scan(void)
{
    uint8_t ret = 0;
    
    // 检测按键1
    if (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_1) == 0)
    {
        delay_ms(10);  // 消抖
        while(GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_1) == 0);  // 等待释放
        delay_ms(10);
        ret = 1;
    }
    
    // 检测按键2
    if (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_11) == 0)
    {
        delay_ms(10);
        while(GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_11) == 0);
        delay_ms(10);
        ret = 2;
    }
    
    return ret;
}
```

### 1.4 主程序
```c
#include "stm32f10x.h"
#include "delay.h"
#include "led.h"
#include "key.h"

int main(void)
{    
    led_init();
    key_init();
    
    while(1)
    {
        if (key_scan() == 1) led1_turn();
        if (key_scan() == 2) led2_turn();
    }
}
```

---

## 二、光敏传感器控制蜂鸣器

### 2.1 硬件接线
- 蜂鸣器接 GPIOB Pin12
- 光敏传感器接 GPIOB Pin13

### 2.2 蜂鸣器驱动代码

**buzzer.h**
```c
#ifndef __BUZZER_H__
#define __BUZZER_H__
#include "stm32f10x.h"

void buzzer_init(void);
void buzzer_on(void);
void buzzer_off(void);

#endif
```

**buzzer.c**
```c
#include "buzzer.h"

void buzzer_init(void)
{
    GPIO_InitTypeDef GPIO_InitStruct;
    
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
    
    // B12 推挽输出
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_12;
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_10MHz;
    GPIO_Init(GPIOB, &GPIO_InitStruct);
    
    GPIO_SetBits(GPIOB, GPIO_Pin_12);  // 初始关闭
}

void buzzer_on(void)  { GPIO_ResetBits(GPIOB, GPIO_Pin_12); }
void buzzer_off(void) { GPIO_SetBits(GPIOB, GPIO_Pin_12); }
```

### 2.3 光敏传感器驱动

**lightsensor.h**
```c
#ifndef __LIGHT_SENSOR_H__
#define __LIGHT_SENSOR_H__
#include "stm32f10x.h"

void lightsensor_init(void);
uint8_t lightsensor_get(void);

#endif
```

**lightsensor.c**
```c
#include "lightsensor.h"

void lightsensor_init(void)
{
    GPIO_InitTypeDef GPIO_InitStruct;
    
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
    
    // B13 上拉输入
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPU;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_13;
    GPIO_Init(GPIOB, &GPIO_InitStruct);
}

// 读取传感器状态
uint8_t lightsensor_get(void)
{
    return GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_13);
}
```

### 2.4 主程序
```c
#include "stm32f10x.h"
#include "delay.h"
#include "buzzer.h"
#include "lightsensor.h"

int main(void)
{    
    buzzer_init();
    lightsensor_init();
    
    while(1)
    {
        // 光线暗时蜂鸣器响
        if (lightsensor_get() == 1)
            buzzer_on();
        else
            buzzer_off();
    }
}
```

---

## 三、核心要点总结

| 模块 | 配置要点 | 关键API |
|------|---------|---------|
| **LED输出** | 推挽输出(GPIO_Mode_Out_PP) | `GPIO_SetBits()` / `GPIO_ResetBits()` |
| **按键输入** | 上拉输入(GPIO_Mode_IPU) | `GPIO_ReadInputDataBit()` |
| **传感器** | 上拉输入，读取数字电平 | 同按键 |
| **蜂鸣器** | 推挽输出，低电平触发 | 同LED |

### 按键消抖原理
```
检测到按下 → 延时10ms → 确认按下 → 等待释放 → 延时10ms → 返回键值
```

---

## 参考链接

- [江科大STM32学习笔记汇总](https://blog.csdn.net/u010249597/article/details/134762513)
- 下一篇：[EXTI外部中断(11)](https://blog.csdn.net/u010249597/article/details/135047842)
