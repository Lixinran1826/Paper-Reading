# WULoc

------

提出了一种新的 Wi-Fi-UWB 连接建立方式，使 Wi-Fi 设备能够与 **多个 UWB 锚点（anchors）** 进行通信，Wi-Fi 设备到 UWB anchor 的链路中提取皮秒级（10⁻¹²秒）精度的时间戳，使用手机、电脑等移动Wi-Fi设备实现长距离、高精度的定位。

## Introduction

Wi-Fi定位中使用Wi-Fi设别作为anchors，使用**飞行时间（ToF）和角度信息**估计目标 Wi-Fi 设备的位置，存在不足之处：

1. **信噪比（SNR）下降影响定位精度**：在**远距离场景**下，Wi-Fi 信号的 SNR 会显著降低，导致测量的飞行时间和角度信息受到噪声干扰，影响定位准确性。
2. **长距离通信易受干扰或完全失效**：研究表明，**在 50 米距离时，802.11ax（Wi-Fi 6）的通信性能下降 56%，在 125 米时完全丢失信号**，这严重限制了其在大范围定位中的适用性。
3. **Wi-Fi 的 OFDM 调制方式对 SNR 下降敏感**：Wi-Fi 主要采用 **正交频分复用（OFDM）** 进行调制/解调，该技术虽然适用于高吞吐量通信，但对 SNR 下降 **极为敏感**，因此难以同时提供 **远距离覆盖和高精度定位**。

### Challenges

1. **Wi-Fi 和 UWB 物理层的不兼容性**：
   - **调制方式不同**：Wi-Fi 采用 **QAM+OFDM**，而 UWB 使用 **BPSK+BPM**，两者信号格式不同，无法直接通信。
   - **带宽差异大**：Wi-Fi **最大带宽 160 MHz**，而 UWB **带宽 500 MHz**，Wi-Fi 设备无法生成标准的 UWB 信号波形。
   - **帧结构不同**：Wi-Fi 包含 **循环前缀（Cyclic Prefix, CP）**，而 UWB 没有，这种结构差异增加了数据转换的复杂性。

2. **UWB anchor 的时间戳数据不稳定**：

- **低成本 UWB 设备的本地振荡器不稳定**，导致时间戳数据产生 **抖动（jitter）**，影响 **到达时间差（TDoA）** 和最终定位精度。

### Methods

1. **Wi-Fi-UWB 连接建立（Wi-Fi-UWB Connection Establishment）**

   **目标**：解决 Wi-Fi 和 UWB 物理层的不兼容性，使 Wi-Fi 设备能够与 UWB 锚点通信。

   **方法**：借鉴 **跨技术通信（Cross-Technology Communication, CTC）**，无需修改硬件，通过**定制 Wi-Fi packet**，让 UWB 锚点能将其识别为有效的 UWB 信号。

   **关键点**：利用 Wi-Fi 信号调制能力，以及 UWB 在解调过程中对信号失真具有较高容忍度，从而克服两者的调制方式、带宽和帧结构的差异。

2. **Wi-Fi-UWB TDoA 计算（Wi-Fi-UWB TDoA Calculation）**

   **目标**：克服低成本 UWB anchor 的时钟不稳定性，提高 **TDoA** 计算的精度。

   **方法**：引入 **周期性校准机制**，定期同步 UWB anchor 的时钟。

   **关键点**：利用 UWB 振荡器在时域的变化特性，通过 广播**时钟对齐数据包**，减少由于设备差异导致的 TDoA 误差，提高定位精度。

3. **基于 Wi-Fi-UWB TDoA 的定位（Wi-Fi-UWB TDoA based Localization）**

   **目标**：使用 **双曲线三角测量（Hyperbolic Triangulation）** 进行高精度 Wi-Fi 设备定位。

   **方法**：综合利用多个 **UWB anchor 计算的 TDoA 数据** 进行定位。

   **创新性**：WULoc 是首个通过 **Wi-Fi-UWB 连接** 实现远距离、高精度 Wi-Fi 设备定位的设计，可与现有的 GPS 解决方案互补，为 CPS（Cyber-Physical Systems）应用提供高精度、可直接部署的定位框架。

### Contributions

1. **提出 WULoc 系统，实现远距离高精度 Wi-Fi 定位**：首次利用 **Wi-Fi-UWB 连接** 进行 **超远程高精度 Wi-Fi 设备定位**，将 UWB 设备重新用于 Wi-Fi 目标的 anchor，WULoc 作为 GPS 替代方案，适用于 **高密度城市环境和 GPS 失效的室内场景**。
2. **突破 Wi-Fi 和 UWB 之间的通信障碍，提升定位精度**：设计 **Wi-Fi-UWB 连接建立机制**，克服 **带宽差异和帧结构不同** 的问题。提出 **周期性同步机制**，解决 UWB 锚点时钟不稳定的问题，并利用 **皮秒级时间戳** 进行 **TDoA 计算** 和 **双曲线测距定位**。
3. **系统实现与评估，验证 WULoc 的有效性和鲁棒性**：在 **USRP N310（Wi-Fi 设备）和 UWB DW1000（UWB anchors）** 上实现并测试。在室内实验室、体育馆和户外校园进行实验，**在 240 米距离下实现 5.3 米平均误差，定位范围比现有方法提升 242%**，并在非视距（NLoS）和运动环境下表现出良好的稳定性。

## Motivation

#### opportunity 1 UWB

long-range low-SNR reception and high-precision timing—form the basis of the WULoc approach, enabling the integration of Wi-Fi and UWB for extended range and accurate localization.

#### oppertunity 2 Cross-Technology Communication

宽带到窄带：**signal emulation**

窄带到宽带：**symbol encoding**

