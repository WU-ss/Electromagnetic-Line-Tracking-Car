# Electromagnetic-Line-Tracking-Car

一款基于 MN32 系列 MCU 的**电磁循迹小车**完整解决方案，包含嵌入式软件代码、硬件原理图 / PCB、核心驱动模块及 PID 闭环控制算法，支持快速搭建和调试循迹小车，适用于智能车竞赛、嵌入式学习等场景。

## 🌟 核心特性



* **全栈一体化**：配套软件代码（Keil MDK 工程）+ 硬件设计文件（原理图 / PCB），开箱即用；

* **高性能循迹**：4 路电磁传感器采集 + PID 闭环控制，支持稳定循迹、自动纠偏；

* **模块化设计**：硬件驱动、控制算法、应用层分离，便于扩展和二次开发；

* **丰富硬件支持**：适配电磁传感器、直流电机、TFT 屏幕、六轴传感器（ICM20602）等外设；

* **可视化调试**：支持 TFT 屏幕显示循迹偏差、电机速度等数据，便于调试优化。

## 📋 项目结构



```
Electromagnetic-Line-Tracking-Car/

├── Control/          # 控制算法模块（PID 控制核心）

│   ├── PID.c/h       # 位置式 PID 实现（循迹纠偏核心）

├── Hardware/         # 硬件驱动层（外设驱动）

│   ├── SEEKFREE\_EM\_SENSOR.c/h  # 电磁传感器 ADC 采集驱动

│   ├── SEEKFREE\_MOTOR.c/h      # 直流电机 PWM 驱动（方向+速度控制）

│   ├── SEEKFREE\_18TFT.c/h      # 1.8 寸 TFT 屏幕驱动（数据可视化）

│   ├── SEEKFREE\_ICM20602.c/h   # ICM20602 六轴传感器驱动（姿态融合）

├── MN32\_HAL/         # MN32 单片机 HAL 库（底层硬件抽象）

├── Startup/          # 启动文件（MN32 芯片初始化）

├── User/             # 应用层（主函数+系统整合）

│   ├── main.c        # 主循环（模块初始化+循迹逻辑）

│   ├── System\_Init.c/h  # 系统时钟、外设初始化配置

├── 驱动板原理图.png   # 电机驱动板硬件原理图

├── 运放版原理图.png   # 电磁传感器运放板原理图

├── PCB 文件/         # 硬件 PCB 设计文件（可选）

├── Electromagnetic-Line-Tracking-Car.uvprojx  # Keil MDK 工程文件

└── README.md         # 项目说明文档（本文档）
```

## 🛠️ 环境要求

### 1. 软件环境



* 开发工具：Keil MDK 5.28+（支持 MN32 系列 MCU）

* 编译标准：C99

* 依赖库：MN32 HAL 库（已集成在 `MN32_HAL/` 目录）

* 辅助工具：STM32CubeMX（可选，用于时钟配置）、串口调试助手（如 SecureCRT）

### 2. 硬件环境



* 主控芯片：MN32 系列 MCU（如 MN32F103）

* 核心外设：


  * 电磁传感器模块（4 路，支持 ADC 采集）

  * 直流电机 + 电机驱动板（如 L298N）

  * 1.8 寸 TFT 屏幕（可选，用于数据可视化）

  * ICM20602 六轴传感器（可选，用于姿态优化）

  * 电源模块（7.4V 锂电池 + 5V/3.3V 稳压）

* 硬件配套：循迹赛道（铺设电磁线）

## 🚀 快速开始

### 1. 克隆仓库



```
git clone git@github.com:WU-ss/Electromagnetic-Line-Tracking-Car.git

cd Electromagnetic-Line-Tracking-Car
```

### 2. 打开工程



* 用 Keil MDK 打开 `Electromagnetic-Line-Tracking-Car.uvprojx` 文件

* 工程自动加载所有源码和 HAL 库，无需额外配置路径

### 3. 硬件适配（关键步骤）

根据实际硬件修改以下参数，确保代码与硬件匹配：

#### (1) 引脚定义修改



* 电磁传感器 ADC 引脚：修改 `Hardware/SEEKFREE_EM_SENSOR.h` 中的 `EM_SENSOR_CH0~CH3`（对应原理图 ADC 通道）

* 电机驱动引脚：修改 `Hardware/SEEKFREE_MOTOR.h` 中的 PWM 通道（`MOTOR_LEFT_PWM_CH`）和方向 GPIO 引脚（`GPIO_PIN_0/1`）

* 屏幕 / 传感器引脚：参考对应驱动文件中的引脚定义，与硬件原理图对齐

#### (2) 核心参数初始化



* 在 `User/main.c` 中调整：


  * 基础速度 `base_speed`（默认 500，范围 0\~1000）

  * PID 参数 `PID_Init(&pid_track, 2.0f, 0.1f, 0.5f, 50.0f, 200.0f)`（KP/KI/KD 需根据实际调试优化）

  * 传感器权重 `weight[4] = {-30, -10, 10, 30}`（对应 4 路传感器的物理位置权重）

### 4. 编译与下载



* 在 Keil MDK 中点击「编译」（Build），确保无错误（0 Error, 0 Warning）

* 连接编程器（如 J-Link、ST-Link）到 MN32 开发板，点击「下载」（Download）将程序烧录到芯片

* 断电后连接小车电源（锂电池），准备循迹测试

### 5. 循迹测试



1. 将小车放置在**电磁赛道**上（赛道需铺设通有高频电流的导线）；

2. 开启电源，系统自动初始化（传感器校准、电机自检）；

3. 小车将根据电磁传感器采集的信号，通过 PID 算法自动纠偏，沿赛道循迹行驶；

4. 可通过 TFT 屏幕查看实时数据（循迹偏差 `track_error`、电机速度 `left_speed/right_speed`）。

## 📖 核心模块说明

### 1. 电磁传感器模块（SEEKFREE\_EM\_SENSOR）



* 功能：采集赛道电磁信号，转换为 ADC 数值，计算循迹偏差；

* 关键函数：


  * `EM_Sensor_Init()`：ADC 初始化，配置多通道连续采集；

  * `EM_Sensor_Calibrate()`：传感器校准（消除零点偏移）；

  * `EM_Sensor_Get_Error()`：通过加权平均法计算循迹偏差（单位：mm）。

### 2. 电机驱动模块（SEEKFREE\_MOTOR）



* 功能：通过 PWM 控制电机转速，GPIO 控制电机方向；

* 关键函数：


  * `Motor_Init()`：初始化 PWM 定时器（10kHz 频率）和方向 GPIO；

  * `Motor_Set_Speed()`：设置左右电机速度（范围 -1000\~1000，负值为倒车）。

### 3. PID 控制模块（PID）



* 功能：根据循迹偏差，计算电机转向补偿量，实现闭环控制；

* 参数说明：


  * KP（比例项）：主要纠偏作用，过大易超调，过小响应慢；

  * KI（积分项）：消除静态偏差，过大易震荡；

  * KD（微分项）：抑制超调，提升稳定性，过大会放大噪声。

## 🛠️ 调试指南

### 1. 硬件调试



* 传感器校准：空载时运行 `EM_Sensor_Calibrate()`，确保校准后 `cali_data` 接近 0；

* 电机测试：注释 PID 控制逻辑，固定电机速度（如 `motor.left_speed = 300`），验证电机转向和转速正常；

* 信号检测：用万用表测量电磁传感器输出电压，确保赛道上的信号强度稳定（避免虚焊、导线接触不良）。

### 2. 软件调试



* 串口打印：在 `main.c` 中添加串口输出，打印 `track_error`、`pid.output` 等参数，分析偏差变化；

* TFT 可视化：启用 `SEEKFREE_18TFT` 驱动，在屏幕上绘制偏差曲线和电机速度，直观观察控制效果；

* PID 参数优化：

1. 先设 KI=0、KD=0，逐步增大 KP 至小车轻微震荡；

2. 减小 KP 至震荡消失，再逐步增大 KI 消除静态偏差；

3. 最后添加 KD 抑制超调，提升循迹平滑度。

## 📄 硬件文件说明



* `驱动板原理图.png`：电机驱动板电路设计（包含 L298N 驱动芯片、电源管理、GPIO 接口）；

* `运放版原理图.png`：电磁传感器信号放大电路（包含运算放大器，用于增强传感器信号灵敏度）；

* `PCB 文件/`：硬件 PCB 设计文件（可直接用于打样生产，适配小车机械结构）。

## 🔧 扩展功能



* 姿态融合：集成 `SEEKFREE_ICM20602` 六轴传感器，通过卡尔曼滤波融合加速度计和陀螺仪数据，优化高速循迹稳定性；

* 速度闭环：添加编码器模块，实现电机速度闭环控制，提升不同路况下的速度一致性；

* 赛道识别：扩展电磁传感器数量（如 8 路），支持弯道、坡道、交叉路口等复杂赛道识别。

## 📋 许可证

本项目基于 [MI](LICENSE)[T](LICENSE)[ 许可](LICENSE)[证](LICENSE) 开源，允许个人 / 商业使用、修改和分发，使用时请保留原作者信息。

## 🤝 贡献指南



1. Fork 本仓库；

2. 创建特性分支（`git checkout -b feature/optimize-pid`）；

3. 提交修改（`git commit -m 'Optimize PID parameters for high speed'`）；

4. 推送到分支（`git push origin feature/optimize-pid`）；

5. 打开 Pull Request，描述修改内容和测试效果。

## 📞 联系方式



* 仓库地址：[htt](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[ps://](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[githu](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[b.com](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[/WU-s](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[s/Ele](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[ctrom](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[agnet](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[ic-Li](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[ne-Tr](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[ackin](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)[g-Car](https://github.com/WU-ss/Electromagnetic-Line-Tracking-Car)

* 若有问题或优化建议，欢迎提交 Issue 或联系作者。

> （注：文档部分内容可能由 AI 生成）