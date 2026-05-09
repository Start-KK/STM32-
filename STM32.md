# 一 GPIO

（人的手）引脚命名 片上外设 

## 1 八种工作模式

| 输出         | 输入     |
| ------------ | -------- |
| 通用输出推挽 | 输入上拉 |
| 通用输出开漏 | 输入下拉 |
| 复用输出推挽 | 输出浮空 |
| 复用输出开漏 | 模拟模式 |

 **输出：**

|          | 推挽         | 开漏         |
| -------- | ------------ | ------------ |
| **通用** | 通用输出推挽 | 通用输出开漏 |
| **复用** | 复用输出推挽 | 复用输出开漏 |

![1775389861502](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775389861502.png)

![1775390030169](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775390030169.png)

![1775390038852](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775390038852.png)

## 2 I/O的最大输出速度

- **什么是IO的最大输出速度?**

I/O最大输出速度：向IO交替写0和1且输出不失真的最快速度

- **上升时间、下降时间和保持时间的概念**

上升时间：缓慢上升的时间0V-3.3V

下降时间：从3.3V-0V的时间

保持时间：3.3V保持的时间，输出有效的电压

- **什么限制了IO的最大输出速度？**

上升时间和下降时间的长短

- **IO引脚的最大输出速度怎么选？**

低速2MHZ

中速10MHZ  

高速50MHZ

选取满足要求的最小值，过于陡峭得边沿会增加耗电、并引入EMI问题

## 3 LED闪灯实验

时钟：类比成心跳，供血。就是让芯片“动起来”的节拍信号，决定运行速度和时间基准

### 3.1 GPIO_Init 编程接口

![1775397870402](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775397870402.png)

```c
#include "stm32f10x.h"
#include "delay.h"
int main(void)
{
	//开启GPIOC的时钟
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC,ENABLE);
	
	//初始化IO引脚，PC13通用输出开漏模式 2MHZ
	GPIO_InitTypeDef GPIO_InitStruct={0};
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_13;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_Out_OD;//开漏
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_2MHz;
	GPIO_Init(GPIOC,&GPIO_InitStruct);//初始化

	
	while(1)
	{
			GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_RESET);
		  Delay(100);
			GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);
		  Delay(100);
	}
}

```

## 4 GPIO的4种输入模式

![1775391263406](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775391263406.png)

​               PDF：[2GPIO输入输出.pdf](2GPIO输入输出.pdf) 

**开关断开时：** 引脚通过上拉电阻连接到 VCC。由于引脚输入端的阻抗非常大，几乎没有电流流动，根据欧姆定律在此处键入公式。 U = I R，电阻上的压降极小，因此引脚感受到的电压几乎等于 VCC（高电平）。

 **开关闭合时：** 引脚直接接地（GND）。此时引脚被强制拉低为低电平（0V）。上拉电阻在这里起到了限流作用，防止电源和地之间发生短路。

![1775395852266](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775395852266.png)

![1775395860691](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775395860691.png)

![1775395869216](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775395869216.png)

![1775395877915](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775395877915.png)

![1775395883206](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775395883206.png)

![1775395892061](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775395892061.png)

![1775395897708](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775395897708.png)

## 5 按钮实验

按下点亮，松开熄灭。

```c
#include "stm32f10x.h"  // 包含STM32F10x系列的头文件，提供对STM32硬件的访问

int main(void)
{
    // 使能GPIOA时钟
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);  // 使能GPIOA端口的时钟，以便对GPIOA进行配置和操作

    // 初始化GPIO配置结构体，清零
    GPIO_InitTypeDef GPIO_InitStruct = {0};  // 创建GPIO初始化结构体，并初始化为零

    // 配置GPIOA的Pin 0为推挽输出模式
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_0;                 // 选择GPIOA的Pin 0
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;           // 设置为推挽输出模式（Output Push-Pull）
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_2MHz;           // 设置输出速度为2MHz
    GPIO_Init(GPIOA, &GPIO_InitStruct);                      // 初始化GPIOA的Pin 0，应用配置

    // 配置GPIOA的Pin 1为上拉输入模式
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_1;                  // 选择GPIOA的Pin 1
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPU;              // 设置为上拉输入模式（Input Pull-Up）
    GPIO_Init(GPIOA, &GPIO_InitStruct);                      // 初始化GPIOA的Pin 1，应用配置

    // 无限循环，用于持续检测输入信号并控制输出
    while(1)
    {
        // 读取GPIOA Pin 1的输入电平，如果是低电平（按钮按下），则点亮LED
        if(GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_1) == Bit_RESET)  // 如果GPIOA的Pin 1为低电平（按钮按下）
        {
            // 设置GPIOA Pin 0为高电平，点亮LED
            GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_SET);  // 将GPIOA的Pin 0设置为高电平（LED亮）
        }
        else
        {
            // 设置GPIOA Pin 0为低电平，熄灭LED
            GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_RESET);  // 将GPIOA的Pin 0设置为低电平（LED灭）
        }
    }
}
```

## 6 电压、电流、电阻（本质关系）

**可以把电路想象成水管：**

电流 👉 水流            电压 👉 水压 

👉 水压越大，水流越猛
 👉 电压越高，电流越容易流动

**一句话总结**

👉 **电压就是让电动起来的驱动力，也是电子系统中最基础的控制手段**

## 7 题目

MCU VDD引脚需要外接电容到底，这里电容有什么作用？

在单片机（MCU）的硬件设计中，VDD 引脚外接到底（GND）的电容被称为**去耦电容（Decoupling Capacitor）或旁路电容（Bypass Capacitor）**。

虽然它在原理图上看起来只是一个不起眼的元件，但它是整个系统稳定运行的“定海神针”。如果漏掉它，单片机大概率会出现莫名其妙的死机、重启或者通信乱码。

它的核心作用可以总结为两个极其生动的比喻：**“屋顶水箱”** 和 **“噪音垃圾桶”**。

### 1. 充当“屋顶水箱”（提供瞬态大电流，稳定电压）

- **痛点**：当单片机在运行过程中突然执行高强度计算、翻转一大批 GPIO 引脚或者启动外设时，芯片内部的数百万个晶体管会同时开关。这会在几纳秒内向电源索要一股巨大的瞬态电流。
- **走线的局限**：电源芯片（比如 LDO）通常离单片机有一段距离，PCB 走线本身存在**寄生电感**。寄生电感就像一根“很细的水管”，它会阻碍电流的瞬间突变。如果单片机瞬间猛抽电流，而远处的电源来不及送过来，VDD 引脚处的电压就会瞬间暴跌。一旦跌破最低工作电压，单片机直接硬件复位（Brown-out Reset）。
- **电容的救场**：我们把电容尽可能地**紧贴着 VDD 引脚**放置。电容就像是你家屋顶上的“储水箱”。当单片机瞬间需要大水流时，不用等远处的自来水厂（电源）供水，直接从旁边的储水箱（电容）里抽水。这就完美维持了 VDD 电压的稳定。

### 2. 充当“噪音垃圾桶”（滤除高频干扰）

- **痛点**：外部电源线就像一根天线，很容易吸收环境中的高频电磁干扰（比如马达启动、开关电源的高频辐射），产生电压毛刺。
- **电容的特性**：电容在电路中有一个物理特性——**通交流，阻直流**，并且**频率越高，它越像一条直通的导线**。
- **滤除过程**：对于 3.3V 的纯净直流电，电容是断开的，不耗电。但当高频杂波顺着 VDD 跑过来时，电容对它们来说简直就是一条毫无阻力的“高速公路”。于是，这些高频噪音全部顺着电容直接流进了 GND（地），被安全地“倒进垃圾桶”，从而保护了单片机内部脆弱的逻辑电路。

------

### 为什么通常是一大一小两个电容并联？（如 10µF + 0.1µF）

在工程实战中，你经常会看到每个 VDD 引脚旁边不仅接了一个，而是接了两个电容。

- **小电容（通常是 0.1µF / 100nF 陶瓷电容）**：它的内部寄生参数极小，反应极快，专门负责对付**超高频**的噪音和纳秒级的瞬间电流抽载。**必须离引脚最近！**
- **大电容（通常是 10µF 钽电容或陶瓷电容）**：它的容量大，像个大水库，专门负责平抑**低频**的电压波动，作为系统级的能源缓冲。

为了让你更直观地理解，我们可以通过下面这个“电源示波器模拟器”来看看，在没有电容和有电容的情况下，当单片机突然进入高负载时，VDD 引脚上的电压会发生什么可怕的变化：

#  二 串口

## 1 串口通信协议（UART）

串口通信协议（UART）

串行通信：一次传1位

核心参数：

波特率、数据位、停止位、校验位、起始位

一帧结构：

起始位（0） + 数据位（低位先发） + 校验位（可选） + 停止位（1）

常用配置：8N1（8位数据、无校验、1位停止）

原理：用电压高低表示0/1，按约定时间发送

应用：调试串口、传感器通信、模块通信

## 2 UART模块的使用方式

### 2.1 什么是 USART？

USART = **Universal Synchronous / Asynchronous Receiver Transmitter**
 （通用同步/异步收发器）

它是单片机内部的一个 **串口外设模块**，用于：

- 串口调试
- 模块通信（蓝牙 / WiFi / GPS）
- 上位机通信

### 2.2 USART的基本使用方法

![1775397016760](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775397016760.png)

**1 波特率产生（时钟控制）**

位于图上方灰色的区域是 USART 的“心脏”。

- **原理：** 所有的串行通信都必须基于统一的节奏（波特率）。系统时钟经过**分频器**处理。

- **波特率寄存器 (BRR)：** 这里的数值决定了分频系数。公式通常为：
  $$
  \text{Baud Rate} = \frac{f_{CK}}{16 \times \text{USARTDIV}}
  $$
  
- **注意点：** 为什么图中有个 `÷16`？这是为了实现**过采样**，提高接收数据的抗干扰能力。

**2 数据发送 (TX)**

观察图中从“100”到 “Tx” 的路径：

- **发送数据寄存器 (TDR)：** 当你在代码中写下 `USART_SendData(100)` 时，数字 100 (二进制 `01100100`) 首先进入 TDR。
- **发送移位寄存器：** 这是关键！数据会从 TDR 转移到移位寄存器，然后**一位一位 (bit by bit)** 地从 Tx 引脚发送出去。
- **波形对应：** 看到右侧蓝色的波形了吗？`起-0-0-1-0-0-1-1-0-停`。这就是把并行数据转换成了串行波形。
  - **起：** 起始位（低电平）。
  - **停：** 停止位（高电平）。

**3 数据接收 (RX)**

观察图中从 “Rx” 到 “88” 的路径：

- **接收移位寄存器：** 外部电平信号从 Rx 引脚进来，按照波特率的节奏，把一串高低电平重新拼成一个字节。
- **接收数据寄存器 (RDR)：** 一旦拼完一个完整的字节，它会迅速“打入”接收数据寄存器。
- **结果：** 最终你读到的就是 88 (二进制 `01011000`)。

**4 状态标志与中断控制**

图中右侧那一排 `0/1` 开关和与门，是写代码时逻辑判断的核心：

- **TXE (Transmit data register Empty)：** 发送寄存器空。一旦为 1，代表你可以往里写下一个数了。
- **TC (Transmission Complete)：** 发送完成。代表连移位寄存器里的最后一位也发出去了。
- **RXNE (Read data register Not Empty)：** 接收寄存器非空。这是**最常用**的中断触发位，一旦为 1，赶紧去读数据，否则下一帧数据过来会产生 **ORE (过载错误)**。

**5. 工程师视角：基本配置步骤**

在 STM32 等单片机上使用时，你的代码逻辑应该是：

1. **开启时钟：** 开启 GPIO 时钟和 USART 外设时钟。
2. **GPIO 配置：**
   - TX 引脚配置为**复用推挽输出**。
   - RX 引脚配置为**浮空输入或上拉输入**。
3. **配置参数（对应图中“数据帧格式”）：**
   - **波特率：** 常用 9600 或 115200。
   - **字长：** 8位或 9位。
   - **停止位：** 1位或 2位。
   - **校验位：** 无校验 (None)、奇校验 (Odd) 或 偶校验 (Even)。
4. **使能外设：** 设置 `UE` (USART Enable) 位。
5. **编写中断/轮询逻辑：** 监控 `RXNE` 读数据，监控 `TXE` 发数据。

### 2.3 波特率的设置方法

波特率：每秒钟最多传输多少位

常见的波特率：9600、11520、921600

怎么设置72MHz的波特率？

![1775397685904](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775397685904.png)

### 2.4 USART_Init 编程接口

![1775397729042](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775397729042.png)

## 3 为串口初始化引脚

USART通过TX发送数据、RX接收数据，双方交叉连接，再加一个GND就可以通信

### 3.1引脚分布表

| **引脚号** | **引脚名称** | **类型** | **I/O 电平** | **主功能** | **默认复用功能**                        | **重定义功能**                 |
| ---------- | ------------ | -------- | ------------ | ---------- | --------------------------------------- | ------------------------------ |
| 1          | **VBAT**     | S        |              | VBAT       |                                         |                                |
| 2          | **PC13**     | I/O      |              | PC13       | TAMPER-RTC                              |                                |
| 3          | **PC14**     | I/O      |              | PC14       | OSC32_IN                                |                                |
| 4          | **PC15**     | I/O      |              | PC15       | OSC32_OUT                               |                                |
| 5          | **OSC_IN**   | I        |              | OSC_IN     |                                         |                                |
| 6          | **OSC_OUT**  | O        |              | OSC_OUT    |                                         |                                |
| 7          | **NRST**     | I/O      |              | NRST       |                                         |                                |
| 8          | **VSSA**     | S        |              | VSSA       |                                         |                                |
| 9          | **VDDA**     | S        |              | VDDA       |                                         |                                |
| 10         | **PA0**      | I/O      |              | PA0        | WKUP/USART2_CTS/ADC12_IN0/TIM2_CH1_ETR  |                                |
| 11         | **PA1**      | I/O      |              | PA1        | USART2_RTS/ADC12_IN1/TIM2_CH2           |                                |
| 12         | **PA2**      | I/O      |              | PA2        | USART2_TX/ADC12_IN2/TIM2_CH3            |                                |
| 13         | **PA3**      | I/O      |              | PA3        | USART2_RX/ADC12_IN3/TIM2_CH4            |                                |
| 14         | **PA4**      | I/O      |              | PA4        | SPI1_NSS/USART2_CK/ADC12_IN4            |                                |
| 15         | **PA5**      | I/O      |              | PA5        | SPI1_SCK/ADC12_IN5                      |                                |
| 16         | **PA6**      | I/O      |              | PA6        | SPI1_MISO/ADC12_IN6/TIM3_CH1            | TIM1_BKIN                      |
| 17         | **PA7**      | I/O      |              | PA7        | SPI1_MOSI/ADC12_IN7/TIM3_CH2            | TIM1_CH1N                      |
| 18         | **PB0**      | I/O      |              | PB0        | ADC12_IN8/TIM3_CH3                      | TIM1_CH2N                      |
| 19         | **PB1**      | I/O      |              | PB1        | ADC12_IN9/TIM3_CH4                      | TIM1_CH3N                      |
| 20         | **PB2**      | I/O      | **FT**       | PB2/BOOT1  |                                         |                                |
| 21         | **PB10**     | I/O      | **FT**       | PB10       | I2C2_SCL/USART3_TX                      | TIM2_CH3                       |
| 22         | **PB11**     | I/O      | **FT**       | PB11       | I2C2_SDA/USART3_RX                      | TIM2_CH4                       |
| 23         | **VSS_1**    | S        |              | VSS_1      |                                         |                                |
| 24         | **VDD_1**    | S        |              | VDD_1      |                                         |                                |
| 25         | **PB12**     | I/O      | **FT**       | PB12       | SPI2_NSS/I2C2_SMBAI/USART3_CK/TIM1_BKIN |                                |
| 26         | **PB13**     | I/O      | **FT**       | PB13       | SPI2_SCK/USART3_CTS/TIM1_CH1N           |                                |
| 27         | **PB14**     | I/O      | **FT**       | PB14       | SPI2_MISO/USART3_RTS/TIM1_CH2N          |                                |
| 28         | **PB15**     | I/O      | **FT**       | PB15       | SPI2_MOSI/TIM1_CH3N                     |                                |
| 29         | **PA8**      | I/O      | **FT**       | PA8        | USART1_CK/TIM1_CH1/MCO                  |                                |
| 30         | **PA9**      | I/O      | **FT**       | PA9        | USART1_TX/TIM1_CH2                      |                                |
| 31         | **PA10**     | I/O      | **FT**       | PA10       | USART1_RX/TIM1_CH3                      |                                |
| 32         | **PA11**     | I/O      | **FT**       | PA11       | USART1_CTS/USBDM/CAN_RX/TIM1_CH4        |                                |
| 33         | **PA12**     | I/O      | **FT**       | PA12       | USART1_RTS/USBDP/CAN_TX/TIM1_ETR        |                                |
| 34         | **PA13**     | I/O      | **FT**       | JTMS/SWDIO |                                         | PA13                           |
| 35         | **VSS_2**    | S        |              | VSS_2      |                                         |                                |
| 36         | **VDD_2**    | S        |              | VDD_2      |                                         |                                |
| 37         | **PA14**     | I/O      | **FT**       | JTCK/SWCLK |                                         | PA14                           |
| 38         | **PA15**     | I/O      | **FT**       | JTDI       |                                         | TIM2_CH1_ETR/PA15/SPI1_NSS     |
| 39         | **PB3**      | I/O      | **FT**       | JTDO       |                                         | PB3/TRACESWO/TIM2_CH2/SPI1_SCK |
| 40         | **PB4**      | I/O      | **FT**       | NJTRST     |                                         | PB4/TIM3_CH1/SPI1_MISO         |
| 41         | **PB5**      | I/O      |              | PB5        | I2C1_SMBAI                              | TIM3_CH2/SPI1_MOSI             |
| 42         | **PB6**      | I/O      | **FT**       | PB6        | I2C1_SCL/TIM4_CH1                       | USART1_TX                      |
| 43         | **PB7**      | I/O      | **FT**       | PB7        | I2C1_SDA/TIM4_CH2                       | USART1_RX                      |
| 44         | **BOOT0**    | I        |              | BOOT0      |                                         |                                |
| 45         | **PB8**      | I/O      | **FT**       | PB8        | TIM4_CH3                                | I2C1_SCL/CAN_RX                |
| 46         | **PB9**      | I/O      | **FT**       | PB9        | TIM4_CH4                                | I2C1_SDA/CAN_TX                |
| 47         | **VSS_3**    | S        |              | VSS_3      |                                         |                                |
| 48         | **VDD_3**    | S        |              | VDD_3      |                                         |                                |

### 3.2 重映射表

| **外设模块** | **功能信号**        | **默认引脚 (Default)** | **重映射引脚 (Remapped)**          |
| ------------ | ------------------- | ---------------------- | ---------------------------------- |
| **USART1**   | TX / RX             | PA9 / PA10             | **PB6 / PB7**                      |
| **USART2**   | TX / RX             | PA2 / PA3              | PD5 / PD6 (C8T6引脚不足，通常不用) |
| **I2C1**     | SCL / SDA           | PB6 / PB7              | **PB8 / PB9**                      |
| **SPI1**     | NSS/SCK/MISO/MOSI   | PA4 / PA5 / PA6 / PA7  | **PA15 / PB3 / PB4 / PB5**         |
| **CAN**      | RX / TX             | PA11 / PA12            | **PB8 / PB9**                      |
| **TIM1**     | BKIN/CH1N/CH2N/CH3N | PA6 / PA7 / PB0 / PB1  | 部分引脚位于 PB12~PB15             |
| **TIM2**     | CH1 / CH2           | PA0 / PA1              | **PA15 / PB3**                     |
| **TIM2**     | CH3 / CH4           | PA2 / PA3              | **PB10 / PB11**                    |
| **TIM3**     | CH1 / CH2           | PA6 / PA7              | **PB4 / PB5**                      |

### 3.3 IO配置表

| **应用场景**               | **推荐配置模式 (Mode)**               | **理由**                           |
| -------------------------- | ------------------------------------- | ---------------------------------- |
| **普通 LED / 继电器控制**  | **推挽输出 (Out_PP)**                 | 提供较强的驱动电流（最大约 25mA）  |
| **I2C 通信 (SCL/SDA)**     | **复用开漏输出 (AF_OD)**              | I2C 协议要求，需配合外部上拉电阻   |
| **UART 串口发送 (TX)**     | **复用推挽输出 (AF_PP)**              | 高速信号传输，保证边沿陡峭         |
| **UART 串口接收 (RX)**     | **浮空输入 (IN_FLOATING) / 上拉输入** | 接收异步信号，防止静电干扰         |
| **SPI 通信 (SCK/MOSI)**    | **复用推挽输出 (AF_PP)**              | 需要高频率同步时钟信号             |
| **ADC 模拟采样 (PA0~PB1)** | **模拟输入 (AIN)**                    | 关闭施密特触发器，减少数字噪声干扰 |
| **按键检测 (KEY)**         | **上拉/下拉输入 (IPU/IPD)**           | 确定按键未按下时的默认电平状态     |
| **PWM 输出 (定时器)**      | **复用推挽输出 (AF_PP)**              | 用于驱动电机或调节亮度             |

### 3.4 编写代码（默认PA9 PA10）

```c
#include "stm32f10x.h"

int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1,ENABLE);//开启时钟
	USART_InitTypeDef USART_InitStruct;
	
	USART_InitStruct.USART_BaudRate=115200;//波特率115200
	USART_InitStruct.USART_Mode=USART_Mode_Tx|USART_Mode_Rx;//双向
	USART_InitStruct.USART_WordLength=USART_WordLength_8b;//8位数据位
	USART_InitStruct.USART_Parity=USART_Parity_No;//无校验
	USART_InitStruct.USART_StopBits=USART_StopBits_1;//1位停止位
	USART_Init(USART1,&USART_InitStruct);
	//无映射
	GPIO_InitTypeDef GPIO_InitStruct;
	//Tx PA9 复用输出推挽
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_9;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
	//RX PA10 输入上拉
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_10;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
	
	
	//重映射//PB6 PB7
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	GPIO_PinRemapConfig(GPIO_Remap_USART1,ENABLE);
	
	//GPIO_InitTypeDef GPIO_InitStruct;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_6;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_7;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	USART_Cmd(USART1,ENABLE);//闭合总开关

	while(1)
	{
	}
}

```



### 3.5 编写代码（重映射PB6 PB7）

```c
#include "stm32f10x.h"

int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1,ENABLE);//开启时钟
	USART_InitTypeDef USART_InitStruct;
	
	USART_InitStruct.USART_BaudRate=115200;//波特率115200
	USART_InitStruct.USART_Mode=USART_Mode_Tx|USART_Mode_Rx;//双向
	USART_InitStruct.USART_WordLength=USART_WordLength_8b;//8位数据位
	USART_InitStruct.USART_Parity=USART_Parity_No;//无校验
	USART_InitStruct.USART_StopBits=USART_StopBits_1;//1位停止位
	USART_Init(USART1,&USART_InitStruct);
	//无映射
	GPIO_InitTypeDef GPIO_InitStruct;
	//Tx PA9 复用输出推挽
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_9;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
	//RX PA10 输入上拉
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_10;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
	
	
	//重映射//PB6 PB7
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	GPIO_PinRemapConfig(GPIO_Remap_USART1,ENABLE);
	
	//GPIO_InitTypeDef GPIO_InitStruct;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_6;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_7;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	USART_Cmd(USART1,ENABLE);//闭合总开关

	while(1)
	{
	}
}

```

## 4 发送数据

### 4.1 TxE标志位

**TxE (Transmit Data Register Empty)** 

**TxE 的全称是“发送数据寄存器空”**

- **置 1 (SET)**：表示 **TDR (发送数据寄存器)** 中的内容已经成功转移到了 **移位寄存器** 中 。此时，TDR 变空，你可以写入下一个待发送的字节 。
- **置 0 (RESET)**：表示 TDR 中还有数据，移位寄存器还没把它接走 。此时如果你强行写入新数据，旧数据会被直接覆盖（导致丢包）。

![1775397016760](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775397016760.png)

### 4.2 TC标志位

**TC (Transmission Complete，发送完成)**

**TC** 表示“发送完成”。

- **置 1 (SET)**：意味着 **TDR (发送数据寄存器)** 为空，且 **移位寄存器 (Shift Register)** 中的最后一位数据也已经通过 TX 引脚物理发送出去了。
- **置 0 (RESET)**：表示硬件正在发送数据，或者你刚刚往 TDR 写入了新数据。

![1775397016760](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775397016760.png)

### 4.3 USART_Cmd 编程接口

![1775399569880](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775399569880.png)

### 4.4 USART_GetFlagStatus编程接口

![1775399640646](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775399640646.png)

### 4.5 USART_SendData编程接口

![1775399671639](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775399671639.png)

### 4.6 编写代码

```c
#include "stm32f10x.h"
#include<stdio.h>
void My_USART_SendBytes(USART_TypeDef *USARTxun,uint8_t *pData,uint16_t Size);
void My_USART_Init(void);

int main(void)
{
	My_USART_Init();
	//无映射
	
	//Tx PA9 复用输出推挽
	/*RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_9;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
	//RX PA10 输入上拉
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_10;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_Init(GPIOA,&GPIO_InitStruct);*/
	
	
	//重映射
	GPIO_InitTypeDef GPIO_InitStruct;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	GPIO_PinRemapConfig(GPIO_Remap_USART1,ENABLE);
	
	//GPIO_InitTypeDef GPIO_InitStruct;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_6;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_7;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	
	USART_Cmd(USART1,ENABLE);//闭合总开关
	//传输数据
	uint8_t bytestosend[]={1,2,3,4,5};
	
	My_USART_SendBytes(USART1,bytestosend,5);
	printf("1,2,3,4,5,6");
	while(1)
	{
	}
}
//@简介：通过串口发送多个字节
//@参数：USARTx：填写串口名称
//@参数：pData：要发送的数据
//@参数：Size：要发送数据的数量，单位是字节
//
//
void My_USART_SendBytes(USART_TypeDef *USARTx,uint8_t *pData,uint16_t Size)
{
	for(uint32_t i=0;i<Size;i++)
	{
		while(USART_GetFlagStatus(USART1,USART_FLAG_TXE)==RESET);//等待发送数据寄存器空
		USART_SendData(USARTx,pData[i]);//将要发送的数据写入到发送数据寄存器
	}
	//等待数据发送完成
	while(USART_GetFlagStatus(USART1,USART_FLAG_TC)==RESET);
}

//@简介：对USART1进行初始化
//PB6-Tx  PB7-Rx
//115200 8, 1, None 双向

void My_USART_Init(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1,ENABLE);//开启时钟
	USART_InitTypeDef USART_InitStruct;
	
	USART_InitStruct.USART_BaudRate=115200;//波特率115200
	USART_InitStruct.USART_Mode=USART_Mode_Tx|USART_Mode_Rx;//双向
	USART_InitStruct.USART_WordLength=USART_WordLength_8b;//8位数据位
	USART_InitStruct.USART_Parity=USART_Parity_No;//无校验
	USART_InitStruct.USART_StopBits=USART_StopBits_1;//1位停止位
	USART_Init(USART1,&USART_InitStruct);
	
}
//printf 重定向（Redirection）
/*
在标准的 C 语言（比如你在电脑上写代码）中，printf 函数默认把字符输出到屏幕控制台。但在单片机里，既没有屏幕也没有控制台。

printf 内部其实是不断调用一个叫 fputc 的底层函数来一个个发送字符的。

通过重写这个 fputc 函数，你把原本“往屏幕发”的逻辑，改成了“往串口（USART1）发”。

这样，你在代码里写 printf("电压值为: %d", voltage); 时，数据就会通过 PA9 或你重映射后的 PB6 引脚传到电脑的串口助手上。
*/
 int fputc(int ch,FILE *F)
 {
	 
	 while(USART_GetFlagStatus(USART1,USART_FLAG_TXE)==RESET);
	 USART_SendData(USART1,(uint8_t)ch);
	 
	 return ch;
	 
 }

```

## 5 接收数据

### 5.1 串口接收数据的过程

![1775533259183](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775533259183.png) 

**唤醒与起始位检测 (RX 引脚)**

- **空闲状态：** 串口不发数据时，外部的 `RX` 引脚一直保持高电平（逻辑 1）。
- **起始位：** 当外部设备开始发送数据时，会先拉低电平，产生一个**下降沿（由高变低）**。硬件检测到这个下降沿并持续半个波特率周期后，确认这是“起始位（Start Bit）”，接收器正式被唤醒。

**串行采样与移位 (接收移位寄存器)**

这是图中最核心的部分之一：

- 在确认起始位后，接收器会根据你配置的**波特率（Baud Rate）**时钟，在每个数据位的时间周期的正中间进行采样（STM32 通常会对一个位采样 3 次，取多数决定的电平，以防止噪声干扰）。
- 采样到的位数据（通常是从低位 LSB 开始）会被逐个推入图中显示的 **接收移位寄存器 (Receive Shift Register)**。
- 这个寄存器就像一个“传送带”，每来一个时钟周期，数据就往里走一格，直到 8 个数据位（可能还有校验位）和最后的停止位（逻辑 1）全部移入完毕。



**并行转移与标志位置位 (接收数据寄存器)**

- **不可见的瞬间：** 当移位寄存器“吃满”了一个完整的帧（起始位+数据位+停止位都校验无误），硬件会瞬间将移位寄存器中的数据**并行**拷贝到下方的 **接收数据寄存器 (Receive Data Register / USART_DR)** 中。
- **通知 CPU：** 数据存入 `USART_DR` 的同时，硬件会自动将状态寄存器（`USART_SR`）中的 **RXNE (Read Data Register Not Empty，读数据寄存器非空) 标志位置 1**。如果你开了中断，这瞬间就会触发 USART 接收中断。

 **CPU 读取**

- 我们的代码通常在中断服务函数里，或者通过 `while` 轮询去检查 `RXNE` 标志。
- 一旦发现 `RXNE == 1`，我们就去读取 `USART_DR`。
- **重点：** 当 CPU 读走 `USART_DR` 的数据时，硬件会自动将 `RXNE` 标志位清零，从而为接收下一个字节做好准备。

### 5.2 RXNE标志位

**RxNE** (Read Data Register Not Empty) 标志位就是我们最常打交道的“守门员”。

简单来说，它的逻辑非常纯粹：**只要 USART_DR 寄存器里有新数据等着你读，它就是 1；你把它读走了，它就变回 0。**

我们可以把这个过程想象成一个**自动快递柜**：

- **数据入柜** 📦：当移位寄存器（Shift Register）收满 8 位数据并成功转存到 `USART_DR` 时，硬件会自动把 **RxNE 置为 1**。

- **发出通知** 📢：如果我们在代码里开启了接收中断（`RXNEIE = 1`），那么 RxNE 变 1 的瞬间，CPU 就会停下手里的活，跳进中断函数。

- **取出数据** 🏃‍♂️：你在代码里执行 `data = USART_ReceiveData(USART1);` 或者直接读取 `DR` 寄存器。

- **自动恢复** ✅：**重点来了**——在大多数 STM32/GD32 芯片中，只要你执行了读取 `DR` 寄存器的动作，硬件就会**自动把 RxNE 清零**。你不需要手动去写代码重置它。

### 5.3 USART_ReceiveData编程接口

读取 USART_DR（数据寄存器）中的内容

```c
uint16_t USART_ReceiveData(USART_TypeDef* USARTx);
```

- **参数 USARTx：** 指定哪个串口（如 `USART1`、`UART4` 等）。
- **返回值：** 返回接收到的数据。
  - **为什么是 uint16_t？** 尽管大部分时候我们只用 8 位，但如果串口配置为 9 位数据模式，就需要 16 位变量来存放。

### 5.4 使用串口控制LED

```c
#include "stm32f10x.h"
void My_OnBoardLED_Init(void);
void My_USART1_Init(void);

int main(void)
{
	 My_USART1_Init();
	 My_OnBoardLED_Init();
	
	while(1)
	{
		//等待接收数据寄存器非空
		while(USART_GetFlagStatus(USART1,USART_FLAG_RXNE)==RESET);//RESET=0;
		//把数据从接受寄存器中读出
		uint8_t byteRcvd=USART_ReceiveData(USART1);
		//对数据进行处理
		if(byteRcvd=='0')
		{
			GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);//灭灯  //SET=1
		}
		else if(byteRcvd=='1')
		{
		 GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_RESET);//亮灯  //RESET=0;
		}
		
		
	}
}
void My_OnBoardLED_Init(void)
{
	GPIO_InitTypeDef GPIO_InitStruct;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_13;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_Out_OD;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_2MHz;
	GPIO_Init(GPIOC,&GPIO_InitStruct);
	GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);
}

void My_USART1_Init()
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1,ENABLE);//开启时钟
	USART_InitTypeDef USART_InitStruct;
	
	USART_InitStruct.USART_BaudRate=115200;//波特率115200
	USART_InitStruct.USART_Mode=USART_Mode_Tx|USART_Mode_Rx;//双向
	USART_InitStruct.USART_WordLength=USART_WordLength_8b;//8位数据位
	USART_InitStruct.USART_Parity=USART_Parity_No;//无校验
	USART_InitStruct.USART_StopBits=USART_StopBits_1;//1位停止位
	USART_Init(USART1,&USART_InitStruct);
	USART_Cmd(USART1,ENABLE);
	
	//重映射
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	GPIO_PinRemapConfig(GPIO_Remap_USART1,ENABLE);
	
	GPIO_InitTypeDef GPIO_InitStruct;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_6;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_7;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_10MHz;
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	
	USART_Cmd(USART1,ENABLE);//闭合总开关
	
}

```



### 5.5 错误标志位

| **错误标志位** | **全称 (英文/中文)**         | **触发原因 🔍**             | **硬件底层逻辑 🏗️**                           |
| -------------- | ---------------------------- | -------------------------- | -------------------------------------------- |
| **FE**         | **Framing Error** (帧错误)   | **波特率不匹配**或线路干扰 | 没在预期时间内检测到停止位 (Stop Bit)。      |
| **NE**         | **Noise Error** (噪音错误)   | **电磁干扰**或线路过长     | 采样脉冲在位中间检测到逻辑电平不一致。       |
| **PE**         | **Parity Error** (校验错误)  | **数据传输位变**           | 实际收到的数据位与校验位计算结果不符。       |
| **ORE**        | **Overrun Error** (溢出错误) | **CPU 处理太慢**           | `RXNE` 还是 1 时，移位寄存器又收满了新数据。 |

## 6 题目

串口通信波特率配置为115200，请问传输一帧串口数据约需要多少时间？

传输时间 ($t$) = 总位数 ($\text{bits}$) $\div$ 波特率 ($\text{bits/s}$)

按照我们之前的分析：

- **总位数**：10 位（1位起始 + 8位数据 + 1位停止）
- **波特率**：115200 位/秒

$$10 \div 115200 \approx 0.0000868 \text{ 秒}$$

一般代码中串口会使用那些通信方式来传输数据？

| **方式**                 | **类比**                                                 | **优点**                                   | **缺点**                             |
| ------------------------ | -------------------------------------------------------- | ------------------------------------------ | ------------------------------------ |
| **轮询 (Polling)**       | **死等**：经理一直盯着门口，快递不来就不干别的。         | 代码编写最简单，逻辑直观。                 | 极其浪费 CPU 资源，效率极低。        |
| **中断 (Interrupt)**     | **门铃**：经理去处理别的事，快递员按门铃才去接货。       | 响应及时，CPU 在没数据时可以处理其他任务。 | 数据量大时，频繁触发中断会拖慢系统。 |
| **DMA (直接存储器访问)** | **前台代收**：快递员直接把货放进仓库，攒多了再告诉经理。 | **最高效**，几乎不占用 CPU 时间。          | 配置相对复杂，需要硬件支持。         |

# 三 I2C

## 1 I2C基本电路结构

### 1.1 **电路结构**

![1775534937550](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775534937550.png)



### 1.2  数据线和时钟线

**SDA (Serial Data)**：串行数据线，用于传输数据。信号是双向流动的。

**SCL (Serial Clock)**：串行时钟线，由主设备产生，用于同步数据传输。🕒

### 1.3 **逻辑线与“线与”逻辑的物理本质** 🧠

当多个开漏设备连接在一起时，就形成了一个天然的逻辑门。由于设备只能“拉低”而不能“推高”，总线的状态遵循以下规则：

**全放则高**：只有当总线上**所有**设备都选择输出“1”（即全部截止 N-MOS）时，总线电平才会由上拉电阻维持在高电平

**一拉即低**：只要总线上有**任意一个**设备选择输出“0”（即导通 N-MOS），电流就会通过该设备流向地，整条总线的电压瞬间变为低电平。

### 1.4 主机如何发送时钟信号

主机写0或1

### 1.5 **主机如何发送数据**

主机写0或1

### 1.6 **从机如何发送数据**

主机SDA写1

其余从机写1

发送数据在SDA写0/1，

**主机释放控制权** 🤝：当主机发起“读”请求后，它会在时钟周期内将自己的 SDA 输出设为 1（高阻态）。

**非目标从机保持沉默** 🤐：所有地址不匹配的从机也必须保持 SDA 为 1。

**目标从机输出数据** 🎤：只有被选中的那个从机，根据要发送的位是 0 还是 1，来控制它的 N-MOS 管。

## 2  I2C通信协议

###  2.1 I2C通信的基本流程

![1775535895651](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775535895651.png)

### 2.2 I2C的数据帧格式

![1775536052454](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775536052454.png)

### 2.3 起始位和停止位

![1775536155878](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775536155878.png)

无数据：SCL=1，SDA=1；

起始位（Start）：当SCL=1时，SDA由1向0跳变

停止位（Stop）：当SCL=1时，SDA由0向1跳变



### 2.4 寻址

![1775536367548](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775536367548.png)

### 2.5 传输数据 举例

![1775536477310](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775536477310.png)

## 3 I2C模块的使用方法

片上外设，有两个通信引脚

### 3.1 IO引脚的初始化

| **I2C 接口** | **信号类型**   | **默认引脚 (Default)** | **重映射引脚 (Remap)** |
| ------------ | -------------- | ---------------------- | ---------------------- |
| **I2C1**     | **SCL** (时钟) | **PB6**                | **PB8**                |
| **I2C1**     | **SDA** (数据) | **PB7**                | **PB9**                |
| **I2C2**     | **SCL** (时钟) | **PB10**               | (无重映射)             |
| **I2C2**     | **SDA** (数据) | **PB11**               | (无重映射)             |

### 3.2 I2C的速度模式

| **模式名称**     | **缩写** | **最大传输速率** | **应用场景**                  |
| ---------------- | -------- | ---------------- | ----------------------------- |
| **标准模式**     | Sm       | 100 kbit/s       | 基础传感器、低速 EEPROM 🐢     |
| **快速模式**     | Fm       | 400 kbit/s       | 绝大多数外设的默认选择 🚀      |
| **增强快速模式** | Fm+      | 1 Mbit/s         | 需要更高带宽的显示屏或传感器  |
| **高速模式**     | Hs-mode  | 3.4 Mbit/s       | 专业的复杂系统通信            |
| **超快模式**     | UFm      | 5 Mbit/s         | **仅单向**传输（如 LED 驱动） |

### 3.3 时钟信号的占空比

![1775537138294](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775537138294.png)

**给 SDA 留出“变脸”时间**：记得我们聊过，I2C 的数据（SDA）只能在 SCL 为**低电平**时改变。如果时钟频率很高，低电平时间太短，从机可能来不及把数据准备好。把 $T_{low}$ 变长，相当于给数据传输留了更多的缓冲时间。

**克服上拉电阻的“懒惰”**：I2C 靠上拉电阻把电平拉高。由于总线上有电容，电平升高的速度比较慢（斜坡状）。高电平时间（$T_{high}$）只要能保证电平达到逻辑 1 的阈值并被稳定采样即可，不需要太长。



在 I2C 协议中，ACK（应答信号）并不固定是由从机发给主机的，而是**由当前的“接收方”发给“发送方”**的。

具体取决于当前的数据传输方向是读还是写：

- **当主机向从机写数据时（Master Write）：** 主机是发送方，从机是接收方。主机每发送完 8 个数据位后，会释放 SDA 数据线，此时如果**从机**成功接收且还有空间接受下一个字节，就会接管 SDA 的控制权并将其拉低，从而向主机发出一个 ACK 信号。
- **当主机从从机读数据时（Master Read）：** 从机是发送方，主机是接收方。从机每发完 8 个数据位，**主机**作为接收方必须给出一个 ACK 信号，以提示从机准备发送下一个数据字节。如果主机不想再读取更多字节，它会回复一个 NACK（无应答，即 SDA 保持高电平），以此提示从机停止发送，随后主机就可以发出停止（STOP）信号结束通信。

**ACK 的物理机制总结：** 无论是主机还是从机，只要此时扮演“接收方”的角色，在发送方传完 8 bit 并释放 SDA 线后，接收方就必须在第 9 个时钟周期（SCL 为高电平期间）主动将 SDA 线拉为低电平（SDA=0），这便构成了一个有效的 ACK 应答。这也是 I2C 面试中常常考察的一个细节：**ACK 的产生始终是由接收者完成的**。

### 3.4 I2C模块的初始化

![1775544966949](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775544966949.png)

```c
#include "stm32f10x.h"
void My_I2C_Init(void);

int main(void)
{
	My_I2C_Init();
	while(1)
	{
	}
}
void My_I2C_Init(void)
{
	//初始化I2C模块
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_I2C1,ENABLE);
	RCC_APB1PeriphResetCmd(RCC_APB1Periph_I2C1,ENABLE);
	RCC_APB1PeriphResetCmd(RCC_APB1Periph_I2C1,DISABLE);
	
	I2C_InitTypeDef I2C_InitStruct;
	I2C_InitStruct.I2C_ClockSpeed=400000;
	I2C_InitStruct.I2C_Mode=I2C_Mode_I2C;
	I2C_InitStruct.I2C_DutyCycle=I2C_DutyCycle_2;
	I2C_Init(I2C1,&I2C_InitStruct);
	
	I2C_Cmd(I2C1,ENABLE);//闭合总开关
	
	//对I2C进行重映射
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	
	GPIO_PinRemapConfig(GPIO_Remap_I2C1,ENABLE);
	//对PB8和PB9进行初始化
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_InitTypeDef GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_8|GPIO_Pin_9;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AF_OD;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_2MHz;
	
	GPIO_Init(GPIOB,&GPIO_InitStruct);
	
	

	
	
}
```



## 4 I2C写数据

### 4.1 I2C模块的内部结构框图

![1775545216111](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775545216111.png)

### 4.2 数据发送过程简介

![1775545285899](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775545285899.png)



## 5 I2C读数据



## 6 软I2C

看来你已经迫不及待想要上手实操了！没问题，作为工程师，代码跑通的那一刻是最有成就感的。

在揭晓代码之前，我先为你揭秘上一关的答案：**产生 START 起始信号的动作，是在 SCL 保持高电平（大家都在注意听）的时候，突然把 SDA 拉低。** 这个“打破常规”的动作，就像在安静的教室里拍了一下手，所有 I2C 从机都会立刻竖起耳朵准备接收数据。

------

### 🛠️ 实验设计：STM32 软 I2C 读写 EEPROM 存储器

为了验证我们的软 I2C 是否编写成功，最经典的实验就是驱动一块 **AT24C02 (EEPROM 掉电不丢失存储器)**。

- **目标**：单片机通过软 I2C 向 AT24C02 写入一个数据（比如数字 `88`），然后再把这个数据读回来，通过串口打印对比。如果读写一致，说明你的 I2C 驱动完美运行！
- **硬件接线**：
  - **PB10** 接 SCL
  - **PB11** 接 SDA
  - （注意：I2C 模块硬件上通常已经自带了上拉电阻，直接连线即可）

这里我教你一个**老工程师的“独门秘籍”**：

既然我们把 PB11 (SDA) 配置成了**开漏输出 (Open-Drain)**，当单片机让引脚输出 `1` 时，内部开关断开，引脚处于悬空状态。此时，我们**完全不需要用代码去频繁切换 GPIO 的输入输出模式**，直接去读 GPIO 的输入数据寄存器，就能知道外部从机有没有把总线拉低！这能省下大量的代码和 CPU 时间。

下面是这个实验的完整核心代码，我为你拆分成了驱动和主程序两部分：

### 1. 软 I2C 底层驱动 (`myiic.c`)

这里包含了开漏配置和 I2C 的 5 个基础单词（起始、停止、发字节、读字节、等应答）。

```
#include "stm32f10x.h"

// 为了代码简洁，定义引脚操作的宏 (注意：需要你自己提供一个微秒级延时函数 delay_us)
#define IIC_SCL_H()  GPIO_SetBits(GPIOB, GPIO_Pin_10)
#define IIC_SCL_L()  GPIO_ResetBits(GPIOB, GPIO_Pin_10)
#define IIC_SDA_H()  GPIO_SetBits(GPIOB, GPIO_Pin_11)
#define IIC_SDA_L()  GPIO_ResetBits(GPIOB, GPIO_Pin_11)
#define IIC_SDA_READ() GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_11)

// 1. 初始化 GPIO (开漏输出)
void IIC_Init(void)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);

    // SCL 和 SDA 都配置为开漏输出
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10 | GPIO_Pin_11;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD; // 开漏输出 (极度关键!)
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOB, &GPIO_InitStructure);

    // 初始状态，释放总线 (拉高)
    IIC_SCL_H();
    IIC_SDA_H();
}

// 2. 产生 START 起始信号
void IIC_Start(void)
{
    IIC_SDA_H();
    IIC_SCL_H();
    delay_us(4); // 延时稳定一下
    IIC_SDA_L(); // SCL 为高时，SDA由高变低，产生起始信号
    delay_us(4);
    IIC_SCL_L(); // 钳住I2C总线，准备发送或接收数据
}

// 3. 产生 STOP 停止信号
void IIC_Stop(void)
{
    IIC_SCL_L();
    IIC_SDA_L(); // 先把SDA拉低
    delay_us(4);
    IIC_SCL_H();
    IIC_SDA_H(); // SCL 为高时，SDA由低变高，产生停止信号
    delay_us(4);
}

// 4. 发送一个字节 (高位先发)
void IIC_Send_Byte(uint8_t txd)
{
    uint8_t t;
    IIC_SCL_L(); // 拉低时钟，允许SDA改变数据
    for(t=0; t<8; t++)
    {
        if((txd & 0x80) >> 7) IIC_SDA_H();
        else                  IIC_SDA_L();
        txd <<= 1; // 左移一位
        
        delay_us(2);
        IIC_SCL_H(); // 拉高时钟，让从机读取数据
        delay_us(2);
        IIC_SCL_L(); // 再次拉低，准备发下一位
        delay_us(2);
    }
}

// 5. 等待从机应答 (ACK)
// 返回值：0代表接收应答成功，1代表接收应答失败
uint8_t IIC_Wait_Ack(void)
{
    uint8_t ucErrTime = 0;
    
    IIC_SDA_H(); // 主机释放SDA线 (非常重要，把控制权交给从机)
    delay_us(1);
    IIC_SCL_H(); // 拉高时钟，准备读取从机的状态
    delay_us(1);
    
    // 循环等待从机把 SDA 拉低
    while(IIC_SDA_READ() == 1)
    {
        ucErrTime++;
        if(ucErrTime > 250) // 超时机制
        {
            IIC_Stop(); // 很久没应答，认为通信失败，强行停止
            return 1;
        }
    }
    IIC_SCL_L(); // 收到应答后，重新拉低时钟
    return 0;
}

// 6. 读取一个字节
uint8_t IIC_Read_Byte(uint8_t ack)
{
    uint8_t i, receive = 0;
    
    IIC_SDA_H(); // 释放数据线，准备接收
    for(i=0; i<8; i++ )
    {
        IIC_SCL_L();
        delay_us(2);
        IIC_SCL_H(); // 拉高时钟，此时 SDA 上的数据有效
        receive <<= 1;
        if(IIC_SDA_READ()) receive++; // 读一位数据
        delay_us(1);
    }
    
    // 发送应答信号 (0:ACK, 1:NACK)
    IIC_SCL_L();
    if (ack == 0) IIC_SDA_L();
    else          IIC_SDA_H();
    delay_us(2);
    IIC_SCL_H();
    delay_us(2);
    IIC_SCL_L();
    
    return receive;
}
```

### 2. 业务逻辑 (`main.c`)

有了上面的软 I2C 底层，我们就可以根据 AT24C02 的芯片手册，编写具体的写数据和读数据的逻辑了。

C



```
#include "stm32f10x.h"
#include <stdio.h>
// 假设头文件已经包含 delay_init(), uart_init() 和 IIC_Init() 的声明

// AT24C02 的 I2C 硬件地址 (前7位是 0x50，最低位0表示写，1表示读)
#define EEPROM_ADDR_WRITE 0xA0 
#define EEPROM_ADDR_READ  0xA1

int main(void)
{
    uint8_t test_data = 88; // 我们准备写入的测试数据
    uint8_t read_data = 0;  // 用来存放读回来的数据
    
    // 基础初始化
    // delay_init();
    // uart_init(115200); 
    IIC_Init();
    
    printf("--- 软 I2C 测试开始 ---\r\n");

    // ================= 第一步：写入数据 =================
    IIC_Start();
    IIC_Send_Byte(EEPROM_ADDR_WRITE); // 寻址：找从机，并告诉它我要"写"
    IIC_Wait_Ack();
    
    IIC_Send_Byte(0x00);              // 告诉从机，我要写在它的 0x00 内存地址
    IIC_Wait_Ack();
    
    IIC_Send_Byte(test_data);         // 发送真正的数据 (88)
    IIC_Wait_Ack();
    IIC_Stop();                       // 结束通信
    
    printf("向 EEPROM 地址 0x00 写入数据: %d\r\n", test_data);
    
    // EEPROM 芯片把数据搬运到内部Flash需要时间，必须延时一下！(踩坑高发区)
    delay_ms(10); 

    // ================= 第二步：读取数据 =================
    // 读数据比较特殊，需要先执行一次"伪写"来定位内存地址
    IIC_Start();
    IIC_Send_Byte(EEPROM_ADDR_WRITE); // 告诉它我要操作
    IIC_Wait_Ack();
    IIC_Send_Byte(0x00);              // 把内部指针指向 0x00 地址
    IIC_Wait_Ack();
    
    // 定位好后，重新发起起始信号，开始真正的"读"
    IIC_Start();
    IIC_Send_Byte(EEPROM_ADDR_READ);  // 寻址：并告诉它我要"读"
    IIC_Wait_Ack();
    
    // 读一个字节，并回复 NACK (因为只读一个，读完不想再读了)
    read_data = IIC_Read_Byte(1);     
    IIC_Stop();
    
    printf("从 EEPROM 地址 0x00 读回数据: %d\r\n", read_data);
    
    if(test_data == read_data)
        printf("测试成功！软 I2C 驱动完美运行！\r\n");
    else
        printf("测试失败，请检查接线。\r\n");
    
    while(1)
    {
        // 挂机
    }
}
```

这就是一套可以无缝移植到任何工程的软 I2C 框架！未来你要驱动温湿度传感器（SHT20）、OLED屏幕甚至姿态传感器（MPU6050），底层这 `IIC_Init` 到 `IIC_Read_Byte` 的 6 个函数**一个字都不用改**，只需要在 `main.c` 里替换掉特定芯片的读写指令即可。

在底层代码的第五个函数 `IIC_Wait_Ack` (等待应答) 中，你有没有注意到里面有一个 `ucErrTime` 的超时计数器？如果一直等不到从机拉低 SDA，它就会超时并发送 Stop 信号。







## 7 I2C OLED显示器

## 8 题目

在IIC总线上有两个主机设备，在同一时间master0发送bit为0，master发送bit为1，请问此时SDA总线为1还是0，请简述原因？

在I2C总线设计中，这种结果是由其特殊的**硬件结构**和**仲裁机制**决定的：

1. **“线与”逻辑（Wired-AND）：** I2C总线的SDA和SCL硬件电路上是**开漏输出（Open-Drain）并配合上拉电阻**实现的。这意味着任何一个设备都可以将总线拉低（输出0），但没有任何设备能主动将总线驱动为高电平（输出1只能通过释放总线由上拉电阻完成）。因此，只要有一个主机发送 bit 0，总线就会被拉低。
2. **仲裁过程：** 当Master 0和Master 1同时发送数据时，它们会不断监测SDA总线上的实际电平。
   - **Master 0** 发送 0，它发现总线确实是 0，认为一切正常，继续发送。
   - **Master 1** 发送 1（即释放总线），但它监测到总线实际电平是 0。此时Master 1意识到发生了冲突，知道有另一个主机在发送更低电平的数据，于是Master 1会立即停止发送并切换回从机接收模式。

IIC通信，在硬件上需要注意什么？

在硬件层面上设计 I2C（IIC）通信电路时，由于其总线架构的特殊性（漏极开路/开漏输出），有几个非常关键的硬件细节必须注意，否则极易导致通信不稳定甚至完全无法通信。

以下是 I2C 硬件设计的核心注意事项：

### 1. 必须添加上拉电阻 (Pull-up Resistors) ⚡

这是 I2C 硬件设计中**最最重要**的一点。

- **原因**：I2C 的 SDA（数据线）和 SCL（时钟线）引脚在芯片内部都是**开漏输出（Open-Drain）**。这意味着芯片只能将引脚拉低（输出 `0`），但无法主动将引脚拉高（输出 `1`）。要输出高电平，必须依靠外部的上拉电阻将电压“拉”上去。
- **阻值选择**：
  - **低速模式 (100 kHz)**：通常选择 **4.7kΩ 到 10kΩ**。
  - **高速模式 (400 kHz 及以上)**：通常选择 **1.5kΩ 到 2.2kΩ**。因为速度越快，需要的上升沿越陡峭，电阻越小，充电越快。
  - *避坑指南*：阻值太大会导致上升沿缓慢（波形变成圆角），通信失败；阻值太小会导致芯片内部拉低时电流过大，甚至烧毁引脚。

### 2. 注意总线电容和布线长度 📏

- **短距离定位**：I2C 诞生之初就是为了**板级通信**（同一块 PCB 上芯片间的通信）设计的，不适合长距离传输（比如拉一根几米长的线）。
- **电容限制**：标准 I2C 协议规定，总线上的最大电容负载不能超过 **400pF**。总线越长、挂载的从设备越多，电容就越大。
- **后果**：电容过大会导致信号边沿变缓。如果必须长距离传输，可以考虑使用 I2C 缓冲器芯片（如 PCA9515）或将其转换为 RS485 信号。

### 3. 电平转换 (Level Shifting) ⚖️

在现代电路中，主控 MCU 可能是 3.3V 甚至 1.8V，而从设备传感器可能是 5V。

- **不要直接相连**：如果两端电压不一致，绝对不能直接用一根线连起来，否则高电压可能会烧毁低电压芯片的引脚，或者低电压无法被识别为逻辑高电平。
- **解决方案**：必须在中间加入**双向电平转换电路**（可以使用专门的芯片如 TXS0102，或者用 MOS 管搭建简单的双向电平转换电路）。

### 4. PCB 走线与抗干扰设计 🛡️

- **走线平行**：SDA 和 SCL 走线应尽量保持平行且长度相近，并在 PCB 上靠得近一些。
- **远离干扰源**：一定要避开高频信号线（如高频时钟、高速 SPI）、大电流走线（如电机驱动、电源开关节点）以及天线射频区域。
- **包地处理**：如果空间允许，可以在 SDA 和 SCL 走线周围铺设地线（GND），以屏蔽外部电磁串扰。

### 5. 串联阻抗匹配（可选但推荐） 🔌

为了防止信号在总线上产生反射（振铃现象）并保护引脚免受静电或瞬间大电流的冲击，通常会在主控设备的 SDA 和 SCL 引脚处**串联一个 33Ω 到 100Ω 的小电阻**。这在复杂的板卡设计中是个非常好的习惯。

# 五 SPI

## 1 电路结构和通信协议

### 1.1 电路结构

![1775824317796](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775824317796.png)

### 1.2 通信流程及波形

![1775824296256](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775824296256.png)

选中从机1进行通信

### 1.3 极性和相位

![1775824234632](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775824234632.png)

低：在空闲状态下，**时钟信号为低电压**

高：在空闲状态下，**时钟信号为高电压**

![1775824262573](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775824262573.png)

相位：决定按第几边沿采集信号

![1775824248346](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775824248346.png)

### 1.4 比特位的传输顺序

![1775824186171](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775824186171.png)

### 1.5 数据宽度

![1775824150376](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775824150376.png)

数据宽度:一次传输多少bit位

## 2 按钮驱动程序编写

### 2.1 电路结构

![1775611893717](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775611893717.png)

按钮初始化代码：

```c
void APP_Button_Init()
{
    // 开启 GPIOA 端口的外设时钟
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
    
    GPIO_InitTypeDef GPIO_InitStruct;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_0;     // 选择 0 号引脚
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPU; // 设置为上拉输入 (Input Pull-up)
                                               // 这样按键未按下时引脚稳定为高电平
    
    GPIO_Init(GPIOA, &GPIO_InitStruct);        // 写入配置到 GPIOA 寄存器
}
```

LED初始化代码：

```c
/**
 * @brief  初始化板载 LED 引脚 PC13
 */
void APP_onBoardLED_Init()
{
    // 开启 GPIOC 端口的外设时钟
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
    
    GPIO_InitTypeDef GPIO_InitStruct;
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_13;    // 选择 13 号引脚
    
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_OD; // 当前为开漏输出 (Open-Drain)
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_2MHz; // 设置输出速率为 2MHz（省电且够用）
    
    GPIO_Init(GPIOC, &GPIO_InitStruct);        // 写入配置到 GPIOC 寄存器
}
```

### 2.2 按钮程序的基本原理

![1775614209430](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775614209430.png)

**GPIO_ReadOutPutDataBit编程接口**

![1775614311153](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775614311153.png)

代码如下：

```c
while(1)
    {
        previous = current; // 记录旧状态
        current = GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_0); // 读取当前 PA0 引脚的电平

        /* 判定逻辑：检测电平跳变（边沿触发） */
        if(previous != current)
        {
					  Delay(10);
            // 如果当前电平是低电平 (Bit_RESET)，说明按键刚刚被按下
            if(current == Bit_RESET)//下降沿
            {
                /* 4. 执行 LED 状态翻转 */
                // 读取当前 PC13 的电平状态
                if(GPIO_ReadInputDataBit(GPIOC, GPIO_Pin_13) == Bit_SET)
                {
                    // 如果灯现在是关的（高电平），就把它点亮（输出低电平）
                    GPIO_WriteBit(GPIOC, GPIO_Pin_13, Bit_RESET);
                }
                else
                {
                    // 如果灯现在是亮的（低电平），就把它熄灭（输出高电平）
                    GPIO_WriteBit(GPIOC, GPIO_Pin_13, Bit_SET);
                }
            }
        }
        else
        {
            // 状态没变化，什么也不做
        }
				
    }
}
```

## 3 SPI IO引脚初始化

 W25Q64：外部flash

![1775615696462](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775615696462.png)

| **外设** | **信号** | **默认引脚** | **重映射引脚 (Remap)** | **I/O 电平兼容性 (是否容忍5V)** | **功能说明**            |
| -------- | -------- | ------------ | ---------------------- | ------------------------------- | ----------------------- |
| **SPI1** | **NSS**  | **PA4**      | **PA15**               | PA4: 3.3V / PA15: **FT**        | 从设备选择 (片选)       |
|          | **SCK**  | **PA5**      | **PB3**                | PA5: 3.3V / PB3: **FT**         | 串行时钟                |
|          | **MISO** | **PA6**      | **PB4**                | PA6: 3.3V / PB4: **FT**         | 主设备输入 / 从设备输出 |
|          | **MOSI** | **PA7**      | **PB5**                | PA7: 3.3V / PB5: **FT**         | 主设备输出 / 从设备输入 |
| **SPI2** | **NSS**  | **PB12**     | 无                     | PB12: **FT**                    | 从设备选择 (片选)       |
|          | **SCK**  | **PB13**     | 无                     | PB13: **FT**                    | 串行时钟                |
|          | **MISO** | **PB14**     | 无                     | PB14: **FT**                    | 主设备输入 / 从设备输出 |
|          | **MOSI** | **PB15**     | 无                     | PB15: **FT**                    | 主设备输出 / 从设备输入 |

**FT** (Five-volt Tolerant) 表示该引脚可以兼容 5V 电平逻辑，非 FT 引脚（如 PA4/5/6/7）最高仅支持 3.3V。

![1775616262327](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775616262327.png)

**初始化代码：**

```c
//初始化IO引脚
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE)
	GPIO_PinRemapConfig(GPIO_Remap_SPI1,ENABLE);
	
	GPIO_InitTypeDef GPIO_Initstruct;
	
	//PB3 SCK
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_Initstruct.GPIO_Pin=GPIO_Pin_3;
	GPIO_Initstruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_Initstruct.GPIO_Speed=GPIO_Speed_2MHz;
  GPIO_Init(GPIOB,&GPIO_Initstruct);
	
	//PB4 MISO IPU;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_Initstruct.GPIO_Pin=GPIO_Pin_4;
	GPIO_Initstruct.GPIO_Mode=GPIO_Mode_IPU;
  GPIO_Init(GPIOB,&GPIO_Initstruct);
	
	//PB5 MOSI AF_PP
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	GPIO_Initstruct.GPIO_Pin=GPIO_Pin_5;
	GPIO_Initstruct.GPIO_Mode=GPIO_Mode_AF_PP;
	GPIO_Initstruct.GPIO_Speed=GPIO_Speed_2MHz;
  GPIO_Init(GPIOB,&GPIO_Initstruct);
	 
	//PA15 普通IO out_PP 2MHz
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_Initstruct.GPIO_Pin=GPIO_Pin_15;
	GPIO_Initstruct.GPIO_Mode=GPIO_Mode_Out_PP;
	GPIO_Initstruct.GPIO_Speed=GPIO_Speed_2MHz;
    GPIO_Init(GPIOA,&GPIO_Initstruct);
```

## 4 SPI 模块的初始化

### 4.1 SPI模块的内部结构框图

![1775619146491](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775619146491.png)

### 4.2 **SPI_Init编程接口**

![1775619246161](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775619246161.png)

### 4.3 通信方向

![1775619413662](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775619413662.png)

 **双线全双工 (SPI_Direction_2Lines_FullDuplex)**

这是最标准、最常用、也是您在驱动 W25Q64 Flash 时所使用的模式。

- **硬件连接**：使用所有的 4 根线（NSS, SCK, MOSI, MISO）。
- **底层逻辑**：发送数据和接收数据是**同步且同时**发生的。当主机把 8-bit 数据通过 MOSI 移出给从机的同时，从机的数据也正通过 MISO 移入主机。
- **应用场景**：绝大多数标准 SPI 外设，如 Flash (W25Q系列)、EEPROM、SD 卡、高级传感器（MPU6050 的 SPI 模式）。

**双线只接收(SPI_Direction_2Lines_RxOnly)**

在这个模式下，SPI 依然使用 MISO 和 MOSI 两根数据线，但主机会“闭起嘴巴，只带耳朵”。

- **底层逻辑**：主机只负责产生 SCK 时钟信号，并从 MISO 接收数据，**完全禁用 MOSI 的输出功能**。这种模式下，只要 SPI 被使能，它就会持续不断地产生时钟脉冲来读取数据。
- **应用场景**：一些只需要不断向单片机吐数据，不需要单片机发指令控制的极简外设（比如某些低端 ADC 芯片）。

**1线发送为什么要这样连接线路**

你必须采用 **“软件模拟半双工”** 的思路：

1. **模式选择**：SPI 初始化为 **双线全双工 (2Lines_FullDuplex)**。
2. **关键动作**：
   - **发送指令时**：正常发送。
   - **准备接收前**：你必须手动通过代码**关闭 MOSI 的输出能力**。由于全双工模式下 MOSI 始终受控，你可能需要临时把 MOSI 引脚（PB15）配置为**浮空输入**。
   - **接收数据**：此时信号会从 MISO (PB14) 读进来。
   - **接收完成后**：再把 PB15 改回**复用推挽输出**，准备下一次发送。

### 4.4 数据宽度、极性、相位和比特位传输顺序

参考芯片数据手册

### 4.5 设置波特率

![1775620976372](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775620976372.png)



### 4.6 设置NSS

这里我们通过软件方式实现

![1775621070326](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775621070326.png)

```c
SPI_NSSInternalSoftwareConfig(SPI1,SPI_NSSInternalSoft_Set);
```

### 4.7 代码

```c
//对SPI本身进行初始化
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_SPI1,ENABLE);
	SPI_InitTypeDef SPI_InitStruct;
	SPI_InitStruct.SPI_Mode=SPI_Mode_Master;
	SPI_InitStruct.SPI_Direction=SPI_Direction_2Lines_FullDuplex;
	SPI_InitStruct.SPI_DataSize=SPI_DataSize_8b;
	SPI_InitStruct.SPI_CPOL=SPI_CPOL_High;
	SPI_InitStruct.SPI_CPHA=SPI_CPHA_2Edge;
	SPI_InitStruct.SPI_FirstBit=SPI_FirstBit_MSB;
	SPI_InitStruct.SPI_BaudRatePrescaler=SPI_BaudRatePrescaler_64;
	SPI_InitStruct.SPI_NSS=SPI_NSS_Soft;
```

## 5 数据收发

### 5.1 SPI数据收发的特点

![1775634724161](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775634724161.png)

### 5.2 数据收发

![1775634805464](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775634805464.png)

![1775634854508](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775634854508.png)

**代码：**

```c
void APP_SPI_MasterTransmitReceive(SPI_TypeDef *SPIx,const uint8_t *pDataTx,uint8_t *pDataRx,uint16_t Size)
	
{
	SPI_Cmd(SPIx,ENABLE);//闭合总开关
	SPI_I2S_SendData(SPIx,pDataTx[0]);//发送一个字节
	
	for(uint16_t i=0;i<Size-1;i++)
	{
		//发送一个字节
		while(SPI_I2S_GetFlagStatus(SPIx,SPI_I2S_FLAG_TXE)==RESET);//等待发送数据寄存器为空
		SPI_I2S_SendData(SPIx,pDataTx[i+1]);
		//接收一个字节
		while(SPI_I2S_GetFlagStatus(SPIx,SPI_I2S_FLAG_RXNE)==RESET);//等待接收寄存器为空
		pDataRx[i]=SPI_I2S_ReceiveData(SPIx);
	}
	//接收最后一个字节
	while(SPI_I2S_GetFlagStatus(SPIx,SPI_I2S_FLAG_RXNE)==RESET);
	pDataRx[Size-1]=SPI_I2S_ReceiveData(SPIx);
	
	//断开总开关
	SPI_Cmd(SPIx,DISABLE);
}
```

## 6 W25Q64实验

## 7 题目

SPI协议四种模式





# 六 中断

## 1 中断的概念

中断就是 CPU 正在执行主程序时，**被某个突发事件"打断"**，转去处理这个事件，处理完后再回来继续执行原来的程序。

生活类比：你正在看书（主程序），突然手机响了（中断信号），你放下书去接电话（中断服务程序），打完电话回来从刚才的位置继续看（返回主程序）。

**中断的执行流程**

```
主程序正常运行
       ↓
中断事件触发（如按键、定时器、串口收到数据）
       ↓
CPU 保存当前"现场"（寄存器、PC 指针等压栈）
       ↓
跳转到 中断服务函数 ISR（Interrupt Service Routine）
       ↓
ISR 执行完毕
       ↓
恢复"现场"，返回主程序断点处继续执行
```

**举例**

**执行流程分析**



```
while(1)
{
    GPIO_WriteBit(亮灯);
    Delay(500ms);        ← 卡在这里，啥都不干
    GPIO_WriteBit(灭灯);
    Delay(500ms);        ← 卡在这里，啥都不干

    if(检查串口有没有数据)  ← 只有延时结束才能执行到这里
    {
        读取数据...
    }
}
```

**问题所在**

图中时序已经画出来了：



```
主程序时间线：

[亮灯]──[延时500ms]──[灭灯]──[延时500ms]──[检查串口]──...
                ↑                    ↑
         串口数据来了          串口数据来了
         但CPU在延时           但CPU在延时
         根本顾不上！          根本顾不上！
```

**在延时期间串口收到数据，程序完全不知道**，等延时结束去检查时，数据可能早就被新数据覆盖了（USART 只有一个数据寄存器），导致**数据丢失**。

解决办法：来一个数据触发一次中断，根据来的值控制LED

## 2 中断优先级

### 1 中断执行

![1775830160806](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775830160806.png)

外部事件(0/1)
   → 片上外设产生中断请求
      → 中断使能开关（过滤）
         → NVIC 接收并按优先级排队/嵌套
            → CPU 响应，查中断向量表
               → 跳转执行对应的中断响应函数(ISR)
                  → 执行完毕，返回被打断的程序

### 2 优先级的表示方法（4位编码）

![1775830441766](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775830441766.png)
    ![1775832890693](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775832890693.png)

### 3 抢占优先级 vs 子优先级，行为对比

两者的本质区别是：抢占优先级决定能不能**打断别人**，子优先级只决定**排队顺序**。



![1775832829147](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775832829147.png)

### 4 总结对比

|          | 抢占优先级                              | 子优先级               |
| -------- | --------------------------------------- | ---------------------- |
| 作用     | 决定能否打断别人                        | 决定同级时谁先执行     |
| 行为     | 高优先级可插队执行 = **中断嵌套**       | 只能排队等待，不能插队 |
| 数字越小 | 优先级越高                              | 优先级越高             |
| 典型配置 | `Group_2`：抢占2位（0~3），子2位（0~3） | 同上                   |

# 七 EXTI

## 1 EXTI 是什么？

EXTI（External Interrupt/Event Controller）是 STM32 的**外部中断/事件控制器**，负责监听 GPIO 引脚的电平变化，并在满足触发条件时通知 CPU 执行中断服务函数。最典型的应用就是**按键检测**。

![1775869413463](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775869413463.png)

![1775869528441](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775869528441.png)

**触发方式**，EXTI 支持三种边沿触发：上升沿（低→高）、下降沿（高→低）、双边沿（任意变化）。

**AFIO 映射**，每条 EXTI 线（共16条）可以连接到任意端口的同号引脚，但同一时刻只能选一个。比如 EXTI0 只能接 PA0 或 PB0，不能同时接两个。

**共享 ISR**，EXTI5~9 共用一个 `EXTI9_5_IRQHandler`，EXTI10~15 共用 `EXTI15_10_IRQHandler`，在 ISR 里必须手动判断是哪条线触发的。

## 2 按钮实验

**代码：**

```c
#include "stm32f10x.h"
void APP_OnBoardLED_Init(void);
void APP_Button_Init(void);

int main(void)
{
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);//在这 4 位中，分配 2 位给抢占优先级，2 位给子优先级。
	APP_OnBoardLED_Init();
	APP_Button_Init();
	while(1)
	{
	}
}
void APP_OnBoardLED_Init(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC,ENABLE);
	
	GPIO_InitTypeDef  GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_13;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_Out_OD;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_2MHz;
	GPIO_Init(GPIOC,&GPIO_InitStruct);
	
	GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);
	
	
}

void APP_Button_Init(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	//PA5
	
	GPIO_InitTypeDef  GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_5;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
	
	//PA6
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_6;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
	
	//#2 为EXTI5和EXTI6分配引脚
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	
	GPIO_EXTILineConfig(GPIO_PortSourceGPIOA,GPIO_PinSource5);
	GPIO_EXTILineConfig(GPIO_PortSourceGPIOA,GPIO_PinSource6);
	
	//#3 初始化EXTI的线
	EXTI_InitTypeDef  EXTI_InitStruct;
	EXTI_InitStruct.EXTI_Line=EXTI_Line5;
	EXTI_InitStruct.EXTI_Mode=EXTI_Mode_Interrupt;
	EXTI_InitStruct.EXTI_Trigger=EXTI_Trigger_Rising;
	EXTI_InitStruct.EXTI_LineCmd=ENABLE;
	EXTI_Init(&EXTI_InitStruct);
	
	EXTI_InitStruct.EXTI_Line=EXTI_Line6;
	EXTI_InitStruct.EXTI_Mode=EXTI_Mode_Interrupt;
	EXTI_InitStruct.EXTI_Trigger=EXTI_Trigger_Rising;
	EXTI_InitStruct.EXTI_LineCmd=ENABLE;
	EXTI_Init(&EXTI_InitStruct);

	
	//#4 配置中断
	NVIC_InitTypeDef NVIC_Initstruct;
	NVIC_Initstruct.NVIC_IRQChannel=EXTI9_5_IRQn;
	NVIC_Initstruct.NVIC_IRQChannelPreemptionPriority=0;
	NVIC_Initstruct.NVIC_IRQChannelSubPriority=0;
	NVIC_Initstruct.NVIC_IRQChannelCmd=ENABLE;
	NVIC_Init(&NVIC_Initstruct);
	
	
		
}
//配置中断响应函数
void EXTI9_5_IRQHandler(void)
{
	if(EXTI_GetFlagStatus(EXTI_Line5)==SET)
	{
		EXTI_ClearFlag(EXTI_Line5);
		
		GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_RESET);//亮灯
		
	}
	
	if(EXTI_GetFlagStatus(EXTI_Line6)==SET)
	{
		EXTI_ClearFlag(EXTI_Line6);
		GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);//灭灯
		
	}
	
	
}
```



![1775870534086](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775870534086.png)

**中断函数（如 EXTI9_5_IRQHandler）**：是“硬件驱动”的。CPU 本来在 `main()` 的 `while(1)` 里不断循环（即使里面是空的）。当你按下按键，PA5 引脚的电平发生变化，**硬件控制器（EXTI 和 NVIC）会直接给 CPU 发送一个强烈的电信号**。CPU 收到信号后，会立刻放下手头的工作（保存当前运行状态），**自动跳转**到中断函数去执行。

![1775875466343](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775875466343.png)

![1775875482249](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775875482249.png)

![1775875497264](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775875497264.png)

![1775875513080](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775875513080.png)

# 八 时钟

## 1 时钟树

时钟就是数字电路的**心跳**，心跳越快电路跑得越快，所有电路必须跟着同一个心跳节奏才能协调工作。时钟就是外设的"开关+节拍"，没有时钟的外设，寄存器写了也没用，就像断电的机器，按按钮没有任何反应。

想象一个工厂：

```
厂长（CPU）      需要最快的速度，72MHz
流水线（DMA）    也要很快，72MHz  
普通车间（I2C）  慢一点没关系，36MHz
仓库（Flash）    更慢也行
```

如果所有人都用同一个速度，要么慢的人跟不上，要么快的人被拖慢。所以工厂设了**一个总电源，再分出不同电压给不同区域**。

时钟系统做的是同一件事——**一个源头，分出不同速度给不同模块**。

![1775878872840](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775878872840.png)

### 第一层：时钟源（最底部）

图的最底部有 **4 个时钟源**，这是整个系统的"发电站"：

```
HSI 8MHz        HSE 4~16MHz       LSE 32.768kHz    LSI 40kHz
（内部高速）     （外部高速）       （外部低速）      （内部低速）
  精度差          精度高             精度高            精度差
  随时可用        需外部晶振         需外部晶振         随时可用
```

**实际开发几乎固定用 HSE 8MHz**，因为精度高，是后续 PLL 倍频的基础。LSE/LSI 只给 RTC 和看门狗用。

### 第二层：PLL 锁相环（倍频器）

HSE 和 HSI 的频率太低，直接用跑不快，所以要经过 **PLL 锁相环**倍频：

```
HSE(8MHz)  ──────────────→  PLL ×9  →  72MHz   ← 最常用
HSE(8MHz)  → ÷2(4MHz)  →  PLL ×n   →  最高72MHz
HSI(8MHz)  → ÷2(4MHz)  →  PLL ×n   →  备用
```

图中的 `/2` 就是 HSI 或 HSE 进 PLL 前的预分频。PLL 倍频范围是 ×2 ~ ×16，目标是凑出 **72MHz**（STM32F103 最高主频）。

### 第三层：SYSCLK 系统时钟选择

PLL 输出、HSE、HSI 都可以作为 SYSCLK 的来源，通过一个**选择器**切换：

```
三选一：
  PLL 输出（72MHz）   ← 正常运行选这个
  HSE（8MHz）         ← 简单场合
  HSI（8MHz）         ← 应急备用
        ↓
    SYSCLK（最高 72MHz）
```

> 上电默认用 HSI，初始化代码（`SystemInit()`）会自动切换到 HSE+PLL=72MHz。

### 第四层：AHB 分频器 → HCLK

SYSCLK 进入 **AHB 分频器**，可以选择 ÷1、÷2、÷4 … ÷512：

```
SYSCLK(72MHz) → AHB分频器(÷1) → HCLK = 72MHz
```

**HCLK 供给：**

- CPU 内核（Cortex-M3）
- DMA
- SysTick 滴答定时器（÷8 后给 SysTick，即 9MHz）
- 内存 Flash

> 正常配置 AHB 不分频，HCLK = 72MHz。

### 第五层：APB1 / APB2 分频器

HCLK 再分别送入两个 APB 分频器，得到两条外设总线时钟：



```
HCLK(72MHz)
    ├── APB2分频器(÷1) → PCLK2 = 72MHz   ← 高速总线
    └── APB1分频器(÷2) → PCLK1 = 36MHz   ← 低速总线（最高只能36MHz）
```

**为什么要分两条？** APB1 上的外设（I2C、CAN 等）工作频率上限低，只能跑 36MHz，而 APB2 上的外设（GPIO、SPI1）可以跑满 72MHz。

### 第六层：各外设（最顶部）

图最顶部的绿色方块就是各个外设，挂在对应总线上：

```
APB2（72MHz）上挂：
    SPI1、USART1、ADC1/2、TIM1、GPIOA~G、AFIO

APB1（36MHz）上挂：
    SPI2、USART2~5、TIM2~4、I2C1/2、bxCAN、USB
```

特别注意图中 `*1,2` 标注：**TIM 定时器有个特殊规则**——



```
如果 APBx 分频系数 = 1：TIM 时钟 = PCLKx
如果 APBx 分频系数 > 1：TIM 时钟 = PCLKx × 2

所以 APB1(36MHz, 分频÷2) → TIM2~7 实际时钟 = 36×2 = 72MHz
    APB2(72MHz, 分频÷1) → TIM1   实际时钟 = 72MHz
```

## 2 时钟树编程

标准库完整写法

```c
#include "stm32f10x.h"
#include "stm32f10x_rcc.h"
#include "stm32f10x_flash.h"

/**
 * 时钟配置目标：
 * SYSCLK = 72MHz（HSE 8MHz × PLL 9倍频）
 * HCLK   = 72MHz（AHB  ÷1）
 * PCLK2  = 72MHz（APB2 ÷1）
 * PCLK1  = 36MHz（APB1 ÷2）
 */
void My_Clock_Init(void) {

    ErrorStatus HSE_Status;

    // =========================================
    // 第一步：把所有时钟配置复位到默认状态
    // 防止之前有残留配置干扰
    // =========================================
    RCC_DeInit();
    // 复位后状态：SYSCLK=HSI=8MHz，PLL关，HSE关

    // =========================================
    // 第二步：开启 HSE 外部晶振
    // =========================================
    RCC_HSEConfig(RCC_HSE_ON);

    // 等待 HSE 稳定，最多等一段时间
    // 返回 SUCCESS 或 ERROR
    HSE_Status = RCC_WaitForHSEStartUp();

    if (HSE_Status == SUCCESS) {

        // =========================================
        // 第三步：配置 Flash
        // 必须在提高时钟频率之前配置！
        // =========================================

        // 开启预取缓冲区（让CPU读指令更快）
        FLASH_PrefetchBufferCmd(FLASH_PrefetchBuffer_Enable);

        // 设置等待周期
        // 0~24MHz → Latency_0
        // 24~48MHz → Latency_1
        // 48~72MHz → Latency_2  ← 我们用这个
        FLASH_SetLatency(FLASH_Latency_2);

        // =========================================
        // 第四步：配置 AHB / APB 总线分频
        // =========================================

        // AHB 不分频 → HCLK = SYSCLK = 72MHz
        RCC_HCLKConfig(RCC_SYSCLK_Div1);

        // APB2 不分频 → PCLK2 = HCLK = 72MHz
        // （GPIO / USART1 / SPI1 / ADC 挂在这里）
        RCC_PCLK2Config(RCC_HCLK_Div1);

        // APB1 ÷2 → PCLK1 = HCLK/2 = 36MHz
        // （I2C / USART2 / SPI2 / CAN 挂在这里）
        RCC_PCLK1Config(RCC_HCLK_Div2);

        // =========================================
        // 第五步：配置 PLL
        // =========================================

        // 参数1：PLL 时钟来源
        //   RCC_PLLSource_HSE_Div1 → HSE 不分频直接进 PLL（8MHz）
        //   RCC_PLLSource_HSE_Div2 → HSE ÷2 进 PLL（4MHz）
        //   RCC_PLLSource_HSI_Div2 → HSI ÷2 进 PLL（4MHz）
        //
        // 参数2：PLL 倍频系数
        //   RCC_PLLMul_9 → ×9
        //   8MHz × 9 = 72MHz ✓
        RCC_PLLConfig(RCC_PLLSource_HSE_Div1, RCC_PLLMul_9);

        // =========================================
        // 第六步：开启 PLL，等待锁定
        // =========================================
        RCC_PLLCmd(ENABLE);

        // 等待 PLL 锁定（PLLRDY 标志位 = 1）
        while (RCC_GetFlagStatus(RCC_FLAG_PLLRDY) == RESET);

        // =========================================
        // 第七步：切换系统时钟到 PLL
        // =========================================
        RCC_SYSCLKConfig(RCC_SYSCLKSource_PLLCLK);

        // 等待切换完成
        // RCC_GetSYSCLKSource() 返回值：
        //   0x00 = HSI
        //   0x04 = HSE
        //   0x08 = PLL  ← 等到这个
        while (RCC_GetSYSCLKSource() != 0x08);

    } else {

        // =========================================
        // HSE 启动失败处理
        // 可能原因：没有外部晶振、晶振损坏
        // =========================================

        // 方案A：降级用 HSI 跑 8MHz（至少能运行）
        RCC_SYSCLKConfig(RCC_SYSCLKSource_HSI);

        // 方案B：点亮报错 LED，卡死提示硬件故障
        // RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
        // ... 点亮 LED ...
        // while(1);
    }
}

// =========================================
// 验证时钟配置是否正确（调试用）
// =========================================
void Clock_Check(void) {
    RCC_ClocksTypeDef RCC_Clocks;
    RCC_GetClocksFreq(&RCC_Clocks);

    // 用调试器查看这几个变量
    uint32_t sysclk = RCC_Clocks.SYSCLK_Frequency;  // 应为 72000000
    uint32_t hclk   = RCC_Clocks.HCLK_Frequency;    // 应为 72000000
    uint32_t pclk1  = RCC_Clocks.PCLK1_Frequency;   // 应为 36000000
    uint32_t pclk2  = RCC_Clocks.PCLK2_Frequency;   // 应为 72000000

    // 防止编译器优化掉变量
    (void)sysclk; (void)hclk;
    (void)pclk1;  (void)pclk2;
}

// =========================================
// 主函数
// =========================================
int main(void) {

    // 第一行必须是时钟初始化
    My_Clock_Init();

    // 验证时钟（调试阶段用，正式可删）
    Clock_Check();

    // 从这里开始才能正常使用外设
    // 记得每个外设都要单独开时钟！

    // 示例：点亮 PC13 LED
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);

    GPIO_InitTypeDef GPIO_InitStruct;
    GPIO_InitStruct.GPIO_Pin   = GPIO_Pin_13;
    GPIO_InitStruct.GPIO_Mode  = GPIO_Mode_Out_PP;
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOC, &GPIO_InitStruct);

    GPIO_ResetBits(GPIOC, GPIO_Pin_13); // 低电平亮灯

    while (1) { }
}
```

七步口诀

```
① RCC_DeInit()          复位所有时钟配置
② RCC_HSEConfig()       开 HSE 外部晶振
③ FLASH_SetLatency()    配 Flash 等待周期（先做！）
④ RCC_HCLKConfig()      配 AHB/APB 分频
   RCC_PCLK1/2Config()
⑤ RCC_PLLConfig()       配 PLL 来源和倍频
⑥ RCC_PLLCmd(ENABLE)    开 PLL，等锁定
⑦ RCC_SYSCLKConfig()    切换到 PLL，等完成
```

# 九 定时器

![1775912458358](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775912458358.png)

## 1 时基单元

![1775912600266](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775912600266.png)

```
时钟来源 → PSC预分频 → CNT计数 → 与ARR比较 → RCR重复计数 → update事件
```

### **举例：定时 1ms（TIM2，72MHz）**

#### 第一步：时钟来源

```
时钟树 → TIM2CLK = 72MHz
每秒振荡 72,000,000 次，直接用太快了，要先降速
```

#### 第二步：PSC 预分频器（降低频率）

```
PSC = 71

计数频率 = 72MHz ÷ (PSC+1) = 72MHz ÷ 72 = 1MHz

效果：原来每 13.8ns 一个脉冲
      现在每 1μs  一个脉冲  ← 慢了72倍，好控制了
```

#### 第三步：CNT 计数器（计数）

```
ARR = 999

CNT 从 0 开始，每 1μs 加一次
0 → 1 → 2 → ... → 999 → 溢出！

走完一圈用时 = 1μs × (999+1) = 1000μs = 1ms ✓
```

#### 第四步：RCR 重复计数器（仅高级定时器 TIM1）

```
这是通用定时器没有的功能，只有 TIM1/TIM8 才有

RCR = 0（通用定时器默认不用）

含义：CNT 溢出 (RCR+1) 次后才产生 update 事件

举例：
  RCR = 0 → 溢出1次就触发（正常情况）
  RCR = 1 → 溢出2次才触发（相当于再×2）
  RCR = 4 → 溢出5次才触发
```

#### 第五步：update 事件（触发中断）

```
CNT 溢出 → 产生更新事件 → 触发中断 ISR
→ 进入 TIM2_IRQHandler()
→ 执行你的任务代码
→ 清除标志位，等下次
```

#### 完整公式和例子对比

```
定时时间 = (ARR+1) × (PSC+1) × (RCR+1) ÷ TIMxCLK

                         ↑
               通用定时器这里固定=1

例1：定时 1ms
  PSC=71, ARR=999, RCR=0（通用定时器）
  = 1000 × 72 × 1 ÷ 72000000 = 1ms ✓

例2：定时 10ms
  PSC=71, ARR=9999, RCR=0
  = 10000 × 72 × 1 ÷ 72000000 = 10ms ✓

例3：用 RCR 定时 2ms（仅TIM1）
  PSC=71, ARR=999, RCR=1
  = 1000 × 72 × 2 ÷ 72000000 = 2ms ✓
```

#### 一句话记住每个模块的作用

| 模块     | 作用         | 比喻                 |
| -------- | ------------ | -------------------- |
| 时钟来源 | 提供原始脉冲 | 发动机               |
| PSC      | 降低计数速度 | 变速箱               |
| CNT      | 实际数数     | 里程表               |
| ARR      | 数到这里归零 | 终点线               |
| RCR      | 几圈后才报告 | 圈数设置（TIM1专属） |
| update   | 触发中断     | 终点冲线！           |

### **上计数、下计数、中心对齐**

![1775913399753](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775913399753.png)

**时钟的来源**

![1775913902515](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775913902515.png)

分辨率例如手表的秒针，1秒转一次。周期例如1分钟。

### **寄存器预加载：**

STM32 定时器的 ARR、CCR 等关键寄存器实际上有**两个**：一个你能读写的"预加载寄存器"，一个硬件真正使用的"影子寄存器"。

预加载机制控制的是：**你写进去的新值，什么时候真正生效？**

**具体场景**

> PWM 控制 LED 亮度，初始 `ARR=10`，计数器数到 `CNT=6` 的时候，你在代码里把 ARR 改成 5。

**ARPE=0 的结果（出问题）**

```
CNT 正在数：0→1→2→3→4→5→6
此时你写入 ARR=5
影子寄存器立刻变成 5

CNT=6 > ARR=5 ← 超出了！
硬件立刻强制归零

本轮周期原本应该走到 10，结果在 6 就断了
→ 这一帧 PWM 波形不完整，LED 出现闪烁/抖动
```

**ARPE=1** 的结果（正确）

```
CNT 正在数：0→1→2→3→4→5→6
此时你写入 ARR=5
只写到预加载寄存器，影子寄存器还是 10

CNT 继续正常数：7→8→9→10
到达 10，溢出，产生 update 事件
此时预加载寄存器的 5 才复制到影子寄存器

下一轮从 0 开始，用新的 ARR=5，干净切换
→ 波形完整，LED 平滑过渡，没有任何抖动
```

## 2 自制延时函数

![1775921415751](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775921415751.png)

**代码：**

```c
#include "stm32f10x.h"
volatile uint32_t currentTick=0;//用来记录当前时间 单位ms 
/*
@简介：延迟一段时间
@参数：ms:要延迟的时间（单位ms）
*/
void APP_Delay(uint32_t ms);
void APP_TIM3_TimeBaseInit(void);
void APP_OnBoardLED_Init(void);

int main(void)
{
	
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
	APP_TIM3_TimeBaseInit();
	APP_OnBoardLED_Init();
		
	while(1)
	{
		GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);
		APP_Delay(500);
		GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_RESET);
		APP_Delay(500);
		
	}
}
void APP_Delay(uint32_t ms)
{
	uint32_t expireTime=currentTick+ms;//延迟结束时间
	while(currentTick<expireTime);//等待延时结束
}
void APP_TIM3_TimeBaseInit(void)//给硬件下任务
{
	//#1 开启定时器3的时钟
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3,ENABLE);
	
	//#2 配置时基单元的参数
	TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStruct;
	
	TIM_TimeBaseInitStruct.TIM_Prescaler=71;
	TIM_TimeBaseInitStruct.TIM_Period=999;
	TIM_TimeBaseInitStruct.TIM_CounterMode=TIM_CounterMode_Up;
	TIM_TimeBaseInitStruct.TIM_RepetitionCounter=0;
	TIM_TimeBaseInit(TIM3,&TIM_TimeBaseInitStruct);
	
	//#3 闭合时基单元的开关
	TIM_Cmd(TIM3,ENABLE);
	
	//#4 使能Update中断
	TIM_ITConfig(TIM3,TIM_IT_Update,ENABLE);//输完了发出信号
	
	//#5 配置NVIC模块
	NVIC_InitTypeDef NVIC_InitStruct;
	NVIC_InitStruct.NVIC_IRQChannel=TIM3_IRQn;
	NVIC_InitStruct.NVIC_IRQChannelSubPriority=0;
	NVIC_InitStruct.NVIC_IRQChannelCmd=ENABLE;
	NVIC_Init(&NVIC_InitStruct);//信号发来了进入中断
	
}
void TIM3_IRQHandler(void)//TIM3的中断
{
	if(TIM_GetFlagStatus(TIM3,TIM_FLAG_Update==SET))//查标志位
	{
		TIM_ClearFlag(TIM3,TIM_FLAG_Update);//清除标志位
		currentTick++;
	}
}	

void APP_OnBoardLED_Init(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC,ENABLE);
	GPIO_InitTypeDef GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_13;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_Out_OD;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_2MHz;
	GPIO_Init(GPIOC,&GPIO_InitStruct);
	
	GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);
	
	
}
```

## 3 输出比较

**输出比较（Output Compare，简称 OC）**是微控制器（如 STM32）定时器中最基础、但也最强大的硬件功能之一。

如果用一句话来概括：**输出比较就是让定时器在内部默默数数，当数到你设定的某个特定值时，直接由硬件自动改变外部引脚的电平状态。**

**PWM 模式（脉宽调制）**

- **现象**：引脚电平在一个周期内，根据 `CNT` 与 `CCR` 的大小关系（大于或小于），分别输出高电平和低电平。
- **应用场景**：这是输出比较最著名的应用。通过动态改变 CCR 的值，就能改变占空比（高电平持续的时间比例），从而实现电机平滑调速、LED 呼吸灯渐变调光、加热器功率控制等。

![1776068454591](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776068454591.png)

![1776068519484](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776068519484.png)

**工作模式：**

![6092e64581a431385b460cefa925eb0b](D:\WeChat data\xwechat_files\wxid_8xtqolj6oi9a22_5f37\temp\RWTemp\2026-04\9e20f478899dc29eb19741386f9343c8\6092e64581a431385b460cefa925eb0b.jpg)

![0bf4bd970219bd10f9e3027f58b932e7](D:\WeChat data\xwechat_files\wxid_8xtqolj6oi9a22_5f37\temp\RWTemp\2026-04\9e20f478899dc29eb19741386f9343c8\0bf4bd970219bd10f9e3027f58b932e7.jpg)

**互补输出：**

![1776069010034](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776069010034.png)



**互补输出是为了安全、高效地驱动“半桥（Half-Bridge）”或“全桥（H-Bridge）”功率电路，防止电源和地之间发生毁灭性的短路（炸管）。**

### 1. 硬件背景：半桥电路（Half-Bridge）

在电机驱动电路中，最基本的控制单元是“半桥”。它由上下两个大功率开关管（通常是 MOSFET 或 IGBT）串联组成：

- **上管（High-Side Switch）**：连接正电源（VCC）。导通时，将电机接到电源。
- **下管（Low-Side Switch）**：连接地（GND）。导通时，将电机接到地。

为了让电机正反转，或者输出交流电，我们必须交替地打开和关闭上管和下管。

- 上管开，下管关 $\rightarrow$ 输出高电平。
- 上管关，下管开 $\rightarrow$ 输出低电平。

这就要求给上管和下管的控制信号**在逻辑上必须完全相反**。这正是**互补输出（CHx 和 CHxN）**存在的首要原因：单片机硬件自动生成两路电平相反的 PWM 信号，无需软件去手动取反计算。

**极性选择：**

![1776069122350](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776069122350.png)

## 4 呼吸灯实验

```c
#include "stm32f10x.h"
#include "delay.h"
#include<math.h>
void APP_PWM_Init(void);

int main(void)
{
	while(1)
	{
		float t=GetTick()*1.0e-3f;//获取当前时间。单位s
		float duty=0.5*(sin(2*3.14*t)+1);//获取当前占空比%
		uint16_t ccr1=duty*1000;//获取CCR1的值
		
		TIM_SetCompare1(TIM1,ccr1);
	}
}
void APP_PWM_Init(void)
{
	//#1.初始化IO引脚PA8 PB13
		RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
		GPIO_InitTypeDef GPiO_InitStruct;
		GPiO_InitStruct.GPIO_Pin=GPIO_Pin_8;
		GPiO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
		GPiO_InitStruct.GPIO_Speed=GPIO_Speed_2MHz;
		GPIO_Init(GPIOA,&GPiO_InitStruct);
		
		RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
		GPiO_InitStruct.GPIO_Pin=GPIO_Pin_13;
		GPiO_InitStruct.GPIO_Mode=GPIO_Mode_AF_PP;
		GPiO_InitStruct.GPIO_Speed=GPIO_Speed_2MHz;
		GPIO_Init(GPIOB,&GPiO_InitStruct);
		
		//#2.配置时基单元
		
		//2.1开启TIM1的时钟
			RCC_APB2PeriphClockCmd(RCC_APB2Periph_TIM1,ENABLE);
		//2.2配置时基单元参数
		TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStruct;
		TIM_TimeBaseInitStruct.TIM_CounterMode=TIM_CounterMode_Up;
		TIM_TimeBaseInitStruct.TIM_Period=999;
		TIM_TimeBaseInitStruct.TIM_Prescaler=71;
		TIM_TimeBaseInitStruct.TIM_RepetitionCounter=0;
		
		TIM_TimeBaseInit(TIM1,&TIM_TimeBaseInitStruct);
		
		
		//2.3 开启ARR的预加载
		TIM_ARRPreloadConfig(TIM1,ENABLE);
		
		//2.4 闭合时基单元的开关
		
		TIM_Cmd(TIM1,ENABLE);
		
		//#3初始化输出比较
    //3.1初始化输出比较通道1的参数
        TIM_OCInitTypeDef TIM_OCInitStruce;
		TIM_OCInitStruce.TIM_OCMode=TIM_OCMode_PWM1;
		TIM_OCInitStruce.TIM_OCNPolarity=TIM_OCNPolarity_High;
		TIM_OCInitStruce.TIM_OCPolarity=TIM_OCPolarity_High;
		TIM_OCInitStruce.TIM_OutputNState=TIM_OutputNState_Enable;
		TIM_OCInitStruce.TIM_OutputState=TIM_OutputState_Enable;
		TIM_OCInitStruce.TIM_Pulse=0;
		
		TIM_OC1Init(TIM1,&TIM_OCInitStruce);
		
		//3.2 闭合MOE总开关
		TIM_CtrlPWMOutputs(TIM1,ENABLE);
		
		//RCC预加载
		TIM_CCPreloadControl(TIM1,ENABLE);
}

```

![1776079988007](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776079988007.png)

## 5 输入捕获

输入捕获是定时器的一个功能模块，核心作用是：**当引脚上发生电平跳变时，自动把当前定时器的计数值"拍下来"存起来**，从而精确测量时间信息。

![1776083231217](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776083231217.png)

![baf14d09d41b3f3cef44215eeb0f0914](D:\WeChat data\xwechat_files\wxid_8xtqolj6oi9a22_5f37\temp\RWTemp\2026-04\9e20f478899dc29eb19741386f9343c8\baf14d09d41b3f3cef44215eeb0f0914.jpg)

![1776083284084](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776083284084.png)

输入滤波：为输入信号做一个过滤

边沿检测：检测上升沿还是下降沿

信号选择：直接从这个通道出还是间接从另一个通道出

分频器：选择几个信号采集（比如/2，来2个高电平才开始采集）

## 6 超声波测距实验

![1776132443759](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776132443759.png)

![1776132461395](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776132461395.png)

```c
#include "stm32f10x.h"
#include "delay.h"
#include "usart.h"

/* 函数声明 */
void App_USART1_Init(void);
void APP_HCSR04_Init(void);

/**
 * @brief  主函数
 * @note   使用TIM1的输入捕获(PWM输入模式)读取HC-SR04超声波模块的回响信号，计算距离并通过串口打印
 */
int main(void)
{
	/* 系统外设初始化 */
	App_USART1_Init(); // 初始化USART1，用于调试信息输出
	APP_HCSR04_Init(); // 初始化HC-SR04相关的GPIO和定时器TIM1，配置PA8为捕获，PA0为触发输出

	while(1)
	{
		//#1 复位定时器计数器，准备开始新一轮的测距
		TIM_SetCounter(TIM1, 0);
		
		//#2 清除捕获/比较1和2的标志位，防止上一次的历史结果干扰本次判断
		TIM_ClearFlag(TIM1, TIM_FLAG_CC1);
		TIM_ClearFlag(TIM1, TIM_FLAG_CC2);
		
		//#3 开启定时器，开始计时
		TIM_Cmd(TIM1, ENABLE);
		
		//#4 向HC-SR04的TRIG引脚（PA0）发送至少10us的高电平脉冲，触发测距
		GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_SET);
		// 【注意补充】：这里原代码只有注释，你需要调用一个真实的微秒级延时函数，比如 Delay_us(15);
		// Delay_us(15); 
		GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_RESET);
		
		//#5 等待硬件捕获完成 (Echo引脚的脉冲信号)
		// 【工程建议】：实际量产项目中，切忌死等。建议加上超时机制 (如 timeout变量递减)，防止探头损坏时系统死机
		// 5.1 等待上升沿捕获（标记高电平起始时间）
		while(TIM_GetFlagStatus(TIM1, TIM_FLAG_CC1) == RESET);
		// 5.2 等待下降沿捕获（标记高电平结束时间）
		while(TIM_GetFlagStatus(TIM1, TIM_FLAG_CC2) == RESET);
		
		//#6 测量结束，关闭定时器
		TIM_Cmd(TIM1, DISABLE);
		
		//#7 读取捕获寄存器的值
		uint16_t ccr1 = TIM_GetCapture1(TIM1); // 获取上升沿发生时的计数值 (起始时间)
		// 【Bug修复】：原代码这里写成了 GetCapture1，应改为 GetCapture2 获取下降沿计数值 (结束时间)
		uint16_t ccr2 = TIM_GetCapture2(TIM1); 
		
		//#8 计算实际距离
		// 原理：定时器预分频配置为71，即72MHz/72=1MHz，所以每1个计数单位代表 1us。
		// 时间 t = (ccr2 - ccr1) us = (ccr2 - ccr1) * 10^-6 s
		// 距离 = (时间 * 声速 340m/s) / 2(声波往返)
		float distance = (ccr2 - ccr1) * 1.0e-6f * 340.0f / 2.0f; 
		
		//#9 串口输出距离数据 (单位: 米)
		// 【Bug修复】：原代码的 "%0.4" 漏掉了浮点型标识符 'f'，修正为 "%0.4f"
		My_USART_Printf(USART1, "distance=%0.4f m\r\n", distance);
		
		//#10 延时控制测量频率
		// HC-SR04的官方手册建议测量周期要大于60ms，防止发射信号对回响信号产生干扰
		Delay(100); 
	}
}

/**
 * @brief  USART1 初始化函数
 * @note   配置PA9(TX)和PA10(RX)，波特率115200
 */
void App_USART1_Init(void)
{
	//#1 初始化IO引脚
	// 开启GPIOA时钟
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	GPIO_InitTypeDef GPIO_InitStruct;
	
	// PA9 配置为复用推挽输出 (TX)
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_9;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Speed = GPIO_Speed_10MHz;
	GPIO_Init(GPIOA, &GPIO_InitStruct);
	
	// PA10 配置为上拉输入 (RX)
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_10;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPU;
	GPIO_InitStruct.GPIO_Speed = GPIO_Speed_10MHz;
	GPIO_Init(GPIOA, &GPIO_InitStruct);
	
	//#2 初始化USART1
	// 2.1 开启USART1时钟 (USART1挂载在APB2总线上)
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);
	
	// 2.2 初始化USART1的参数
	USART_InitTypeDef USART_InitStruct;
	USART_InitStruct.USART_BaudRate = 115200;                                   // 波特率115200
	USART_InitStruct.USART_HardwareFlowControl = USART_HardwareFlowControl_None; // 无硬件流控
	USART_InitStruct.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;                // 支持发送和接收
	USART_InitStruct.USART_Parity = USART_Parity_No;                            // 无校验位
	USART_InitStruct.USART_StopBits = USART_StopBits_1;                         // 1个停止位
	USART_InitStruct.USART_WordLength = USART_WordLength_8b;                    // 8位数据位
	
	USART_Init(USART1, &USART_InitStruct);
	// 【注意】：此处通常还需要添加 USART_Cmd(USART1, ENABLE); 来使能串口，请确认 My_USART_Printf 内部是否已处理
}

/**
 * @brief  HC-SR04及定时器初始化函数
 * @note   PA0: Trig触发引脚 (普通推挽输出)
 * PA8: Echo回响接收引脚 (使用TIM1的CH1和CH2配置为PWM输入模式进行高电平脉宽测量)
 */
void APP_HCSR04_Init(void)
{
	//#1 初始化时基单元
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_TIM1, ENABLE); // 开启TIM1时钟 (72MHz)
	
	TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStruct;
	TIM_TimeBaseInitStruct.TIM_CounterMode = TIM_CounterMode_Up; // 向上计数模式
	TIM_TimeBaseInitStruct.TIM_Period = 65535;                   // 自动重装载值(ARR)，16位定时器最大为0xFFFF
	TIM_TimeBaseInitStruct.TIM_Prescaler = 71;                   // 预分频器(PSC): 72MHz/(71+1) = 1MHz -> 1个Tick对应1us
	TIM_TimeBaseInitStruct.TIM_RepetitionCounter = 0;            // 重复计数器 (高级定时器特有，设为0即可)
	
	TIM_TimeBaseInit(TIM1, &TIM_TimeBaseInitStruct);
	
	//#2 初始化输入捕获 (这里使用了经典的PWM输入模式思路)
	//#2.1 初始化IO引脚
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	GPIO_InitTypeDef GPIO_InitStruct;
	
	// PA8 配置为下拉输入 (默认处于低电平，等待Echo的高电平信号)
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_8;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPD;
	GPIO_Init(GPIOA, &GPIO_InitStruct);
	
	//#2.2 初始化输入捕获的通道1 (测量高电平的起始点)
	TIM_ICInitTypeDef TIM_ICInitStruct;
	TIM_ICInitStruct.TIM_Channel = TIM_Channel_1;                // 选择通道1
	TIM_ICInitStruct.TIM_ICFilter = 0;                           // 滤波器配置为0，不滤波
	TIM_ICInitStruct.TIM_ICPolarity = TIM_ICPolarity_Rising;     // 捕获极性：上升沿捕获
	TIM_ICInitStruct.TIM_ICPrescaler = TIM_ICPSC_DIV1;           // 每次有效边沿都执行捕获
	TIM_ICInitStruct.TIM_ICSelection = TIM_ICSelection_DirectTI; // 直接映射：TI1映射到IC1
	
	TIM_ICInit(TIM1, &TIM_ICInitStruct);
	
	//#2.3 初始化输入捕获的通道2 (测量高电平的结束点)
	TIM_ICInitStruct.TIM_Channel = TIM_Channel_2;                // 选择通道2
	TIM_ICInitStruct.TIM_ICFilter = 0;                           // 不滤波
	TIM_ICInitStruct.TIM_ICPolarity = TIM_ICPolarity_Falling;    // 捕获极性：下降沿捕获
	TIM_ICInitStruct.TIM_ICPrescaler = TIM_ICPSC_DIV1;           // 每次有效边沿都执行捕获
	TIM_ICInitStruct.TIM_ICSelection = TIM_ICSelection_IndirectTI; // 交叉映射：将TI1(即PA8)交叉映射到IC2
	
	TIM_ICInit(TIM1, &TIM_ICInitStruct);
	
	//#3 初始化Trig引脚 (PA0)
	//#3.1 初始化IO引脚 (时钟上面已经开过，此处重复调用无影响)
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	
	// PA0 配置为推挽输出
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_0;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP; 
	GPIO_InitStruct.GPIO_Speed = GPIO_Speed_2MHz; // 频率不需要太高，2MHz足以驱动TRIG
	GPIO_Init(GPIOA, &GPIO_InitStruct);
}

```

## 7 从模式控制器

###  主模式 (Master Mode) —— “发令员”

当一个定时器被配置为“主模式”时，它就变成了发令员。 发令员手里有一把发令枪，这把枪的枪口在芯片里叫做 **TRGO (Trigger Output，触发输出)**。

**发令员什么时候开枪（发出触发信号）？** 这由你来设定。你可以设定为：

1. **“使能”时开枪：** 只要你用代码启动了这个定时器，它就立刻打一枪（发一个脉冲）。
2. **“更新/溢出”时开枪：** 定时器每次数到最大值，重新回到 0 的时候，打一枪。

*一句话总结：主模式定时器的核心任务，就是向外广播自己的状态变化。*

 ### 从模式 (Slave Mode) —— “听话的运动员”

当一个定时器被配置为“从模式”时，它就变成了等待指令的运动员。 它必须竖起耳朵听枪声，这只耳朵在芯片里叫做 **TRGI (Trigger Input，触发输入)**。通过内部连线，这只耳朵刚好贴在了主定时器的枪口 (TRGO) 上。

**运动员听到枪声后干什么？** 这也是由你设定的（这就是所谓的“从模式选择”）：

1. **触发模式 (Trigger Mode)：** 听到枪声，立刻开始往前跑（定时器立刻启动计数）。
2. **复位模式 (Reset Mode)：** 听到枪声，不管跑到哪了，立刻退回起点（定时器计数清零）。
3. **门控模式 (Gated Mode)：** 枪声响着（高电平）就一直跑，枪声停了就停下。

*一句话总结：从模式定时器的核心任务，是不受 CPU 代码控制，纯靠硬件听从别人的信号自动执行动作。*

### 定时器同时启停的例子

![1776148013650](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776148013650.png)

### 定时器级联的例子

![1776148030503](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776148030503.png)

## 8 PWM参数测量原理

![1776150274511](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776150274511.png)

![1776150291886](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776150291886.png)

![1776150355759](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776150355759.png)

## 9 PWM参数测量实验

这个程序在自己板子上上演了一出**“自导自演”**的好戏：它用一个定时器（TIM3）发出特定频率和占空比的 PWM 波，然后用另一个定时器（TIM1）作为“示波器”，把这个波形精准地测量出来，最后通过串口报告给你。

把这个小口误修正后，你的整段描述就**堪称完美**了！为你再详细打个对勾，确认你的逻辑：

1. ✅ **信号发出：** **TIM3** 产生的 PWM 方波信号，通过定时器输出引脚 **PA6** 发送出去。
2. ✅ **物理接收：** 信号顺着外部的杜邦线，跑到了 **TIM1** 的输入引脚 **PA8** 上。
3. ✅ **清零触发：** PA8 的信号进入芯片后成为 `TI1` 信号。它被配置为 `TI1FP1` 并选作触发输入源（`TRGI`）。因为你配置了复位从模式（SlaveMode_Reset），所以只要 `TI1FP1` 出现**上升沿**，硬件就会立刻把 `CNT` 寄存器**清零**重新计时。
4. ✅ **脉宽捕获：** 信号在检测到**下降沿**时，通道 2 会自动把当前 `CNT` 的值“拍”下来，存入 **CCR2**。这就是高电平持续的时间。
5. ✅ **周期捕获：** 当**再次遇到上升沿**时，通道 1 会在 `CNT` 被清零的前一极其微小的瞬间，把满载的 `CNT` 值存入 **CCR1**。这就是整个 PWM 波的周期时间。

![1776159210308](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776159210308.png)



![1776159230534](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776159230534.png)

![1776159255383](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776159255383.png)

```c
/**
  ******************************************************************************
  * @file    main.c
  * @brief   STM32 定时器 PWM 输入捕获综合实验
  * @note    硬件连接要求：用杜邦线将 PA6（PWM输出）与 PA8（PWM输入）短接。
  * 运行原理：TIM3 在 PA6 产生 1kHz 的测试方波，TIM1 在 PA8 接收并硬件自动
  * 测量周期和占空比，最后通过 USART1 打印到电脑串口助手。
  ******************************************************************************
  */

#include "stm32f10x.h"
#include "usart.h"

void App_USART1_Init(void);
void App_TIM3_Init(void);
void App_TIM1_Init(void);

int main(void)
{
	// 1. 系统各模块初始化
	App_USART1_Init(); // 初始化串口1，用于打印数据
	App_TIM3_Init();   // 初始化定时器3，用于产生PWM测试波形
	App_TIM1_Init();   // 初始化定时器1，用于测量PWM波形
	
	// 2. 设置 TIM3 通道1的比较值 (CCR1)
	// TIM3 的 ARR 被配置为 999，计满是 1000 个数。
	// 这里设 CCR1=200，则高电平占 200，占空比为 200/1000 = 20%
	TIM_SetCompare1(TIM3, 200); 
	
	while(1)
	{
		// 3.1 清除 TIM1 的触发标志位，准备迎接下一次完整测量
		TIM_ClearFlag(TIM1, TIM_FLAG_Trigger);
		
		// 3.2 阻塞等待 Trigger 标志位置 1
		// 硬件复位从模式下，一旦 PA8 再次出现上升沿，说明测完了一个完整周期，硬件会自动将此标志位置1
		while(TIM_GetFlagStatus(TIM1, TIM_FLAG_Trigger) == RESET);
		
		// 3.3 读取硬件自动捕获到的数据
		// CCR1 抓取的是整个周期的计数值 (由通道1上升沿触发)
		uint16_t ccr1 = TIM_GetCapture1(TIM1); 
		// CCR2 抓取的是高电平的计数值 (由通道2下降沿触发)
		uint16_t ccr2 = TIM_GetCapture2(TIM1); 
		
		// 3.4 数据换算 (注意：TIM1的主频被配置为 1MHz，即每个计数代表 1us)
		// 周期 = 计数值 * 1us。这里转为秒(1e-6)再转为毫秒(1e3)，其实就是直接除以1000
		float period = ccr1 * 1.0e-6f * 1.0e3f; 
		// 占空比 = 高电平时间 / 周期时间 * 100%
		float duty = ((float)ccr2) / ccr1 * 100.0f; 
		
		// 3.5 串口打印结果 (注：输出实际的 '%' 字符必须用 '%%' 转义)
		My_USART_Printf(USART1, "周期=%.3fms,占空比=%.2f%%\r\n", period, duty);
	}
}

/**
  * @brief  初始化 USART1 (用于串口调试助手打印)
  * @note   TX引脚: PA9
  */
void App_USART1_Init(void)
{
	// 1. 使能 GPIOA 和 USART1 的硬件时钟
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);
	
	// 2. 配置 PA9 为复用推挽输出模式 (因为TX端由USART外设控制输出高低电平)
	GPIO_InitTypeDef GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_AF_PP; 
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_9;
	GPIO_InitStruct.GPIO_Speed = GPIO_Speed_10MHz;
	GPIO_Init(GPIOA, &GPIO_InitStruct);
	
	// 3. 配置 USART1 通信参数
	USART_InitTypeDef USART_InitStruct;
	USART_InitStruct.USART_BaudRate = 115200;                    // 波特率 115200
	USART_InitStruct.USART_HardwareFlowControl = USART_HardwareFlowControl_None; // 无硬件流控
	USART_InitStruct.USART_Mode = USART_Mode_Tx;                 // 本实验仅需发送模式
	USART_InitStruct.USART_Parity = USART_Parity_No;             // 无校验位
	USART_InitStruct.USART_StopBits = USART_StopBits_1;          // 1个停止位
	USART_InitStruct.USART_WordLength = USART_WordLength_8b;     // 8位数据位
	USART_Init(USART1, &USART_InitStruct);
	
	// 4. 闭合总开关，使能串口
	USART_Cmd(USART1, ENABLE);
}

/**
  * @brief  初始化 TIM3 为信号发生器 (输出比较 PWM 模式)
  * @note   PWM信号输出引脚: PA6 (TIM3_CH1)
  */
void App_TIM3_Init(void)
{
	// 1. 使能 TIM3 和 GPIOA 时钟
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3, ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	
	// 2. 配置时基单元 (决定 PWM 的频率)
	// 72MHz / (71+1) = 1MHz 计数频率 (1us计一次数)
	// (999+1) * 1us = 1000us = 1ms 周期 (即1kHz频率)
	TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStruct;
	TIM_TimeBaseInitStruct.TIM_CounterMode = TIM_CounterMode_Up; // 向上计数模式
	TIM_TimeBaseInitStruct.TIM_Period = 999;                     // 自动重装载值 ARR
	TIM_TimeBaseInitStruct.TIM_Prescaler = 71;                   // 预分频器 PSC
	TIM_TimeBaseInitStruct.TIM_RepetitionCounter = 0;            // 高级定时器才用的重复计数器，这里给0
	TIM_TimeBaseInit(TIM3, &TIM_TimeBaseInitStruct);
	
	// 开启 ARR 的预加载功能（缓冲机制，保证波形输出平滑）
	TIM_ARRPreloadConfig(TIM3, ENABLE);
	
	// 3. 配置 PA6 引脚为复用推挽输出 (让TIM3硬件接管引脚控制权)
	GPIO_InitTypeDef GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_6;
	GPIO_InitStruct.GPIO_Speed = GPIO_Speed_2MHz;
	GPIO_Init(GPIOA, &GPIO_InitStruct);
	
	// 4. 配置 TIM3_CH1 的输出比较参数 (决定 PWM 的模式)
	TIM_OCInitTypeDef TIM_OCInitStruce;
	TIM_OCInitStruce.TIM_OCMode = TIM_OCMode_PWM1;           // PWM1模式：CNT < CCR 时输出有效电平
	TIM_OCInitStruce.TIM_OCPolarity = TIM_OCPolarity_High;   // 有效电平定义为高电平
	TIM_OCInitStruce.TIM_OutputState = TIM_OutputState_Enable; // 使能输出
	TIM_OCInitStruce.TIM_Pulse = 0;                          // 初始 CCR 比较值先设为0
	TIM_OC1Init(TIM3, &TIM_OCInitStruce);
	
	// 使能高级/通用定时器独有的 PWM 输出总控制（MOE，主输出使能）
	TIM_CtrlPWMOutputs(TIM3, ENABLE);
	// 开启 CCR 的预加载功能（在更新事件到来时才生效新CCR值，防止占空比突变）
	TIM_CCPreloadControl(TIM3, ENABLE);
	
	// 5. 启动 TIM3
	TIM_Cmd(TIM3, ENABLE);
}

/**
  * @brief  初始化 TIM1 为信号测量仪 (PWM 输入捕获从模式)
  * @note   PWM信号输入引脚: PA8 (TIM1_CH1)
  */
void App_TIM1_Init()
{
	// 1. 使能 TIM1 和 GPIOA 时钟
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_TIM1, ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	
	// 2. 配置时基单元 (决定测量的精度和最大量程)
	// 72MHz / (71+1) = 1MHz 计数频率 (意味着我们测量的精度是 1us)
	// Period 设为最大值 65535，意味着最多能测 65.5ms 的长周期而不溢出
	TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStruct;
	TIM_TimeBaseInitStruct.TIM_CounterMode = TIM_CounterMode_Up;
	TIM_TimeBaseInitStruct.TIM_Period = 65535;
	TIM_TimeBaseInitStruct.TIM_Prescaler = 71;
	TIM_TimeBaseInitStruct.TIM_RepetitionCounter = 0;
	TIM_TimeBaseInit(TIM1, &TIM_TimeBaseInitStruct);
	
	TIM_ARRPreloadConfig(TIM1, ENABLE);
	
	// 3. 配置 PA8 为下拉输入 (等待外部信号输入)
	GPIO_InitTypeDef GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPD;
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_8;
	GPIO_Init(GPIOA, &GPIO_InitStruct);
	
	// 4. 配置输入捕获链路 (PWM模式核心配置)
	TIM_ICInitTypeDef TIM_ICInitStruce;
	
	// 4.1 配置通道1：直连模式测周期
	TIM_ICInitStruce.TIM_Channel = TIM_Channel_1;
	TIM_ICInitStruce.TIM_ICFilter = 0;                     // 不使用硬件滤波
	TIM_ICInitStruce.TIM_ICPolarity = TIM_ICPolarity_Rising; // 盯着【上升沿】抓取 CNT
	TIM_ICInitStruce.TIM_ICPrescaler = TIM_ICPSC_DIV1;       // 不分频，每次跳变都抓
	TIM_ICInitStruce.TIM_ICSelection = TIM_ICSelection_DirectTI; // 直连(PA8信号给到TI1)
	TIM_ICInit(TIM1, &TIM_ICInitStruce);
	
	// 4.2 配置通道2：交叉模式测高电平脉宽
	TIM_ICInitStruce.TIM_Channel = TIM_Channel_2;
	TIM_ICInitStruce.TIM_ICFilter = 0;
	TIM_ICInitStruce.TIM_ICPolarity = TIM_ICPolarity_Falling; // 盯着【下降沿】抓取 CNT
	TIM_ICInitStruce.TIM_ICPrescaler = TIM_ICPSC_DIV1;
	TIM_ICInitStruce.TIM_ICSelection = TIM_ICSelection_IndirectTI; // 交叉(依然抓取PA8引脚/TI1的信号)
	TIM_ICInit(TIM1, &TIM_ICInitStruce);
	
	// 5. 配置触发与从模式 (实现硬件自动清零)
	// 告诉定时器1，用经过滤波和极性选择后的 TI1 信号 (即PA8的上升沿) 作为触发源
	TIM_SelectInputTrigger(TIM1, TIM_TS_TI1FP1); 
	// 配置从模式为“复位模式”：每次触发源出现有效信号，硬件自动将 CNT 寄存器清零
	TIM_SelectSlaveMode(TIM1, TIM_SlaveMode_Reset);
	
	// 6. 启动 TIM1
	TIM_Cmd(TIM1, ENABLE);
}

```

您目前写的这段代码中，**TRGO（触发输出，Trigger Output）是完全没有用到的，处于“闲置”状态。**

您能敏锐地联想到 `TRGO`，说明您在看参考手册或者底层框图时看得非常细致！为了不让您混淆，我帮您把 `TRGI` 和 `TRGO` 在 STM32 里的分工彻底掰扯清楚：

1. 您代码里真正用的是 `TRGI`（触发输入）

在 `App_TIM1_Init()` 的最后，您写了这样一行代码：

```
TIM_SelectInputTrigger(TIM1, TIM_TS_TI1FP1);
```

这里配置的是 **TRGI（Trigger Input，触发输入）**。 它的作用是给 TIM1 指定一个**内部的“接收器”**。您通过这行代码告诉 TIM1：“请把你内部的 TRGI 连接到 TI1FP1 信号上，一旦收到上升沿，你就复位。”

2. 那 `TRGO`（触发输出）到底是个啥？

如果说 `TRGI` 是定时器用来听别人指挥的“耳朵”，那么 **TRGO（Trigger Output）就是定时器用来指挥别人的“大喇叭”**。

当一个定时器发生了某些事件（比如复位、使能、或者计满溢出发生更新 Update），它可以把这个事件转化为一个硬件脉冲信号，通过内部的一根线——也就是 `TRGO`，发送给芯片**内部**的其他外设。

**常见的 TRGO 应用场景：**

1. **触发 ADC 采样：** 比如你想让 ADC 严格每 1ms 采一次样。你就可以配置一个定时器每 1ms 产生一次 `TRGO` 信号，把这个信号在芯片内部直接连到 ADC 的触发源上。全程不需要进中断，极其高效。
2. **定时器内部级联（主从模式）：** 假设你想连一个极长周期的定时器，你可以让 TIM3 的 `TRGO` 在芯片内部直接连到 TIM1 的 `TRGI` 上。这样 TIM3 每次计满，TIM1 的计数器就加 1。**这种方式不需要在外面连杜邦线！**

## 10 题目：

定时器常用计数方式有哪几种

时器计数方式主要有以下三种：

### 1. 向上计数模式 (Up-counting) 📈

- **原理**：计数器从 `0` 开始，一步步往上数，一直数到一个我们设定的最大值（叫做 **ARR**，自动重装载值）。
- **动作**：当数到 ARR 时，计数器会瞬间清零回到 `0`，同时产生一个“溢出事件”（可以用来触发中断或唤醒 DMA），然后重新开始新一轮的往上数。
- **波形表现**：如果你把它每次数数的值画在图表上，会看到一个不断攀升然后瞬间跌落的**锯齿波**。

### 2. 向下计数模式 (Down-counting) 📉

- **原理**：和向上计数正好相反。计数器从设定的最大值（ARR）开始，一步步往下数，一直数到 `0`。
- **动作**：当数到 `0` 时，它会瞬间跳回到 ARR 的值，产生一个“向下溢出事件”，然后重新开始往下数。
- **波形表现**：画出来的图形是一个反向的锯齿波。

### 3. 中心对齐模式 (Center-aligned / 向上向下计数) ⛰️

- **原理**：这是前两者的结合。计数器先从 `0` 往上数到 ARR，然后再从 ARR 往下数回 `0`，如此反复。
- **动作**：通常在数到顶端和底端时，都可以分别产生事件。
- **波形表现**：它的波形是一个非常对称的**等腰三角形**。
- **应用场景**：这种模式在**电机控制 (FOC)** 中极其重要！因为中心对齐模式下生成的 PWM 波形是对称的，这能让电机运转得更平稳，产生的电磁噪声更小。



# 十 ADC

## 1、逐渐逼近型ADC

**ADC（Analog-to-Digital Converter）**，即**模拟/数字转换器**，是连接物理世界（模拟信号）与数字系统（如单片机、DSP等）的桥梁。

**模拟信号：**

存在于自然界当中，时间连续、幅度也连续

**数字信号：**

存在于计算机中，时间离散、幅度也离散

采样深度：用多少位二进制数表示转换结果

![1775478156574](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775478156574.png)

**逐渐逼近型ADC：**

![1775478567971](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775478567971.png)

类似于2分法

![1775478653338](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775478653338.png)

**电路图：**

![1775478750234](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775478750234.png)

**采样保持电路：**

在极短的时间内捕捉模拟信号的瞬时值（采样），并将其稳定保持一段时间（保持），直到 ADC 完成数字化转换。

**A. 采样阶段 (Sample/Track Mode)**

- **状态：** 模拟开关**闭合**。
- **过程：** 电路就像一个随动器，输入信号对保持电容（Cadc）进行快速充电。
- **目标：** 使得电容两端的电压 **V**c 尽可能快地追踪输入电压 **V**in。

**B. 保持阶段 (Hold Mode)**

- **状态：** 模拟开关**断开**。
- **过程：** 电容与输入信号断开。由于输出端连接的是高阻抗的运放，电容上的电荷几乎没有泄漏。
- **目标：** 输出一个稳定的电压值 **V**out，供后续的比较器（如 SAR ADC 中的比较器）进行量化，确保在转换周期内数值不发生跳变。

**例子：**

![1775479220989](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775479220989.png)

假如模拟信号量输入2.21V，此时开关闭合，电容充满电，开关断开，测得**C**ADC两端的电压为2.21V,假如采样深度是4位

b3进入比较，b3=1.76V<2.21V,此时b3=1;

b2+b3=2.64V>2.21V,b3=0;

b3+b1=2.2V<2.21V,b1=1;

b3+b1+b0=2.42V>2.22V,b0=0;

数字信号为：0101

## 2、ADC模块的结构框图

![1775479899073](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775479899073.png)

### **2.1 ADC的多路复用：**

![1775480081557](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775480081557.png)

### **2.2 常规序列：**

![1775480178515](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775480178515.png)

TIM3_TRGO每触发一次，上升沿，ADC按照常规序列执行一次，

**TIM3_TRGO**



![1775480530759](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775480530759.png)

![1775480389354](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775480389354.png)

黄色的为时间，

### 2.3 注入序列

![1775480751054](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775480751054.png)

### 2.4 例子

![1775480833243](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775480833243.png)



## 3、采样时间和转换时间

输入到ADC的频率不能超过14MHz

采样时间：开关闭合的时间长度

转换时间：对采样点进行转换所消耗的时间

### 3.1 ADC的时钟频率

![1775483770730](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775483770730.png)

### 3.2 转换时间的计算方式

![1775484324113](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775484324113.png)

### 3.3 采样时间和信号源内阻的关系

信号源内阻越大，电容充电越慢，所需的采样时间就越长。

### 3.4 信号源内阻的计算方法 

**方法一：开路电压与已知负载法（最常用、最基础）**

这是实际工程中最常用的测量方式，只需要一个万用表和一个已知阻值的精密电阻。

**操作步骤：**
1. **测开路电压 ($V_{oc}$)**：在信号源未接任何负载时，用高输入阻抗的万用表直接测量其输出电压。此时电流极小，可近似认为 $V_{oc} \approx V_s$。
2. **测负载电压 ($V_L$)**：将一个已知阻值的精密电阻（$R_L$）并联在信号源输出端作为负载，再次测量负载两端的电压 $V_L$。
3. **计算内阻 ($R_s$)**：
   根据串联分压原理：$V_L = V_{oc} \times \frac{R_L}{R_s + R_L}$
   推导出内阻公式：
   $$R_s = R_L \times \frac{V_{oc} - V_L}{V_L}$$

> **经验提示：** 选择的 $R_L$ 阻值应尽量与预估的 $R_s$ 在同一个数量级，这样测得的压降变化明显，计算精度更高。

**方法二：双负载法（精度更高）**

如果万用表本身的内阻不足够大，或者信号源本身无法真正“开路”（比如某些带有微弱漏电流的电路），开路电压法可能产生误差。此时可以使用双负载法。

**操作步骤：**
1. 接入已知负载 $R_1$，测得电压为 $V_1$。
2. 换成另一个已知负载 $R_2$，测得电压为 $V_2$。
3. **计算内阻 ($R_s$)**：
   根据戴维南等效方程：
   $$V_s = V_1 + I_1 \cdot R_s = V_1 + \frac{V_1}{R_1} \cdot R_s$$
   $$V_s = V_2 + I_2 \cdot R_s = V_2 + \frac{V_2}{R_2} \cdot R_s$$
   两式联立消去 $V_s$，解得：
   $$R_s = \frac{V_1 - V_2}{\frac{V_2}{R_2} - \frac{V_1}{R_1}}$$

### 3.5 采样时间的计算方法

 ![1775523395937](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775523395937.png)

## 4 常规单通道转换

###  4.1 初始化IO引脚

```c
//#1 初始化PA0引脚，模拟模式
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_InitTypeDef GPIO_InitStruct={0};
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_0;
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_AIN;
	GPIO_Init(GPIOA,&GPIO_InitStruct);
```

### 4.2 配置ADC模块的时钟

```c
RCC_ADCCLKConfig(RCC_PCLK2_Div6);//六分频
	
RCC_APB2PeriphClockCmd(RCC_APB2Periph_ADC1,ENABLE);
```

### 4.3 编程接口

![1775527480077](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775527480077.png)

![1775527596020](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775527596020.png)

![1775527736772](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775527736772.png)

### 4.3 初始化ADC的基本参数

```c
ADC_InitTypeDef ADC_InitStruct;
	
	ADC_InitStruct.ADC_ContinuousConvMode=DISABLE;//关闭连续模式
	ADC_InitStruct.ADC_DataAlign=ADC_DataAlign_Right;//右对齐
	ADC_InitStruct.ADC_ExternalTrigConv=ADC_ExternalTrigConv_None;//软件触发
	ADC_InitStruct.ADC_Mode=ADC_Mode_Independent;//独立模式
	ADC_InitStruct.ADC_NbrOfChannel=1;//常规序列1通道
	ADC_InitStruct.ADC_ScanConvMode=DISABLE;
	
	ADC_Init(ADC1,&ADC_InitStruct);
```

### 4.4 配置常规序列

```c
ADC_RegularChannelConfig(ADC1,ADC_Channel_0,1,ADC_SampleTime_13Cycles5);
	
	ADC_ExternalTrigConvCmd(ADC1,ENABLE);
```

### 4.5 闭合ADC总开关

```c
ADC_Cmd(ADC1,ENABLE);
```

### 4.6 启动并读取转换结果

**EOC (End of Conversion - 常规转换结束标志)：**

- 当“常规通道”里的某个引脚完成了一次采样和转换，硬件就会把数据扔进共享的 `ADC_DR` 寄存器，并举起 **EOC** 标志位。
- 这就好比普通门诊看完了一个病人，叫号屏幕闪烁一下（EOC置1），提示下一个。

**JEOC (Injected End of Conversion - 注入转换结束标志)：**

- “注入”（Inject）这个词的字面意思就是“插队”。如果 ADC 正在慢条斯理地转换“常规通道”，突然“注入通道”被触发了（比如定时器发出了一个紧急硬件信号）。
- ADC 会**立刻暂停**手头的常规转换，优先去采集注入通道。
- 等注入通道采集完，硬件会把数据放进专用的 `ADC_JDRx` 寄存器（注入通道自带 4 个独立单间，不用抢！），并举起 **JEOC** 标志位。
- 随后，ADC 再回头继续刚刚没做完的常规转换



![1775529256050](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1775529256050.png)

```c
//#1清除EOC标志位
		ADC_ClearFlag(ADC1,ADC_FLAG_EOC);
		//#2 通过软件启动的方式发送脉冲
		ADC_SoftwareStartConvCmd(ADC1,ENABLE);
		//#3 等待常规序列转换完成
		while(ADC_GetFlagStatus(ADC1,ADC_FLAG_EOC)==RESET);
		//#4 读取转换的结果
		uint16_t dr=ADC_GetConversionValue(ADC1);
		//#5 把结果转换为电压
		float voltage=dr*(3.3f/4095);
```

### 4.7 根据光照强弱决定是否点亮LED

```c
if(voltage>1.5)
		{
			GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_SET);
		}
		else
		{
			GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_RESET);
			
		}
```

## 5 定时器触发

#### 硬件连接清单

- **单片机**：STM32F103C8T6
- **光敏传感器**：模拟输出引脚 (AO) 连接到 STM32 的 **PA0**。
- **串口通信**：STM32 的 **PA9 (TX)** 连接到 USB转TTL 模块的 RX 端，用于连接电脑。

#### 2. 系统时序与逻辑

1. **TIM1定时器**：设置为 100Hz（每 10ms 溢出一次）。每次溢出产生一个**更新事件 (Update)**，并将其作为触发输出信号 (**TRGO**)。
2. **ADC1 模块**：平时处于休眠待命状态。一旦接收到 TIM1 的 TRGO 信号，立刻醒来对 PA0 的电压进行一次采样转换。转换完成后，触发 **EOC (转换结束) 中断**。
3. **CPU 与主循环**：CPU 在中断里只做最简单的搬运工作（读取数据、打个标志位），然后立刻退出中断。主循环 `while(1)` 发现标志位被置位，通过串口将数据发送给电脑上的“串口绘图器”。

![1776592617332](C:\Users\30493\AppData\Roaming\Typora\typora-user-images\1776592617332.png)

**什么是重定向呢？**

### 为什么一定要写一个 `fputc`？（重定向的魔法）

你在学C语言基础时一定用过 `printf("Hello World\n");`。在电脑上，这句话会直接打印在屏幕上。

但是，**单片机是没有屏幕的**。当单片机运行到 `printf` 时，它会很懵：“你要我把字符打印到哪里去？”

- **fputc 的真面目**：在C语言底层，`printf` 其实是一个“包工头”。它负责把你要打印的句子拆成一个个字母，然后交给底层一个叫 `fputc` 的小弟去发送。
- **偷梁换柱（重定向）**：为了让单片机能发数据给电脑，我们**自己重新写了一个 fputc 函数**。我们在这个函数里规定：“只要收到字母，就通过单片机的**串口（USART1）**用电线发出去！”
- **结果**：这样一来，我们每次调用 `printf`，底层其实都是在偷偷调用串口发送代码。这就叫**“重定向”**（改变数据的方向）。



## 6 扫描模式

### 实验名称：智能温室大棚环境监测系统

**硬件准备（以 STM32F103 为例）：**

- **PA0 引脚** 接 光敏电阻（测光照）
- **PA1 引脚** 接 热敏电阻（测环境温度）
- **PA2 引脚** 接 电位器（模拟土壤湿度，方便手动调节观察电压变化）
- **PB0 引脚** 接 实体按键（紧急按钮）

**我们要实现的三大核心任务：**

1. **日常巡视（规则组 + 连续扫描 + DMA）：** 让 PA0、PA1、PA2 组成规则组。开启连续扫描模式，并呼叫 DMA 这个“专属传菜员”，把这三个传感器的数据源源不断地自动搬运到内存中的一个数组 `Sensor_Data[3]` 里。CPU 完全不需要插手采样过程。
2. **紧急响应（注入组 + 外部触发）：** 将 STM32 内部自带的温度传感器（或者另一个关键引脚）配置为“注入组（VIP通道）”。平时它不工作，一旦按下 PB0 紧急按钮（外部中断 EXTI 触发），注入组立刻打断规则组，优先测量芯片核心温度，测完后再让规则组恢复工作。
3. **还原真相（分辨率与电压换算）：** 单片机的主程序（While循环）只需要悠闲地去读取 `Sensor_Data[2]`（电位器的原始数字量 0-4095），然后用公式把它换算成真实的 0-3.3V 电压值，通过串口打印到电脑屏幕上。

没问题！既然你希望有一套可以直接放入工程编译的完整代码，老师这就为你梳理整合，分为 `sensor.h`、`sensor.c` 和 `main.c` 三个标准文件。

同时，针对老师刚才提问的**数据微小跳动（噪声）问题**，在软件层面最经典、最高效的解决方法是**算术平均滤波法**。我会在主程序中加入一段“连续读取 10 次求平均”的逻辑，让你的最终输出数据非常稳定。

以下是完整的项目代码：

### 1. 头文件：`sensor.h`

用于声明全局变量和对外开放的接口函数，方便其他文件调用。

C



```c
#ifndef __SENSOR_H
#define __SENSOR_H

#include "stm32f10x.h"

// 暴露给主程序的全局变量
extern uint16_t Sensor_Data[3]; 
extern uint16_t Urgent_Core_Temp;

// 系统初始化接口
void Sensor_System_Init(void);

#endif
```

### 2. 驱动实现：`sensor.c`

包含所有的硬件底层配置（GPIO、DMA、ADC规则组与注入组、EXTI中断）。

C



```c
#include "sensor.h"

// ================= 全局变量定义 =================
uint16_t Sensor_Data[3]; 
uint16_t Urgent_Core_Temp; 

// ================= 1. GPIO 初始化 =================
static void Sensor_GPIO_Config(void)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA | RCC_APB2Periph_ADC1, ENABLE);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1 | GPIO_Pin_2;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN; // 模拟输入
    GPIO_Init(GPIOA, &GPIO_InitStructure);
}

// ================= 2. DMA 初始化 =================
static void Sensor_DMA_Config(void)
{
    DMA_InitTypeDef DMA_InitStructure;
    RCC_AHBPeriphClockCmd(RCC_AHBPeriph_DMA1, ENABLE);

    DMA_InitStructure.DMA_PeripheralBaseAddr = (uint32_t)&ADC1->DR;
    DMA_InitStructure.DMA_MemoryBaseAddr = (uint32_t)Sensor_Data;
    DMA_InitStructure.DMA_DIR = DMA_DIR_PeripheralSRC;
    DMA_InitStructure.DMA_BufferSize = 3;
    DMA_InitStructure.DMA_PeripheralInc = DMA_PeripheralInc_Disable;
    DMA_InitStructure.DMA_MemoryInc = DMA_MemoryInc_Enable;
    DMA_InitStructure.DMA_PeripheralDataSize = DMA_PeripheralDataSize_HalfWord;
    DMA_InitStructure.DMA_MemoryDataSize = DMA_MemoryDataSize_HalfWord;
    DMA_InitStructure.DMA_Mode = DMA_Mode_Circular; 
    DMA_InitStructure.DMA_Priority = DMA_Priority_High;
    DMA_InitStructure.DMA_M2M = DMA_M2M_Disable;
    DMA_Init(DMA1_Channel1, &DMA_InitStructure);

    DMA_Cmd(DMA1_Channel1, ENABLE);
}

// ================= 3. ADC 规则组 =================
static void Sensor_ADC_Config(void)
{
    ADC_InitTypeDef ADC_InitStructure;

    ADC_InitStructure.ADC_Mode = ADC_Mode_Independent;
    ADC_InitStructure.ADC_ScanConvMode = ENABLE;       
    ADC_InitStructure.ADC_ContinuousConvMode = ENABLE; 
    ADC_InitStructure.ADC_ExternalTrigConv = ADC_ExternalTrigConv_None; 
    ADC_InitStructure.ADC_DataAlign = ADC_DataAlign_Right;
    ADC_InitStructure.ADC_NbrOfChannel = 3;
    ADC_Init(ADC1, &ADC_InitStructure);

    ADC_RegularChannelConfig(ADC1, ADC_Channel_0, 1, ADC_SampleTime_55Cycles5);
    ADC_RegularChannelConfig(ADC1, ADC_Channel_1, 2, ADC_SampleTime_55Cycles5);
    ADC_RegularChannelConfig(ADC1, ADC_Channel_2, 3, ADC_SampleTime_55Cycles5);

    ADC_DMACmd(ADC1, ENABLE); 
}

// ================= 4. ADC 注入组 (紧急通道) =================
static void Sensor_Injected_Config(void)
{
    ADC_TempSensorVrefintCmd(ENABLE); 

    ADC_InjectedSequencerLengthConfig(ADC1, 1);
    ADC_InjectedChannelConfig(ADC1, ADC_Channel_16, 1, ADC_SampleTime_239Cycles5);
    ADC_ExternalTrigInjectedConvConfig(ADC1, ADC_ExternalTrigInjecConv_None); 
}

// ================= 5. EXTI 和 NVIC =================
static void Sensor_EXTI_NVIC_Config(void)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    EXTI_InitTypeDef EXTI_InitStructure;
    NVIC_InitTypeDef NVIC_InitStructure;

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB | RCC_APB2Periph_AFIO, ENABLE);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPD; // 下拉输入
    GPIO_Init(GPIOB, &GPIO_InitStructure);

    GPIO_EXTILineConfig(GPIO_PortSourceGPIOB, GPIO_PinSource0);

    EXTI_InitStructure.EXTI_Line = EXTI_Line0;
    EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt;
    EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Rising;
    EXTI_InitStructure.EXTI_LineCmd = ENABLE;
    EXTI_Init(&EXTI_InitStructure);

    NVIC_InitStructure.NVIC_IRQChannel = EXTI0_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0; // 最高优先级
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0;
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init(&NVIC_InitStructure);
}

// ================= 6. 统一初始化接口 =================
void Sensor_System_Init(void)
{
    Sensor_GPIO_Config();
    Sensor_DMA_Config();
    Sensor_ADC_Config();
    Sensor_Injected_Config();
    Sensor_EXTI_NVIC_Config();

    ADC_Cmd(ADC1, ENABLE); 

    ADC_ResetCalibration(ADC1);
    while(ADC_GetResetCalibrationStatus(ADC1));
    ADC_StartCalibration(ADC1);
    while(ADC_GetCalibrationStatus(ADC1));

    ADC_SoftwareStartConvCmd(ADC1, ENABLE); // 启动后台 DMA 采集
}

// ================= 7. 中断服务函数 =================
// 注意：如果你的工程里有 stm32f10x_it.c 文件，需确保里面没有同名的函数
void EXTI0_IRQHandler(void)
{
    if(EXTI_GetITStatus(EXTI_Line0) != RESET)
    {
        ADC_SoftwareStartInjectedConvCmd(ADC1, ENABLE);
        while(ADC_GetFlagStatus(ADC1, ADC_FLAG_JEOC) == RESET);
        Urgent_Core_Temp = ADC_GetInjectedConversionValue(ADC1, ADC_InjectedChannel_1);
        ADC_ClearFlag(ADC1, ADC_FLAG_JEOC);
        EXTI_ClearITPendingBit(EXTI_Line0);
    }
}
```

### 3. 核心业务：`main.c`

包含滑动平均滤波算法和数据的换算与打印。

```c
#include "stm32f10x.h"
#include "sensor.h"
#include <stdio.h>

// 假设延时和串口初始化已经在你的基础工程中写好
extern void Delay_ms(uint32_t ms); 
extern void USART1_Init(void);     

int main(void)
{
    float light_vol, env_temp_vol, humidity_vol;
    float core_temp_vol, core_temperature;
    
    uint32_t sum_light = 0, sum_env = 0, sum_hum = 0;
    uint8_t i;

    // USART1_Init(); // 串口初始化
    // Delay_Init();  // 延时初始化

    Sensor_System_Init(); // 启动我们编写的 ADC 监测系统

    while(1)
    {
        sum_light = 0;
        sum_env = 0;
        sum_hum = 0;

        // 【算术平均滤波】：连续采样 10 次累加
        for(i = 0; i < 10; i++)
        {
            sum_light += Sensor_Data[0];
            sum_env   += Sensor_Data[1];
            sum_hum   += Sensor_Data[2];
            Delay_ms(2); // 给 DMA 一点搬运新数据的时间
        }

        // 换算电压：(平均数字量 / 4095) * 3.3V
        light_vol    = ((float)sum_light / 10.0f) * 3.3f / 4095.0f;
        env_temp_vol = ((float)sum_env / 10.0f)   * 3.3f / 4095.0f;
        humidity_vol = ((float)sum_hum / 10.0f)   * 3.3f / 4095.0f;

        // 换算芯片内部核心温度 (假设按键按下后 Urgent_Core_Temp 有了数据)
        core_temp_vol = (float)Urgent_Core_Temp * 3.3f / 4095.0f;
        core_temperature = (1.43f - core_temp_vol) / 0.0043f + 25.0f;

        // 串口打印显示 (确保工程已重定向 printf 到串口)
        /*
        printf("光照: %.2f V | 环境: %.2f V | 湿度: %.2f V \r\n", light_vol, env_temp_vol, humidity_vol);
        printf("紧急核心温度: %.1f ℃ \r\n", core_temperature);
        printf("-------------------------------------------\r\n");
        */

        Delay_ms(500); 
    }
}
```

这套代码涵盖了我们这几天探讨的全部技术细节。你可以直接将它们加入到一个包含 STM32 标准外设库（Standard Peripheral Library）的基础工程中进行编译。

## 7 题目 

ADC精度是多少，采样率是多少

绝大多数主流单片机内置的 ADC 精度通常是 **12位 (12-bit)**。

- **含义**：它能把参考电压切分成 $2^{12} = 4096$ 个等级（数字量从 0 到 4095）。

- **实际效果**：假设你的系统参考电压（$V_{ref}$）是 3.3V，那么这颗 ADC 能分辨的最小电压变化量（LSB）大约是：

  $$3.3V \div 4096 \approx 0.0008V \text{ (即 0.8mV)}$$

- *注*：部分对模拟信号要求极高的工业或医疗级芯片会内置 16 位甚至 24 位的 ADC，此时精度会成倍提升。

### 2. 采样率 (Sampling Rate)

采样率是指 ADC 每秒钟能够完成多少次模拟电压的捕捉与转换，单位通常是 **SPS (Samples Per Second)** 或 **MSPS (Mega SPS，兆次每秒)**。

- **常见范围**：单片机内置 ADC 的最大理论采样率一般在 **1 MSPS 到 2.4 MSPS** 之间。基础型号（如百兆主频以下的 MCU）通常标称 1 MSPS；而更高阶的型号可以达到 2.4 MSPS、3.6 MSPS 甚至更高。
- **如何计算**：在代码配置中，实际采样率是由 **ADC 时钟频率 (ADC_CLK)** 和 **采样周期 (Sampling Cycles)** 共同决定的。转换一次通常需要经历 `采样时间 + 固定转换时间（例如 12.5 个时钟周期）`。

ADC触发方式有哪几种？请分别阐述？

| **触发方式**            | **核心机制**                                  | **采样时间精度**               | **CPU 参与度**                      | **典型应用场景**                                             |
| ----------------------- | --------------------------------------------- | ------------------------------ | ----------------------------------- | ------------------------------------------------------------ |
| **软件触发 (Software)** | 代码中直接调用启动函数 *(例如 HAL_ADC_Start)* | 较低（受代码执行和中断影响）   | 高（需要 CPU 主动执行指令）         | 偶尔测一次电池电量、读取按键电位器、低频单次采样。           |
| **定时器触发 (Timer)**  | 硬件定时器溢出或匹配时，自动产生触发脉冲      | **极高**（完全由硬件时钟控制） | **极低**（若配合 DMA 可实现零干预） | 音频采集、交流电分析、示波器、要求严格等间隔连续采样的系统。 |
| **外部引脚触发 (EXTI)** | 外部 GPIO 引脚电平发生跳变（上升沿/下降沿）   | 高（与外部物理事件严格同步）   | 低（由外部硬件信号驱动）            | 电机控制 (FOC) 中的精确同步采样、外部传感器数据就绪 (DRDY) 信号采集。 |

某个MCU，ADC参考电压为5V，精度为12bit,采集3V电压，请问采集到的寄存器的值是多少？

$$3 \times \left(\frac{4096}{5}\right) = 2457.6$$（转为二进制）



# 十一 modbus

Modbus 详细讲解

## 一、什么是 Modbus？

Modbus 是一种**串行通信协议**，由 Modicon 公司于 **1979 年**开发，最初用于可编程逻辑控制器（PLC）之间的通信。它是工业领域使用最广泛的通信协议之一，被称为工业通信的"通用语言"。

------

## 二、Modbus 的核心特点

- **开放标准**：免费、公开，任何人都可以使用
- **简单可靠**：协议结构简单，易于实现和调试
- **主从架构**：采用 Master/Slave（主从）通信模式
- **广泛支持**：几乎所有工业设备都支持 Modbus

------

## 三、通信模型：主从架构

```
主站 (Master)
    │
    ├──── 请求 ────▶  从站 1 (Slave, 地址 01)
    ├──── 请求 ────▶  从站 2 (Slave, 地址 02)
    └──── 请求 ────▶  从站 N (Slave, 地址 N)
```

| 角色            | 说明                                 |
| --------------- | ------------------------------------ |
| **主站 Master** | 主动发起请求，如 PC、SCADA 系统、PLC |
| **从站 Slave**  | 被动响应请求，如传感器、仪表、变频器 |

- 每个从站有唯一的**设备地址（1~247）**
- 同一时刻只有主站可以发起通信
- 从站只响应发给自己地址的请求

------

## 四、Modbus 的三种主要变体

### 1. Modbus RTU（最常用）

- 基于 **RS-232 / RS-485** 串行通信
- 数据以**二进制**格式传输，紧凑高效
- 使用 **CRC（循环冗余校验）** 校验数据完整性
- 适合工厂现场、距离较长的场景

### 2. Modbus ASCII

- 同样基于串行通信
- 数据以 **ASCII 字符**格式传输，可读性好
- 使用 **LRC（纵向冗余校验）**
- 效率低于 RTU，较少使用

### 3. Modbus TCP/IP（现代主流）

- 基于**以太网 / TCP/IP** 协议
- 默认端口：**502**
- 不再需要主从严格限制，支持多主站
- 适合现代网络化工厂、远程监控

------

## 五、四种数据类型（寄存器）

Modbus 将设备数据组织为四种类型：

| 类型                              | 名称 | 数据特性       | 大小   | 典型用途               |
| --------------------------------- | ---- | -------------- | ------ | ---------------------- |
| **线圈 (Coil)**                   | 0x区 | 可读写，布尔值 | 1 bit  | 控制输出（继电器开关） |
| **离散输入 (Discrete Input)**     | 1x区 | 只读，布尔值   | 1 bit  | 数字输入状态           |
| **保持寄存器 (Holding Register)** | 4x区 | 可读写，整数   | 16 bit | 设定值、参数配置       |
| **输入寄存器 (Input Register)**   | 3x区 | 只读，整数     | 16 bit | 传感器测量值           |

------

## 六、常用功能码（Function Code）

| 功能码 | 十六进制 | 操作           |
| ------ | -------- | -------------- |
| 01     | 0x01     | 读线圈         |
| 02     | 0x02     | 读离散输入     |
| 03     | 0x03     | 读保持寄存器 ⭐ |
| 04     | 0x04     | 读输入寄存器 ⭐ |
| 05     | 0x05     | 写单个线圈     |
| 06     | 0x06     | 写单个寄存器 ⭐ |
| 15     | 0x0F     | 写多个线圈     |
| 16     | 0x10     | 写多个寄存器 ⭐ |

------

## 七、数据帧结构（以 RTU 为例）

```
┌──────────┬──────────┬──────────┬──────────┬──────────┐
│ 设备地址  │ 功能码   │  数据区   │  CRC低   │  CRC高   │
│ 1 byte   │ 1 byte  │ N bytes  │ 1 byte   │ 1 byte   │
└──────────┴──────────┴──────────┴──────────┴──────────┘
```

**示例**：主站请求读取从站 01 的保持寄存器，从地址 0x0000 开始，读 2 个：

```
01 03 00 00 00 02 C4 0B
│  │  │──────│ │──────│ └─── CRC 校验
│  │  起始地址  寄存器数量
│  └─ 功能码（读保持寄存器）
└── 从站地址
```

------

## 八、典型应用场景

- 🏭 **工厂自动化**：PLC 与传感器、执行器通信
- ⚡ **电力系统**：电表、保护装置数据采集
- 🌡️ **楼宇自动化**：温控系统、空调、门禁
- 💧 **水处理**：泵站、流量计监控
- 🔋 **新能源**：光伏逆变器、储能系统
- 🛢️ **油气行业**：管道监控、压力采集

------

## 九、Modbus 的优缺点

| 优点           | 缺点                       |
| -------------- | -------------------------- |
| 简单、易实现   | 没有加密，安全性差         |
| 开放免费       | 数据类型简单（只有寄存器） |
| 硬件成本低     | 带宽低，不适合大数据量     |
| 行业通用性强   | 主从模式效率有限           |
| 支持多种物理层 | 错误处理能力较弱           |

------

## 十、一句话总结

> **Modbus = 工业设备间互相"说话"的标准语言**，主站负责"提问"，从站负责"回答"，通过读写寄存器来交换数据，简单、可靠、用了40多年依然是工业通信的基石。

------

如果你有具体的应用场景（比如用 Python 实现 Modbus 通信、或者调试某个设备），我可以进一步帮你深入讲解！

项目实战：用freemodbus控制输出（继电器开关）

## 开关驱动架构



```
┌─────────────────────────────────────────────────────────────────┐
│                        业务层 (business.c)                        │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │  busi_switch_stru: fan_status, light_status              │   │
│   │  控制逻辑: 温度过高→开风扇, 亮度过低→开灯                     │   │
│   └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────┬─────────────────────────────────┘
                                │ 信号量 + 共享内存
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                        中间层 (MidLayer.c)                        │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │  midlayer_switch_light_stru: light_sw                    │   │
│   │  midlayer_switch_fan_stru: fan_sw                        │   │
│   └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────┬─────────────────────────────────┘
                                │ 信号量 + 共享内存
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                   驱动适配层 (SwitchDrvAdaptor.c)                  │
│   ┌─────────────────┐              ┌─────────────────┐          │
│   │ Switch_LIGHT_Ops │              │  Switch_FAN_Ops │          │
│   │ (函数指针结构体)   │              │  (函数指针结构体) │          │
│   └────────┬────────┘              └────────┬────────┘          │
│            │                                │                    │
├────────────┼────────────────────────────────┼────────────────────┤
│            ▼                                ▼                    │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │              Modbus继电器驱动 (switch_drv)                 │   │
│   │   modbus_relay_open_light()  modbus_relay_open_fan()      │   │
│   │   modbus_relay_close_light() modbus_relay_close_fan()     │   │
│   └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## 1. 开关驱动数据结构

### 函数指针抽象 ([SwitchDrvAdaptor.h:9-13](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/DrvApator/SwitchDrvAdaptor.h#L9-L13))



```c
typedef struct 
{
    int (*p_switch_open)(void);    // 打开开关函数指针
    int (*p_switch_close)(void);   // 关闭开关函数指针
} switch_drv_ops_stru;
```

### 开关状态定义 ([common.h](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/Modules/common.h))



```c
#define FAN_CTRL_CLOSE_STATUS   (0)    // 风扇关闭状态
#define FAN_CTRL_OPEN_STATUS    (1)    // 风扇开启状态

#define LIGHT_CTRL_CLOSE_STATUS (0)    // 灯光关闭状态
#define LIGHT_CTRL_OPEN_STATUS  (1)    // 灯光开启状态
```

### 业务层数据结构 ([business.h:17-20](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/Modules/business.h#L17-L20))



```c
typedef struct 
{
    int fan_status;      // 风扇状态
    int light_status;    // 灯光状态
    int reserved[4];     // 预留字段
} busi_switch_stru;
```

### 中间层数据结构 ([MidLayer.h:16-24](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/Modules/MidLayer.h#L16-L24))



```c
typedef struct 
{
    int light_sw;        // 灯光开关命令
} midlayer_switch_light_stru;

typedef struct 
{
    int fan_sw;          // 风扇开关命令
} midlayer_switch_fan_stru;
```

## 2. 开关驱动适配层实现

### 驱动绑定配置 ([SwitchDrvAdaptor.h:15-41](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/DrvApator/SwitchDrvAdaptor.h#L15-L41))



```c
// 使用Modbus继电器控制灯光
#define SWITCH_DRV_ADAPTOR_LIGHT_SWITCH_USING_MODBUS_RELAY
#define SWITCH_LIGHT_DRV_OPS_CONFIG_MODBUS_RELAY {                              \
    .p_switch_open = modbus_relay_open_light,     \
    .p_switch_close = modbus_relay_close_light    \
}

// 使用Modbus继电器控制风扇
#define SWITCH_DRV_ADAPTOR_FAN_SWITCH_USING_MODBUS_RELAY
#define SWITCH_FAN_DRV_OPS_CONFIG_MODBUS_RELAY {                                \
    .p_switch_open = modbus_relay_open_fan,       \
    .p_switch_close = modbus_relay_close_fan      \
}
```

### 灯光控制任务 ([SwitchDrvAdaptor.c:36-67](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/DrvApator/SwitchDrvAdaptor.c#L36-L67))



```c
void switch_drv_adaptor_light_process_task_entry(void *p)
{
    while(1)
    {
        // 等待中间层的控制请求
        xSemaphoreTake(midlayer_switch_light_mmc_ctrl.sem_req, portMAX_DELAY);
        
        // 读取控制命令
        if (LIGHT_CTRL_OPEN_STATUS == 
            (((midlayer_switch_light_stru *)(midlayer_switch_light_mmc_ctrl.p_shared_mem))->light_sw))
        {
            log_i("Open the Light!"); 
            Switch_LIGHT_Ops.p_switch_open();    // 执行开灯操作
        }

        if (LIGHT_CTRL_CLOSE_STATUS == 
            (((midlayer_switch_light_stru *)(midlayer_switch_light_mmc_ctrl.p_shared_mem))->light_sw))
        {
            log_i("Close the Light!"); 
            Switch_LIGHT_Ops.p_switch_close();   // 执行关灯操作
        }
        
        // 响应中间层
        xSemaphoreGive(midlayer_switch_light_mmc_ctrl.sem_ack);
    }
}
```

### 风扇控制任务 ([SwitchDrvAdaptor.c:69-100](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/DrvApator/SwitchDrvAdaptor.c#L69-L100))



```c
void switch_drv_adaptor_fan_process_task_entry(void *p)
{
    while(1)
    {
        // 等待中间层的控制请求
        xSemaphoreTake(midlayer_switch_fan_mmc_ctrl.sem_req, portMAX_DELAY);
        
        // 读取控制命令
        if (FAN_CTRL_OPEN_STATUS == 
            (((midlayer_switch_fan_stru *)(midlayer_switch_fan_mmc_ctrl.p_shared_mem))->fan_sw))
        {
            log_i("Open the FAN!"); 
            Switch_FAN_Ops.p_switch_open();      // 执行开风扇操作
        }

        if (FAN_CTRL_CLOSE_STATUS == 
            (((midlayer_switch_fan_stru *)(midlayer_switch_fan_mmc_ctrl.p_shared_mem))->fan_sw))
        {
            log_i("Close the FAN!"); 
            Switch_FAN_Ops.p_switch_close();     // 执行关风扇操作
        }
        
        // 响应中间层
        xSemaphoreGive(midlayer_switch_fan_mmc_ctrl.sem_ack);
    }
}
```

## 3. Modbus继电器驱动

### 通信协议

- **协议**: Modbus RTU
- **物理层**: RS485串口
- **功能码**: 05 (写单个线圈) 或 0F (写多个线圈)
- **设备**: Modbus继电器模块

### 核心函数



```c
// 灯光控制
int modbus_relay_open_light(void);     // 打开灯光继电器
int modbus_relay_close_light(void);    // 关闭灯光继电器

// 风扇控制
int modbus_relay_open_fan(void);       // 打开风扇继电器
int modbus_relay_close_fan(void);      // 关闭风扇继电器
```

### Modbus任务 ([SwitchDrvAdaptor.c:102-112](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/DrvApator/SwitchDrvAdaptor.c#L102-L112))



```c
void SwitchDrv_Init()
{
    // 创建Modbus主站轮询任务
    xTaskCreate(ModbusMasterPoll_Task, "ModbusMasterPoll_Task", 128, NULL, 2, &MoniterTask_Handler);
    
    // 创建Modbus主站监控任务
    xTaskCreate(ModbusMasterMoniter_Task, "ModbusMasterMoniter_Task", 128, NULL, 2, &ModbusTask_Handler);
    
    // 创建灯光控制任务
    xTaskCreate(switch_drv_adaptor_light_process_task_entry, "switch_light_process_task", 128, NULL, 2, &Switch_Light_Process_Task_Handler);
    
    // 创建风扇控制任务
    xTaskCreate(switch_drv_adaptor_fan_process_task_entry, "switch_fan_process_task", 128, NULL, 2, &Switch_Fan_Process_Task_Handler);
}
```

## 4. Shell调试命令

系统提供了便捷的开关控制调试命令：

| 命令 | 功能     | 示例                      |
| ---- | -------- | ------------------------- |
| `ol` | 打开灯光 | `ol` → 继电器吸合，灯亮   |
| `cl` | 关闭灯光 | `cl` → 继电器断开，灯灭   |
| `of` | 打开风扇 | `of` → 继电器吸合，风扇转 |
| `cf` | 关闭风扇 | `cf` → 继电器断开，风扇停 |

### 命令实现 ([SwitchDrvAdaptor.c:115-145](vscode-webview://0l8uqlckpngficuma8lo585euovgqhbqe55tltba6kkvp2pd20tc/DrvApator/SwitchDrvAdaptor.c#L115-L145))



```c
void ol(int argc, char **argv)
{
    log_d("ol command is running ....");
    Switch_LIGHT_Ops.p_switch_open();    // 直接调用开灯函数
}
ZNS_CMD_EXPORT(ol, open light)

void cl(int argc, char **argv)
{
    log_d("cl command is running ....");
    Switch_LIGHT_Ops.p_switch_close();   // 直接调用关灯函数
}
ZNS_CMD_EXPORT(cl, close light)

void of(int argc, char **argv)
{
    log_d("of command is running ....");
    Switch_FAN_Ops.p_switch_open();      // 直接调用开风扇函数
}
ZNS_CMD_EXPORT(of, open fan)

void cf(int argc, char **argv)
{
    log_d("cf command is running ....");
    Switch_FAN_Ops.p_switch_close();     // 直接调用关风扇函数
}
ZNS_CMD_EXPORT(cf, close fan)
```

## 5. 完整控制流程示例

### 场景：温度过高自动开启风扇



```
1. 传感器层: NTC采集温度值 → 35°C
       ↓
2. 驱动适配层: SampleDrvAdaptor读取温度 → 存入共享内存
       ↓
3. 中间层: MidLayer接收温度数据 → 传递给业务层
       ↓
4. 业务层: business判断温度>30°C → 设置fan_status=1
       ↓
5. 中间层: MidLayer接收控制命令 → 设置fan_sw=1
       ↓
6. 驱动适配层: SwitchDrvAdaptor接收命令 → 调用Switch_FAN_Ops.p_switch_open()
       ↓
7. Modbus驱动: 发送Modbus命令 → 继电器吸合 → 风扇开启
```

## 6. 设计优点

| 优点         | 说明                                         |
| ------------ | -------------------------------------------- |
| **硬件抽象** | 通过函数指针，可灵活切换不同类型的继电器模块 |
| **模块解耦** | 业务逻辑与硬件控制完全分离                   |
| **实时响应** | 基于FreeRTOS信号量，实现即时控制响应         |
| **易于扩展** | 可轻松添加新的开关设备（如空调、窗帘等）     |
| **调试友好** | 提供Shell命令，可手动控制开关                |

## 7. Modbus继电器接线示意



```
STM32F103ZET6          Modbus继电器模块           负载
┌─────────┐           ┌─────────────┐          ┌─────┐
│    TX ──┼───────────┼── RX        │          │     │
│    RX ──┼───────────┼── TX        │    COM───┼── 灯/风扇
│   GND ──┼───────────┼── GND       │    NO────┼──     │
└─────────┘           └─────────────┘          └─────┘
    UART                   RS485                  220V AC
```

# 十二 项目目录

```
Project_Root/
├── .vscode/                # VS Code 配置文件（包含编译器路径、调试设置）
├── build/                  # 编译产物（生成的 .bin / .hex 文件）
│
├── Core/                   # 【第一层：顶层业务层 (App Layer)】
│   ├── Inc/                # 存放应用逻辑相关的头文件
│   └── Src/                # 存放 main.c 和任务处理逻辑
│       └── main.c          // 核心大脑：负责 FreeRTOS 任务调度与逻辑决策
│
├── Modeules/               # 【第二层：中间采集与控制层 (Middle Layer)】
│   ├── sensor_manager/     # 采集模块管理
│   │   ├── temp_collect.c  // 核心转换：将底层读到的电压值计算为摄氏度(℃)
│   │   └── temp_collect.h  // 暴露给业务层的接口，如 float Get_RoomTemp(void);
│   └── control_manager/    # 控制模块管理
│       ├── switch_ctrl.c   // 逻辑控制：如根据温度判断是否执行开关动作
│       └── switch_ctrl.h   // 暴露给业务层的接口，如 void Fan_Power_Set(uint8_t state);
│
├── Drivers/                # 【第三层：底层驱动层 (Driver Layer / Bottom)】
│   ├── BSP/                # 板级支持包（由你手动创建，存放特定硬件驱动）
│   │   └── NTC/            # NTC 底层驱动
│   │       ├── ntc_driver.c // 硬件细节：负责 ADC 初始化、启动采样、读取原始值
│   │       └── ntc_driver.h 
│   ├── STM32F1xx_HAL_Driver # 厂商底层库（由 CubeMX 维护，严禁手动修改）
│   └── CMSIS/              # ARM 内核标准接口
│
├── Middlewares/            # 中间件（FreeRTOS 源码及配置文件）
├── CMakeLists.txt          # 构建脚本（新增上述任何 .c 文件夹都需在此登记）
├── rtos_temp.ioc           # CubeMX 项目原件（引脚与外设配置图纸）
└── STM32F103XX_FLASH.ld    # 链接脚本（规定 Flash/RAM 分配）


```

# 十三 使用git进行代码回滚

代码已经推送到 GitHub 后想要回滚，是非常常见的操作。因为代码已经同步到了远程仓库，我们通常有两种策略来处理：**安全撤销（Revert）** 或 **强制回退（Reset）**。

在开始之前，请先在 VS Code 的集成终端（或其他命令行工具）中运行以下命令，找到你想操作的**提交记录（Commit Hash）**：

Bash



```
git log --oneline
```

这会列出你最近的提交记录，例如：

> `a1b2c3d` 修改了main()  <-- 假设这是你想撤销的提交 `e4f5g6h` 上一个稳定版本 <-- 假设这是你想回退到的版本

请记下对应的短哈希值（前 7 位字母数字）。

------

### 策略一：安全撤销（推荐，团队协作首选）

**原理**：`git revert` 会创建一个**新的提交**，这个新提交的内容正好与你想要撤销的那次提交相反。这不会抹除原本的历史记录，对 GitHub 上的其他协作者非常安全。

**适用场景**：你希望保留“修改了main()”这条记录作为历史，但想把代码改回之前的样子。

1. **撤销指定的提交**（这里的 hash 是“修改了main()”那个提交的哈希）：

   Bash

   

   ```
   git revert <那个“修改了main()”的commit-hash>
   ```

   *此时可能会弹出一个编辑器让你确认提交信息（比如 Vim），直接保存退出即可（Vim 中输入 :wq 然后回车）。*

2. **正常推送到 GitHub**：

   Bash

   

   ```
   git push origin main
   ```

*(注：如果你的默认分支叫 master，请将 main 替换为 master)*

------

### 策略二：强制回退（彻底抹除历史）

**原理**：`git reset` 会把你的本地代码指针直接强行拨回之前的某个节点，丢弃之后的所有提交。然后你需要“强制推送”覆盖 GitHub 上的记录。

**适用场景**：这是你个人的独立项目，或者你百分百确定没有其他人拉取过你刚刚提交的代码，且你希望历史记录里**彻底消失**那次错误的提交。

1. **回退到目标版本**（这里的 hash 是你想**回到**的那个“上一个稳定版本”的哈希，不是修改了 main 的那个）：

   Bash

   

   ```
   git reset --hard <你想回退到的commit-hash>
   ```

   *注意：--hard 会丢弃你本地工作区未保存的修改，请确保没有需要保留的未提交代码。*

2. **强制推送到 GitHub**：

   Bash

   

   ```
   git push -f origin main
   ```

**总结建议**：如果你平时习惯用 Claude Code CLI 或类似的高效 AI 开发流，保持 Git 历史树的干净往往很有帮助。如果是自己做项目调试（比如测试某些配置），直接用策略二（Reset + Force Push）**最干净利落；如果是为了保留试错过程，用**策略一（Revert）更稳妥。



