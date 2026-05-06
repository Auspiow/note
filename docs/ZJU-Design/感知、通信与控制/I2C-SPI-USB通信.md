在b站上发现了一个讲的很好的up主：蓝指针科普

### I2C

> 半双工通信

为了解决早期电视机内部芯片间通信布线复杂，并行总线占用引脚多、PCB面积大的问题，飞利浦半导体提出两线式串行总线。

 仅使用 SDA 数据线与 SCL 时钟线，多设备共享同一总线，通过地址区分

| 发展阶段 | 1980     | 1992     | 1998     | 2007      | 2010s               | 2010s        |
| -------- | -------- | -------- | -------- | --------- | ------------------- | ------------ |
| 速率     | 100 kbps | 400 kbps | 3.4 Mbps | 1~5 Mbps  | /                   | 12.5 Mbps+   |
| 发展     | 内部芯片 | /        | Hs-mode  | Fast-mode | 传感器、RTC、显示屏 | 高性能传感器 |

主设备：MCU，从设备：显示屏、温度传感器或者风扇

时钟线传输周期性的高电平，从设备会在高电平的时候读取数据线的内容。

START：高到低，起始信号；STOP：低到高，终止信号

写入数据：

| S    | 设备地址 | W    | A    | 寄存器地址 | A    | 写入数据 | A    | P    |
| ---- | -------- | ---- | ---- | ---------- | ---- | -------- | ---- | ---- |
|      | 7 bits   | 0    |      |            |      |          |      |      |

读取数据：

| S    | 设备地址 | W    | A    | 寄存器地址 | A    | S    | 设备地址 | R    | A    | 读取的数据 | A    | P    |
| ---- | -------- | ---- | ---- | ---------- | ---- | ---- | -------- | ---- | ---- | ---------- | ---- | ---- |
|      | 7 bits   | 0    |      |            |      |      |          | 1    |      |            | NACK |      |

注意从导数第二个ACK之后，从设备给主设备发数据，主设备给从设备发NACK，并发送终止信号

#### 常见设备地址

| 设备类型         | 常见芯片          | 7位地址 (Hex) | 地址位控制 (引脚)           | 功能                         |
| ---------------- | ----------------- | ------------- | --------------------------- | ---------------------------- |
| IMU (六轴传感器) | MPU6050 / MPU6500 | 0x68          | AD0 引脚接高电平变为 0x69   | 测量加速度、角速度（陀螺仪） |
| OLED 显示屏      | SSD1306 / SH1106  | 0x3C          | 改变背面电阻位置可变为 0x3D | 0.96/1.3寸小屏幕显示         |
| EEPROM (存储)    | AT24C02 / AT24C32 | 0x50          | A0/A1/A2 组合可变 0x50~0x57 | 掉电不丢失的数据存储         |
| RTC (实时时钟)   | DS3231 / DS1307   | 0x68          | 固定地址                    | 获取精确的时间、日期         |
| 温湿度传感器     | AHT10 / AHT20     | 0x38          | 固定地址                    | 测量环境温度和湿度           |
| 气压计/高度计    | BMP280 / BME280   | 0x76          | SDO 引脚接高电平变为 0x77   | 测量大气压和海拔高度         |

#### Arduino开发

SDA：A4    SCL：A5

Arduino Wire 库 API 速查表

| 函数                                | 功能简述                                               |
| ----------------------------------- | ------------------------------------------------------ |
| Wire.begin()                        | 以主机 (Master) 模式初始化 I2C 总线                    |
| Wire.begin(address)                 | 以从机 (Slave) 模式加入总线，`address` 为 7 位从机地址 |
| Wire.setClock(hz)                   | 修改 I2C 通信时钟频率（如 100000 或 400000）           |
| Wire.beginTransmission(address)     | 启动对指定地址从机的写操作（开启缓冲区）               |
| Wire.write(value/string)            | 向缓冲区写入单个字节/字符串数据                        |
| Wire.write(data, length)            | 向缓冲区写入指定长度的字节数组                         |
| Wire.endTransmission()              | 真正发送缓冲区内容并结束传输，返回状态码               |
| Wire.requestFrom(address, quantity) | 主机向从机请求读取指定数量的字节                       |
| Wire.available()                    | 返回当前接收缓冲区中可用的字节数                       |
| Wire.read()                         | 从缓冲区读取一个字节的数据                             |
| Wire.onReceive(handler)             | 仅从机： 注册当从机收到主机数据时的回调函数            |
| Wire.onRequest(handler)             | 仅从机： 注册当主机请求数据时的回调函数                |

给设备0x68写数据

```c
Wire.beginTransmission(0x68);
Wire.write(0x3B);             // 寄存器地址
Wire.write(0x01);             // 要修改的值
Wire.endTransmission();
```

读取温湿度传感器SHT20

```c
#include <Wire.h>
#define SHT20_ADDR 0x40

void setup() {
  Wire.begin();
  Serial.begin(9600);
}

float readTemperature() {
  Wire.beginTransmission(SHT20_ADDR);
  Wire.write(0xF3);
  Wire.endTransmission();

  delay(100);

  Wire.requestFrom(SHT20_ADDR, 3);
  if (Wire.available() >= 3) {
    uint16_t raw = (Wire.read() << 8) | Wire.read();
    raw &= 0xFFFC;
    return -46.85 + 175.72 * raw / 65536.0;
  }
  return -999;
}

void loop() {
  float temp = readTemperature();
  Serial.print("Temp: ");
  Serial.println(temp, 2);
  delay(2000);
}
```



### SPI

> 全双工

| 信号线  | 全称                       | 作用                                                         | 方向                | 电平逻辑 (通常)         |
| ------- | -------------------------- | ------------------------------------------------------------ | ------------------- | ----------------------- |
| SCLK    | Serial Clock               | 串行时钟，提供数据传输的同步脉冲。时钟频率决定了通信速率。   | 主 $\rightarrow$ 从 | 由主设备产生脉冲        |
| MOSI    | Master Out Slave In        | 主出从入：主设备通过此线将控制指令或数据发送给从设备。       | 主 $\rightarrow$ 从 | 高/低电平               |
| MISO    | Master In Slave Out        | 主入从出：从设备通过此线将状态或数据反馈给主设备。           | 从 $\rightarrow$ 主 | 高/低电平               |
| CS / SS | Chip Select / Slave Select | 片选/从机选择：用于激活特定的从设备。多个从设备共用总线时，通过此线区分。 | 主 $\rightarrow$ 从 | 低电平有效 (Active Low) |

两者数据通过移位寄存器进行传输，简单说就是敌进我退，敌退我进。

SPI 时序：

| 模式 (Mode) | CPOL | CPHA | 空闲电平 | 采样时刻             |
| ----------- | ---- | ---- | -------- | -------------------- |
| Mode 0      | 0    | 0    | 低电平   | 第 1 个边沿 (上升沿) |
| Mode 1      | 0    | 1    | 低电平   | 第 2 个边沿 (下降沿) |
| Mode 2      | 1    | 0    | 高电平   | 第 1 个边沿 (下降沿) |
| Mode 3      | 1    | 1    | 高电平   | 第 2 个边沿 (上升沿) |

SPI 主从：

| 组件名称       | 片选信号线 | 逻辑状态 | 选通效果                      | MISO 状态                |
| -------------- | ---------- | -------- | ----------------------------- | ------------------------ |
| Flash 存储器   | CS0        | 0 (Low)  | 选中：接收指令并存储/读出数据 | 处于驱动状态（输出数据） |
| ADC 采样芯片   | CS1        | 0 (Low)  | 选中：开始转换并传输模拟数值  | 处于驱动状态（输出数据） |
| Display 显示屏 | CS2        | 0 (Low)  | 选中：接收显存数据进行刷新    | 处于驱动状态（输出数据） |
| 全总线         | 所有 CS    | 1 (High) | 空闲：没有任何设备在通信      | 高阻态 (Hi-Z)：防止干扰  |

#### Arduino开发

| 开发板型号         | SCLK    | MOSI    | MISO    | SS (默认) | 备注                                  |
| ------------------ | ------- | ------- | ------- | --------- | ------------------------------------- |
| Arduino UNO / Nano | D13     | D11     | D12     | D10       | 经典的 ATmega328P 布局                |
| Arduino Mega 2560  | D52     | D51     | D50     | D53       | 适用于多设备的大型项目                |
| Arduino Leonardo   | ICSP-3  | ICSP-4  | ICSP-1  | 无默认    | 引脚未引出至数字 IO，需查阅 ICSP 接口 |
| ESP32 (Default)    | GPIO 18 | GPIO 23 | GPIO 19 | GPIO 5    | 通常指 VSPI 接口，引脚可灵活映射      |

SPI 标准库常用函数简表

| 函数名称               | 功能说明                                         |
| ---------------------- | ------------------------------------------------ |
| SPI.begin()            | 初始化 SPI 总线（设置引脚模式并拉高 SS）。       |
| SPI.beginTransaction() | 根据指定的频率、位序和模式配置 SPI 总线。        |
| SPI.transfer()         | 核心传输函数：发送 1 字节数据的同时接收 1 字节。 |
| SPI.transfer16()       | 连续发送并接收 2 字节（16 位）数据。             |
| SPI.endTransaction()   | 结束当前的 SPI 事务，释放总线配置。              |
| SPI.end()              | 关闭 SPI 外设，停止总线时钟。                    |
| SPI.setBitOrder()      | 已过时：设置高位优先 (MSB) 或低位优先 (LSB)。    |
| SPI.setDataMode()      | 已过时：设置 SPI 模式（0、1、2 或 3）。          |
| SPI.setClockDivider()  | 已过时：设置相对于系统主频的时钟分频因子。       |

读取SPI设备寄存器

```c
#include <SPI.h>
const int CS_PIN = 10;

void setup() {
  Serial.begin(9600);
  SPI.begin();
  pinMode(CS_PIN, OUTPUT);
  digitalWrite(CS_PIN, HIGH); 
}

uint8_t readRegister(uint8_t reg) {
  SPI.beginTransaction(SPISettings(1000000, MSBFIRST, SPI_MODE0));
  digitalWrite(CS_PIN, LOW);
  
  // 将最高位置 1 表示“读取”
  SPI.transfer(reg | 0x80);
  
  // 发送空字节(0x00)以产生时钟脉冲，从而交换回从设备内部的数据
  uint8_t value = SPI.transfer(0x00);
  digitalWrite(CS_PIN, HIGH);
  SPI.endTransaction();
  return value; 
}

void loop() {
  uint8_t id = readRegister(0x00);
  Serial.print("Device ID: 0x");
  Serial.println(id, HEX);
  delay(1000); 
}
```



### SPI vs I²C vs UART

| 对比维度     | SPI                            | I²C                          | UART                          |
| ------------ | ------------------------------ | ---------------------------- | ----------------------------- |
| 通信方式     | 同步、全双工                   | 同步、半双工                 | 异步、全双工                  |
| 信号线数量   | 4 根核心线（每增从机需加 CS）  | 2 根 (SDA, SCL)              | 2 根 (TX, RX)                 |
| 通信速度     | 极快 (通常可达 10Mbps-100Mbps) | 中低速 (100k, 400k, 3.4Mbps) | 低速 (常用 115200bps)         |
| 多设备支持   | 靠物理片选线 (CS) 区分         | 靠软件设备地址区分           | 通常仅支持点对点              |
| 传输距离     | 短（板级通信，通常 < 20cm）    | 中短（板级，需上拉电阻）     | 长（配合 RS232/485 可达千米） |
| 协议复杂度   | 极低（直接移位传输）           | 中（需要应答、起始/停止位）  | 低（需约定波特率、校验位）    |
| 典型应用场景 | 高速 Flash、高清屏幕、精密 ADC | 小型传感器、RTC、EEPROM      | 串口调试、GNSS、无线透传模块  |