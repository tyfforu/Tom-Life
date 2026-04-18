---
source: https://blog.csdn.net/u010249597/article/details/135047842
author: Print World
date: 2026-04-18
tags:
  - STM32
  - 中断
  - EXTI
  - NVIC
  - 嵌入式
---

# STM32学习笔记——EXTI外部中断（11）

> 来源：CSDN @Print World

---

## 一、中断系统基础

### 1.1 什么是中断？
**中断**：在主程序运行过程中，出现特定触发条件（中断源），CPU暂停当前程序，转去处理中断程序，完成后返回原位置继续运行。

**中断优先级**：多个中断同时申请时，CPU根据轻重缓急裁决，优先响应紧急中断。

**中断嵌套**：一个中断程序运行时，更高优先级的中断申请，CPU再次暂停，处理新中断后依次返回。

### 1.2 中断执行流程
```
主程序 → 中断触发 → 保存现场 → 执行中断服务程序 → 恢复现场 → 返回主程序
```

---

## 二、STM32中断特点

- **68个可屏蔽中断通道**：包含EXTI、TIM、ADC、USART、SPI、I2C、RTC等
- **NVIC统一管理**：每个中断通道16个可编程优先等级
- **优先级分组**：可设置抢占优先级和响应优先级

---

## 三、NVIC（嵌套向量中断控制器）

### 3.1 NVIC优先级分组

| 分组 | 抢占优先级位数 | 响应优先级位数 | 说明 |
|------|---------------|---------------|------|
| 组0 | 0位 | 4位 | 全是响应优先级 |
| 组1 | 1位 | 3位 | 2种抢占，8种响应 |
| 组2 | 2位 | 2位 | 4种抢占，4种响应 |
| 组3 | 3位 | 1位 | 8种抢占，2种响应 |
| 组4 | 4位 | 0位 | 16种抢占，无响应 |

### 3.2 优先级规则
- **抢占优先级高** → 可中断嵌套
- **响应优先级高** → 优先排队
- **两者相同** → 按中断号排队

---

## 四、EXTI外部中断

### 4.1 EXTI简介
- **全称**：External Interrupt（外部中断）
- **功能**：监测指定GPIO口电平信号，产生变化时向NVIC发出中断申请
- **触发方式**：上升沿 / 下降沿 / 双边沿 / 软件触发
- **支持GPIO**：所有GPIO口，但**相同Pin不能同时触发**（如PA0和PB0不能同时用）

### 4.2 EXTI通道
- **16个GPIO_Pin**：Pin0 ~ Pin15
- **额外通道**：PVD输出、RTC闹钟、USB唤醒、以太网唤醒

### 4.3 触发响应方式
1. **中断响应**：正常中断流程，CPU执行中断程序
2. **事件响应**：不触发中断，直接触发其他外设操作

---

## 五、AFIO复用IO口

### 5.1 AFIO功能
- **引脚复用功能选择和重定义**
- **中断引脚选择**：选择哪个GPIO的Pin连接到EXTI

### 5.2 外部中断线路映射
```
EXTI0  →  PA0/PB0/PC0... 选一
EXTI1  →  PA1/PB1/PC1... 选一
...
EXTI15 →  PA15/PB15/PC15... 选一
```

---

## 六、计数器模块（对射式红外传感器）

### 6.1 用途
- 电机转速检测
- 脉冲计数
- 位置限位

### 6.2 模块特点
| 参数 | 说明 |
|------|------|
| 槽宽度 | 5mm |
| 输出状态 | 有遮挡→高电平；无遮挡→低电平 |
| 驱动能力 | >15mA |
| 工作电压 | 3.3V ~ 5V |
| 输出形式 | 数字开关量（0和1） |

### 6.3 接线说明
- VCC → 3.3-5V
- GND → 地
- DO → 单片机IO口（TTL开关信号）
- AO → 此模块不起作用

---

## 七、旋转编码器

### 7.1 功能
测量位置、速度或旋转方向，输出与旋转速度和方向对应的方波信号。

### 7.2 类型
- **机械触点式**：成本低，有抖动
- **霍尔传感器式**：无接触，寿命长
- **光栅式**：精度高，价格贵

### 7.3 工作原理
- 两个输出端（A相、B相）输出方波
- 根据相位差判断旋转方向
- 根据频率计算旋转速度

---

## 八、EXTI配置步骤

```c
// 1. 开启时钟（GPIO + AFIO）
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO, ENABLE);

// 2. 配置GPIO为输入模式
GPIO_InitTypeDef GPIO_InitStruct;
GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPU;
GPIO_InitStruct.GPIO_Pin = GPIO_Pin_0;
GPIO_Init(GPIOB, &GPIO_InitStruct);

// 3. AFIO选择中断引脚
GPIO_EXTILineConfig(GPIO_PortSourceGPIOB, GPIO_PinSource0);

// 4. 配置EXTI
EXTI_InitTypeDef EXTI_InitStruct;
EXTI_InitStruct.EXTI_Line = EXTI_Line0;
EXTI_InitStruct.EXTI_LineCmd = ENABLE;
EXTI_InitStruct.EXTI_Mode = EXTI_Mode_Interrupt;
EXTI_InitStruct.EXTI_Trigger = EXTI_Trigger_Falling;  // 下降沿触发
EXTI_Init(&EXTI_InitStruct);

// 5. 配置NVIC
NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);  // 分组2

NVIC_InitTypeDef NVIC_InitStruct;
NVIC_InitStruct.NVIC_IRQChannel = EXTI0_IRQn;
NVIC_InitStruct.NVIC_IRQChannelCmd = ENABLE;
NVIC_InitStruct.NVIC_IRQChannelPreemptionPriority = 1;
NVIC_InitStruct.NVIC_IRQChannelSubPriority = 1;
NVIC_Init(&NVIC_InitStruct);

// 6. 编写中断服务函数
void EXTI0_IRQHandler(void)
{
    if (EXTI_GetITStatus(EXTI_Line0) == SET)
    {
        // 中断处理代码
        
        EXTI_ClearITPendingBit(EXTI_Line0);  // 清除标志位
    }
}
```

---

## 九、知识点速查

| 概念 | 说明 |
|------|------|
| NVIC | 嵌套向量中断控制器，管理所有中断 |
| EXTI | 外部中断/事件控制器，监测GPIO电平变化 |
| AFIO | 复用功能IO，选择中断引脚 |
| 抢占优先级 | 决定能否打断其他中断 |
| 响应优先级 | 决定排队顺序 |
| 中断向量表 | 存储中断服务函数入口地址 |

---

## 参考链接

- [江科大STM32学习笔记汇总](https://blog.csdn.net/u010249597/article/details/134762513)
