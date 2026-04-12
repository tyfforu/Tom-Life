---
title: 嵌入式 FreeRTOS STM32 面试题汇总
date: 2026-04-12
tags:
  - 嵌入式
  - FreeRTOS
  - STM32
  - 面试
  - 学习
source: https://zhuanlan.zhihu.com/p/1895104636026877368
author: DeepMeet一对一
---

# 嵌入式 FreeRTOS STM32 面试题汇总

> [!info] 来源信息
> - **来源**: [知乎 - 嵌入式Free RTOS单片机STM32面试题汇总](https://zhuanlan.zhihu.com/p/1895104636026877368)
> - **作者**: DeepMeet一对一
> - **赞同**: 94 人
> - **整理时间**: 2026-04-12

---

## 目录

1. [[#FreeRTOS 主要知识点|FreeRTOS 主要知识点]]
2. [[#FreeRTOS 常见面试题|FreeRTOS 常见面试题]]
3. [[#单片机经典面试题|单片机经典面试题]]
4. [[#嵌入式面试题及答案|嵌入式面试题及答案]]

---

## FreeRTOS 主要知识点

### 1. 核心概念

| 概念 | 说明 |
|------|------|
| **任务（Task）** | FreeRTOS 的基本执行单元，具有运行态、就绪态、阻塞态、挂起态和删除态 |
| **任务调度** | 支持抢占式、时间片轮转和合作式调度 |
| **内核对象** | 队列、信号量、互斥量、事件组、任务通知，用于任务间通信和同步 |

### 2. 任务管理

- **创建任务**: `xTaskCreate()` 或静态创建
- **删除任务**: `vTaskDelete()`
- **任务优先级**: 优先级越高，越先获得 CPU

### 3. 内存管理

> [!tip] 内存分配方案
> - **静态分配**: 编译时确定
> - **动态分配**: `pvPortMalloc()` / `vPortFree()`

### 4. 同步与通信

```c
// 队列 - 任务间/中断与任务间消息传递
QueueHandle_t xQueue = xQueueCreate(10, sizeof(int));

// 信号量 - 二值信号量 + 计数型信号量
SemaphoreHandle_t xSemaphore = xSemaphoreCreateBinary();

// 互斥量 - 保护共享资源，避免优先级反转
SemaphoreHandle_t xMutex = xSemaphoreCreateMutex();

// 事件组 - 任务间同步
EventGroupHandle_t xEventGroup = xEventGroupCreate();

// 任务通知 - 轻量级通信
xTaskNotifyGive(xTaskHandle);
```

### 5. 低功耗支持

> [!note] Tickless 模式
> 系统空闲时关闭滴答定时器，进入低功耗模式，事件触发时唤醒。

```c
// 空闲任务回调实现低功耗
void vApplicationIdleHook(void) {
    enterLowPowerMode();
}
```

### 6. 移植与配置

通过 `FreeRTOSConfig.h` 配置:
- 任务优先级
- 调度策略
- 滴答定时器频率 (`configTICK_RATE_HZ`)

---

## FreeRTOS 常见面试题

### 基础概念

**Q: FreeRTOS 的任务状态有哪些？**

| 状态 | 英文 | 说明 |
|------|------|------|
| 就绪态 | Ready | 等待调度 |
| 运行态 | Running | 正在执行 |
| 阻塞态 | Blocked | 等待事件 |
| 挂起态 | Suspended | 被挂起，不参与调度 |

**Q: FreeRTOS 的调度方式有哪些？**

1. **时间片轮转**: 相同优先级任务轮流执行（默认 1ms）
2. **抢占式调度**: 高优先级任务强制打断低优先级任务
3. **协作式调度**: 任务主动让出 CPU（`vTaskDelay`、信号量、互斥量）

**Q: FreeRTOS 的内核对象有哪些？**

| 文件 | 功能 |
|------|------|
| `croutine.c` | 协程 |
| `event_groups.c` | 事件组 |
| `list.c` | 链表 |
| `queue.c` | 队列 |
| `stream_buffer.c` | 流缓冲区 |
| `tasks.c` | 任务管理 |
| `timers.c` | 软件定时器 |
| `heap_x.c` | 内存管理 |

### 任务管理

**Q: 如何创建和删除任务？**

```c
// 动态创建
TaskHandle_t xHandle;
xTaskCreate(vTaskFunction, "TaskName", STACK_SIZE, NULL, PRIORITY, &xHandle);

// 删除任务
vTaskDelete(xHandle);
```

**Q: 如何避免任务优先级反转？**

> [!warning] 优先级反转问题
> 低优先级任务先于高优先级任务执行。

**解决方案**: 使用**互斥量** + **优先级继承**机制

### 同步与通信

**Q: 信号量和互斥量的区别？**

| 特性 | 信号量 | 互斥量 |
|------|--------|--------|
| 取值 | 0~N（计数型）或 0/1（二值） | 只有 0/1 |
| 优先级反转 | 可能发生 | 可避免（优先级继承） |
| 用途 | 同步 + 互斥 | 保护共享资源 |

**Q: 如何使用队列实现任务间通信？**

```c
// 1. 创建队列
QueueHandle_t xQueue = xQueueCreate(10, sizeof(int));

// 2. 发送
int value = 100;
xQueueSend(xQueue, &value, portMAX_DELAY);

// 3. 接收
int received;
xQueueReceive(xQueue, &received, portMAX_DELAY);
```

### 内存管理

**Q: FreeRTOS 的内存分配方案有哪些？**

| 方案 | 特点 | 适用场景 |
|------|------|----------|
| `heap_1` | 只分配，不释放 | 简单系统 |
| `heap_2` | 可释放，不对齐 | 不推荐 |
| `heap_3` | 封装 C 库 malloc/free | 有 C 库环境 |
| `heap_4` | 可释放 + 自动整理碎片 | **推荐** |
| `heap_5` | 支持跨内存区 | 多内存区系统 |

**Q: 如何避免内存泄漏？**

> [!important] 内存管理最佳实践

1. **选择正确的 heap**: 避免 `heap_1`，推荐 `heap_4` 或 `heap_5`
2. **正确释放资源**:
   - `vTaskDelete()` 后释放动态内存
   - `vQueueDelete()` 释放队列
   - `vSemaphoreDelete()` 释放信号量
3. **避免碎片化**: 预分配资源，减少频繁动态分配
4. **使用 FreeRTOS API**: `pvPortMalloc()` / `vPortFree()` 替代标准 malloc/free
5. **启用检测**:
   - `configCHECK_FOR_STACK_OVERFLOW` 检测栈溢出
   - `uxTaskGetStackHighWaterMark()` 监测栈使用

### 低功耗

**Q: FreeRTOS 的 Tickless 模式是如何工作的？**

**工作流程**:
1. 空闲时计算下一次调度所需时间
2. 关闭滴答定时器，进入低功耗模式
3. 定时器中断唤醒，继续调度

**配置**:
```c
#define configUSE_TICKLESS_IDLE 1
```

### 移植

**Q: 如何移植 FreeRTOS？**

1. 复制源码文件（.c / .h）
2. 移植 `FreeRTOSConfig.h`
3. 编译解决报错（头文件路径等）

### 性能优化

> [!tip] FreeRTOS 性能优化 10 条

1. **减少中断延迟**: ISR 保持简短
2. **任务优先级设计**: 避免任务饥饿
3. **简化任务栈**: 减小栈大小
4. **选对 heap**: `heap_4` / `heap_5`
5. **减少动态分配**: 实时任务用静态分配
6. **调节 Tick 频率**: 按需设置 `configTICK_RATE_HZ`
7. **定时器合并**: 减少中断次数
8. **减少上下文切换**: 合理设计 CPU 占用
9. **协作调度**: 非严格实时场景
10. **轻量级同步**: 优先用任务通知

### 任务监控

**Q: 如何监控任务的运行状态？**

```c
// 1. 获取任务状态
eTaskState state = eTaskGetState(xTaskHandle);

// 2. 任务通知
xTaskNotifyGive(xTaskHandle);
ulTaskNotifyTake(pdTRUE, portMAX_DELAY);

// 3. 栈监测
UBaseType_t uxHighWaterMark = uxTaskGetStackHighWaterMark(xTaskHandle);

// 4. 运行时统计
vTaskGetRunTimeStats(pcBuffer);
vTaskList(pcBuffer);
```

**外部工具**:
- **FreeRTOS+Trace**: 可视化时间轴
- **SEGGER SystemView**: 实时监控

---

## 单片机经典面试题

### GPIO

**Q: STM32 的 8 种 GPIO 模式？**

| 模式 | 说明 |
|------|------|
| 浮空输入 | I/O 电平不确定，由外部决定 |
| 上拉输入 | 悬空时保持高电平 |
| 下拉输入 | 悬空时保持低电平 |
| 模拟输入 | 输入模拟信号到片上外设 |
| 开漏输出 | 需外部上拉/下拉决定电平 |
| 开漏复用输出 | 外设控制电平 |
| 推挽输出 | P-MOS + N-MOS 控制电平 |

**Q: 推挽输出 vs 开漏输出？**

| 特性 | 推挽输出 | 开漏输出 |
|------|----------|----------|
| 输出能力 | 高低电平都可输出 | 只能输出低电平 |
| 高电平 | 直接驱动 | 需外部上拉电阻 |
| 应用场景 | LED、继电器 | I²C 总线（线与逻辑） |

### 通信协议

**Q: SPI、I²C、UART 的区别？**

| 协议 | 类型 | 线数 | 特点 |
|------|------|------|------|
| **SPI** | 全双工 | 4 (MISO/MOSI/SCLK/CS) | 速度快，无流控 |
| **I²C** | 半双工 | 2 (SDA/SCL) | 多主控，简单 |
| **UART** | 全双工 | 2 (TX/RX) | 无时钟，距离远 |
| **CAN** | 半双工 | 2 (H/L 差分) | 高可靠，汽车/工控 |

### 无线通信

| 技术 | 频段 | 特点 |
|------|------|------|
| **BLE** | 2.4GHz | 低功耗，近距离 |
| **WiFi** | 2.4GHz | 高速，100m 覆盖 |
| **NFC** | 13.56MHz | 双向识别，极短距 |
| **Zigbee** | 2.4GHz | 低速低功耗，复杂拓扑 |

### IoT 协议

| 协议 | 传输层 | 适用场景 |
|------|--------|----------|
| **MQTT** | TCP | 物联网消息发布/订阅 |
| **CoAP** | UDP | 低资源设备、低功耗网络 |
| **HTTP** | TCP | Web 通信，开销大 |

### 网络基础

**Q: TCP 三次握手？**

```
客户端 → SYN → 服务端
客户端 ← SYN+ACK ← 服务端
客户端 → ACK → 服务端
```

**Q: TCP 四次挥手？**

```
客户端 → FIN → 服务端
客户端 ← ACK ← 服务端
客户端 ← FIN ← 服务端（处理完后）
客户端 → ACK → 服务端
```

### 中断

**Q: 什么是中断？处理流程？**

> [!note] 中断过程
> 1. **保护现场**: PC 地址压栈
> 2. **执行 ISR**: 中断服务程序
> 3. **恢复现场**: PC 从栈中取回
> 4. **继续执行**: 恢复原程序

### 定时器

**Q: 单片机定时器的作用？**

- 计数
- 延时
- 输入捕获
- 输出比较（PWM）
- 产生中断

**优势**: 无需 CPU 参与，精度高

---

## 嵌入式面试题及答案

### 1. STM32 PWM 生成

**Q: 如何在 STM32 中生成 PWM 波？如何计算频率和占空比？**

**公式**:
- **PWM 频率**: $f = \frac{Timer\_Clock}{(ARR + 1) \times (PSC + 1)}$
- **PWM 占空比**: $Duty = \frac{CCR}{ARR + 1} \times 100\%$

其中:
- `ARR`: 自动重装载寄存器
- `PSC`: 预分频器
- `CCR`: 捕获比较寄存器

### 2. FreeRTOS vs RT-Thread

| 特性 | FreeRTOS | RT-Thread |
|------|----------|-----------|
| **定位** | 轻量级内核 | 功能丰富，含软件包管理器 |
| **内存占用** | 极低 | 较多 |
| **内存管理** | 简单静态/小块动态 | 完整堆和栈管理 |
| **适用场景** | 资源受限的小系统 | 需要 GUI、网络等丰富功能 |

### 3. 串口大数据包处理

**Q: 如何接收并解析大数据包（1K+ 字节，带帧头、帧长、校验）？**

**处理流程**:
1. **初始化环形缓冲区**
2. **分段接收**: ISR 中存入缓冲区，检测帧头和帧长
3. **帧校验**: CRC/校验和验证完整性
4. **数据解析**: 提取实际数据内容
5. **错误处理**: 校验失败则丢弃，重置缓冲区

### 4. I²C 协议

**Q: I²C 的工作原理？**

- **物理层**: SCL（时钟）+ SDA（数据），上拉电阻
- **通信过程**:
  1. **起始条件**: SDA 拉低，SCL 高
  2. **数据传输**: 8 位 + ACK
  3. **停止条件**: SDA 拉高，SCL 高

### 5. SPI 协议

**Q: SPI 的基本原理？**

- **四线**: SCLK / MOSI / MISO / CS
- **全双工**: 同时收发
- **主从模式**: 主设备生成时钟，CS 选择从设备

### 6. UART / RS232 / RS485 区别

| 特性 | UART | RS232 | RS485 |
|------|------|-------|-------|
| **电平** | TTL (0/3.3V/5V) | ±12V | 差分 A/B |
| **距离** | 短 | <15m | 1200m |
| **节点** | 点对点 | 点对点 | 多节点 (32+) |
| **抗干扰** | 弱 | 弱 | **强** |

### 7. FreeRTOS 任务调度

**Q: 任务调度的原理？**

- **优先级调度**: 高优先级优先运行
- **时间片轮转**: 同优先级轮流执行
- **抢占式**: 高优先级任务就绪立即抢占

**触发调度的事件**:
- 高优先级任务变为就绪态
- 系统节拍中断（tick）
- 任务挂起/删除
- 延时函数调用

### 8. 看门狗定时器

**Q: 如何在 STM32 中实现看门狗？**

```c
// 独立看门狗 (IWDG)
IWDG_Init(IWDG_Prescaler_64, 625);  // 1s 超时
IWDG_ReloadCounter();  // 喂狗

// 窗口看门狗 (WWDG)
WWDG_Init(0x7F, 0x5F, WWDG_Prescaler_8);
WWDG_SetCounter(0x7F);  // 喂狗
```

### 9. DMA

**Q: DMA 的优势？**

- **直接内存访问**: 无需 CPU 干预
- **减轻 CPU 负担**: 数据传输时 CPU 可执行其他任务
- **高速传输**: 适合大数据量传输（ADC、SPI、SDIO 等）

### 10. 固件升级

**Q: 如何在 STM32 中实现固件升级？**

**方案**:
1. **Bootloader + APP 分区**
2. **通过 UART/USB/OTA 接收新固件**
3. **写入 Flash 并校验**
4. **跳转到新固件执行**

---

## 总结

> [!success] 核心要点
> 1. **理解 FreeRTOS 核心概念**: 任务、调度、同步机制
> 2. **掌握内存管理**: 避免泄漏，选对 heap 方案
> 3. **熟悉通信协议**: SPI/I²C/UART/CAN 的区别和应用
> 4. **了解低功耗设计**: Tickless 模式、空闲任务
> 5. **重视调试技巧**: 栈监测、Trace 工具、SystemView
