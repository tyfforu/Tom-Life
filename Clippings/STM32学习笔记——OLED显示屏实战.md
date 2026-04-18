---
source: https://blog.csdn.net/u010249597/article/details/135033843
author: Print World
date: 2026-04-18
tags:
  - STM32
  - OLED
  - I2C
  - 字模
  - 嵌入式
---

# STM32学习笔记——OLED显示屏实战（10）

> 来源：CSDN @Print World

---

## 一、OLED接线图

**I2C接口接线**：
- GND → GND
- VCC → 3.3V/5V
- SCL → PB8
- SDA → PB9

---

## 二、OLED函数库

### 2.1 头文件 oled.h

```c
#ifndef __OLED_H
#define __OLED_H

#include "stm32f10x.h"

void OLED_Init(void);
void OLED_Clear(void);
void OLED_ShowChar(uint8_t Line, uint8_t Column, char Char);
void OLED_ShowString(uint8_t Line, uint8_t Column, char *String);
void OLED_ShowNum(uint8_t Line, uint8_t Column, uint32_t Number, uint8_t Length);
void OLED_ShowSignedNum(uint8_t Line, uint8_t Column, int32_t Number, uint8_t Length);
void OLED_ShowHexNum(uint8_t Line, uint8_t Column, uint32_t Number, uint8_t Length);
void OLED_ShowBinNum(uint8_t Line, uint8_t Column, uint32_t Number, uint8_t Length);

#endif
```

### 2.2 核心驱动代码 oled.c

#### 引脚配置
```c
#include "stm32f10x.h"
#include "oled_font.h"
#include "oled.h"

/* 引脚配置宏 */
#define OLED_W_SCL(x)  GPIO_WriteBit(GPIOB, GPIO_Pin_8, (BitAction)(x))
#define OLED_W_SDA(x)  GPIO_WriteBit(GPIOB, GPIO_Pin_9, (BitAction)(x))

/* I2C引脚初始化 */
void OLED_I2C_Init(void)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
    
    // PB8-SCL, PB9-SDA 开漏输出
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_8;
    GPIO_Init(GPIOB, &GPIO_InitStructure);
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9;
    GPIO_Init(GPIOB, &GPIO_InitStructure);
    
    OLED_W_SCL(1);
    OLED_W_SDA(1);
}
```

#### I2C通信协议
```c
/* I2C起始信号 */
void OLED_I2C_Start(void)
{
    OLED_W_SDA(1);
    OLED_W_SCL(1);
    OLED_W_SDA(0);
    OLED_W_SCL(0);
}

/* I2C停止信号 */
void OLED_I2C_Stop(void)
{
    OLED_W_SDA(0);
    OLED_W_SCL(1);
    OLED_W_SDA(1);
}

/* I2C发送一个字节 */
void OLED_I2C_SendByte(uint8_t Byte)
{
    uint8_t i;
    for (i = 0; i < 8; i++)
    {
        OLED_W_SDA(Byte & (0x80 >> i));
        OLED_W_SCL(1);
        OLED_W_SCL(0);
    }
    OLED_W_SCL(1);  // 额外时钟，不处理应答
    OLED_W_SCL(0);
}
```

#### OLED命令与数据写入
```c
/* 写命令 */
void OLED_WriteCommand(uint8_t Command)
{
    OLED_I2C_Start();
    OLED_I2C_SendByte(0x78);    // 从机地址
    OLED_I2C_SendByte(0x00);    // 写命令
    OLED_I2C_SendByte(Command);
    OLED_I2C_Stop();
}

/* 写数据 */
void OLED_WriteData(uint8_t Data)
{
    OLED_I2C_Start();
    OLED_I2C_SendByte(0x78);    // 从机地址
    OLED_I2C_SendByte(0x40);    // 写数据
    OLED_I2C_SendByte(Data);
    OLED_I2C_Stop();
}

/* 设置光标位置 */
void OLED_SetCursor(uint8_t Y, uint8_t X)
{
    OLED_WriteCommand(0xB0 | Y);                    // 设置Y位置（页）
    OLED_WriteCommand(0x10 | ((X & 0xF0) >> 4));    // 设置X位置高4位
    OLED_WriteCommand(0x00 | (X & 0x0F));           // 设置X位置低4位
}
```

#### 清屏与显示函数
```c
/* OLED清屏 */
void OLED_Clear(void)
{  
    uint8_t i, j;
    for (j = 0; j < 8; j++)      // 8页
    {
        OLED_SetCursor(j, 0);
        for(i = 0; i < 128; i++) // 每页128列
        {
            OLED_WriteData(0x00);
        }
    }
}

/* 显示一个字符（8x16像素） */
void OLED_ShowChar(uint8_t Line, uint8_t Column, char Char)
{          
    uint8_t i;
    // 设置光标在上半部分（第Line*2-2页）
    OLED_SetCursor((Line - 1) * 2, (Column - 1) * 8);
    for (i = 0; i < 8; i++)
    {
        OLED_WriteData(OLED_F8x16[Char - ' '][i]);
    }
    // 设置光标在下半部分（第Line*2-1页）
    OLED_SetCursor((Line - 1) * 2 + 1, (Column - 1) * 8);
    for (i = 0; i < 8; i++)
    {
        OLED_WriteData(OLED_F8x16[Char - ' '][i + 8]);
    }
}

/* 显示字符串 */
void OLED_ShowString(uint8_t Line, uint8_t Column, char *String)
{
    uint8_t i;
    for (i = 0; String[i] != '\0'; i++)
    {
        OLED_ShowChar(Line, Column + i, String[i]);
    }
}

/* 次方函数（内部使用） */
uint32_t OLED_Pow(uint32_t X, uint32_t Y)
{
    uint32_t Result = 1;
    while (Y--)
        Result *= X;
    return Result;
}

/* 显示十进制数字 */
void OLED_ShowNum(uint8_t Line, uint8_t Column, uint32_t Number, uint8_t Length)
{
    uint8_t i;
    for (i = 0; i < Length; i++)                            
    {
        OLED_ShowChar(Line, Column + i, 
            Number / OLED_Pow(10, Length - i - 1) % 10 + '0');
    }
}

/* 显示有符号十进制数 */
void OLED_ShowSignedNum(uint8_t Line, uint8_t Column, int32_t Number, uint8_t Length)
{
    uint32_t Number1;
    if (Number >= 0)
    {
        OLED_ShowChar(Line, Column, '+');
        Number1 = Number;
    }
    else
    {
        OLED_ShowChar(Line, Column, '-');
        Number1 = -Number;
    }
    for (uint8_t i = 0; i < Length; i++)                            
    {
        OLED_ShowChar(Line, Column + i + 1, 
            Number1 / OLED_Pow(10, Length - i - 1) % 10 + '0');
    }
}

/* 显示十六进制数字 */
void OLED_ShowHexNum(uint8_t Line, uint8_t Column, uint32_t Number, uint8_t Length)
{
    uint8_t i, SingleNumber;
    for (i = 0; i < Length; i++)                            
    {
        SingleNumber = Number / OLED_Pow(16, Length - i - 1) % 16;
        if (SingleNumber < 10)
            OLED_ShowChar(Line, Column + i, SingleNumber + '0');
        else
            OLED_ShowChar(Line, Column + i, SingleNumber - 10 + 'A');
    }
}

/* 显示二进制数字 */
void OLED_ShowBinNum(uint8_t Line, uint8_t Column, uint32_t Number, uint8_t Length)
{
    uint8_t i;
    for (i = 0; i < Length; i++)                            
    {
        OLED_ShowChar(Line, Column + i, 
            Number / OLED_Pow(2, Length - i - 1) % 2 + '0');
    }
}
```

#### OLED初始化
```c
void OLED_Init(void)
{
    uint32_t i, j;
    
    // 上电延时
    for (i = 0; i < 1000; i++)
        for (j = 0; j < 1000; j++);
    
    OLED_I2C_Init();
    
    OLED_WriteCommand(0xAE);  // 关闭显示
    OLED_WriteCommand(0xD5);  // 设置显示时钟分频
    OLED_WriteCommand(0x80);
    OLED_WriteCommand(0xA8);  // 设置多路复用率
    OLED_WriteCommand(0x3F);
    OLED_WriteCommand(0xD3);  // 设置显示偏移
    OLED_WriteCommand(0x00);
    OLED_WriteCommand(0x40);  // 设置显示开始行
    OLED_WriteCommand(0xA1);  // 设置左右方向（正常）
    OLED_WriteCommand(0xC8);  // 设置上下方向（正常）
    OLED_WriteCommand(0xDA);  // 设置COM引脚配置
    OLED_WriteCommand(0x12);
    OLED_WriteCommand(0x81);  // 设置对比度
    OLED_WriteCommand(0xCF);
    OLED_WriteCommand(0xD9);  // 设置预充电周期
    OLED_WriteCommand(0xF1);
    OLED_WriteCommand(0xDB);  // 设置VCOMH
    OLED_WriteCommand(0x30);
    OLED_WriteCommand(0xA4);  // 设置整个显示打开/关闭
    OLED_WriteCommand(0xA6);  // 设置正常/倒转显示
    OLED_WriteCommand(0x8D);  // 设置充电泵
    OLED_WriteCommand(0x14);
    OLED_WriteCommand(0xAF);  // 开启显示
    
    OLED_Clear();
}
```

---

## 三、字模库 oled_font.h

```c
#ifndef __OLED_FONT_H
#define __OLED_FONT_H

/* OLED字模库，宽8像素，高16像素 */
const uint8_t OLED_F8x16[][16] =
{
    // ASCII字符 32~126的字模数据
    // 每个字符16字节，前8字节是上半部分，后8字节是下半部分
    
    // 空格 ' ' (0x20)
    0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
    0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
    
    // '0' (0x30)
    0x00,0xE0,0x10,0x08,0x08,0x10,0xE0,0x00,
    0x00,0x0F,0x10,0x20,0x20,0x10,0x0F,0x00,
    
    // 'A' (0x41)
    0x00,0x00,0xC0,0x38,0xE0,0x00,0x00,0x00,
    0x20,0x3C,0x23,0x02,0x02,0x27,0x38,0x20,
    
    // ... 其他字符
};

#endif
```

---

## 四、测试代码

```c
#include "stm32f10x.h"
#include "delay.h"
#include "oled.h"

int main(void)
{    
    OLED_Init();
    
    // 显示字符
    OLED_ShowChar(1, 1, 'A');
    
    // 显示字符串
    OLED_ShowString(1, 3, "HelloWorld!");
    
    // 显示十进制数字
    OLED_ShowNum(2, 1, 12345, 5);
    
    // 显示有符号数
    OLED_ShowSignedNum(2, 7, -66, 2);
    
    // 显示十六进制
    OLED_ShowHexNum(3, 1, 0xAA55, 4);
    
    // 显示二进制
    OLED_ShowBinNum(4, 1, 0xAA55, 16);
    
    while(1);
}
```

---

## 五、显示效果说明

| 函数 | 显示效果 |
|------|---------|
| `OLED_ShowChar(1, 1, 'A')` | 第1行第1列显示 A |
| `OLED_ShowString(1, 3, "Hello!")` | 第1行第3列显示 Hello! |
| `OLED_ShowNum(2, 1, 123, 3)` | 第2行显示 123 |
| `OLED_ShowSignedNum(2, 5, -5, 1)` | 显示 -5 |
| `OLED_ShowHexNum(3, 1, 255, 2)` | 显示 FF |
| `OLED_ShowBinNum(4, 1, 5, 3)` | 显示 101 |

---

## 六、关键要点总结

1. **I2C通信**：软件模拟I2C，SCL和SDA都是开漏输出
2. **显示原理**：128×64像素，分8页，每页128字节
3. **字符显示**：8×16像素，分上下两部分显示
4. **字模取模**：使用取模软件生成字模数据
5. **坐标系统**：行(1-4)，列(1-16)，对应像素坐标要×8

---

## 参考链接

- [江科大STM32学习笔记汇总](https://blog.csdn.net/u010249597/article/details/134762513)
