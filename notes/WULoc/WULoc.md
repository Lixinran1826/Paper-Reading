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

   **目标**：解决 Wi-Fi 和 UWB 物理层的不兼容性，使 Wi-Fi 设备能够与 UWB anchor通信。

   **方法**：借鉴 **跨技术通信（Cross-Technology Communication, CTC）**，无需修改硬件，通过**定制 Wi-Fi packet**，让 UWB anchor能将其识别为有效的 UWB 信号。

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
2. **突破 Wi-Fi 和 UWB 之间的通信障碍，提升定位精度**：设计 **Wi-Fi-UWB 连接建立机制**，克服 **带宽差异和帧结构不同** 的问题。提出 **周期性同步机制**，解决 UWB anchor时钟不稳定的问题，并利用 **皮秒级时间戳** 进行 **TDoA 计算** 和 **双曲线测距定位**。
3. **系统实现与评估，验证 WULoc 的有效性和鲁棒性**：在 **USRP N310（Wi-Fi 设备）和 UWB DW1000（UWB anchors）** 上实现并测试。在室内实验室、体育馆和户外校园进行实验，**在 240 米距离下实现 5.3 米平均误差，定位范围比现有方法提升 242%**，并在非视距（NLoS）和运动环境下表现出良好的稳定性。

## Motivation

#### opportunity 1 UWB

整合 Wi-Fi 和 UWB，实现长距离低信噪比接收和高精度时间同步 timing，进而实现长距离、高精度的定位。

#### oppertunity 2 Cross-Technology Communication

现有的处理不同技术之间通信的方案：

宽带到窄带：**signal emulation**

窄带到宽带：**symbol encoding**

由于低成本 UWB 设备只能处理 UWB 兼容信号，无法从不相容的 Wi-Fi 信号中提取模式，因此 Wi-Fi 和 UWB 通信不能采用 symbol encoding 的方法。文章改进了 signal emulation 方法用于 Wi-Fi 和 UWB 通信。

## System Overview

![system overview](https://github.com/Lixinran1826/Paper-Reading/blob/main/notes/WULoc/overview.png?raw=true)

step 1: 首先是通过 Wi-Fi-UWB 连接建立向 UWB anchor 传输一个专门定制的 Wi-Fi 数据包，该数据包可模拟 UWB 数据包。

step 2：接收到模拟的 UWB 数据包后，每个 anchor 都会提取粒度为 15 ps 的时间戳，然后在 Wi-Fi-UWB TDoA 计算中用于计算两个anchor之间的到达时间差（TDoA）。

step 3: 用双曲三角测量法在基于 Wi-Fi-UWB TDoA 的定位中确定 Wi-Fi 目标的位置。

## System Design

### Wi-Fi-UWB 连接建立

#### 挑战1: Wi-Fi 循环前缀导致的精度损失

Wi-Fi 的 **循环前缀（Cyclic Prefix, CP）** 的持续时间为 3.2 μs，在 Wi-Fi OFDM（正交频分复用）信号中，每个符号都会添加一个循环前缀，以减少多径干扰。然而，这个 CP 是不可控的（uncontrollable），并且时间跨度较长。**UWB 信号前导码（preamble unit）** 的持续时间为 0.999 μs。UWB 的前导码用于同步和信道估计，它的时间尺度远小于 Wi-Fi 的循环前缀。这会导致 Wi-Fi 信号在转换为 UWB 兼容格式时出现精度损失，影响时间同步（TDoA 计算）和信号识别。

#### 解决方案1: 

1. 选择合适的 Wi-Fi 符号持续时间，确保它是 UWB 符号持续时间的整数倍。

    Wi-Fi 802.11ax 6E 标准使得用户可以自行配制 symbol duration 和 CP 时间，UWB 允许选择不同的前导码序列，以适应不同的应用需求和信号对齐要求，通过灵活配置 Wi-Fi 符号结构和 UWB 前导码，可以更好地解决两者的帧结构差异。作者选择合适的 **Wi-Fi 符号持续时间**，确保它是 **UWB 符号持续时间的整数倍**，例如，若 Wi-Fi 符号设为 **4 μs**，而 UWB 符号是 **1 μs**，那么 1 个 Wi-Fi 符号可以等效于 **4 个 UWB 符号**，从而建立基本的映射关系。这样可以减少时间对齐上的误差，使 Wi-Fi 信号更容易模拟 UWB 信号。

2. 选择特定的 UWB code index，使 Wi-Fi 的 CP 与 UWB 更匹配。

   循环前缀（CP）的作用是减少多径干扰；但 UWB 信号结构不同，若直接映射 Wi-Fi 到 UWB，可能会导致 CP 之后的信号失真。作者选择合适的 **UWB code index**，使得 UWB 信号的结构 **尽可能接近 Wi-Fi 的 CP**，确保 CP 之后的信号基本不失真。

   IEEE 802.11ax 标准规定的 **Wi-Fi 符号的保护间隔（GI）**选项为0.8μs、1.6μs和3.2μs，UWB 符号持续时间 ≈ 1μs，因此Wi-Fi 的 GI 不总是 UWB 符号的整数倍，这样会导致 CP 的分割出现误差而引入误差。GI 无法避免，但可以选择合适的值来降低误差：选择 **GI =  3.2μs**，因为3.2 μs 的 GI 只会影响一个 UWB 符号的 20%。

   ![Different duration of GI](https://github.com/Lixinran1826/Paper-Reading/blob/main/notes/WULoc/Different%20duration%20of%20GI.png?raw=true)

   Wi-Fi 的 **CP 会复制符号最后 3.2 μs 的部分到符号开头**，形成一个前导段。由于 **UWB 符号是 1 μs 长**，3.2 μs CP 对应 **3 个完整的 UWB 符号 + 额外的 0.2 μs 误差部分**，这 **0.2 μs** 的 CP 误差部分会破坏 UWB 符号的连续性。每个 UWB 符号（1 μs）包含 **32 个code bits**，0.2μs 对应**7个bits**，相当于 **Wi-Fi CP 覆盖了 7 个 UWB bits**。如果 **Wi-Fi CP 复制的最后 7 个 UWB 码比特与 UWB 符号开头的 7 个码比特相似**，则信号的**不连续性最小**，从而 **减少误差**。**选择 UWB 前导码 Code 4**，确保 **Wi-Fi CP 复制部分与 UWB 开头部分最相似。**

   图 6 比较了 Wi-Fi 模拟的 UWB 信号和真实的 UWB 信号，可以看出由于 CP，0.2 μs之前的部分完全不同。不过这部分只占信号的一小部分，而且不是每个 UWB 符号都会出现，因此可以减少由此产生的误差。另一方面，由于带宽的限制，仿真前导码更加平滑。通过我们的实验，商用 UWB 设备能够成功地检测和解调由 Wi-Fi 仿真的 UWB 信号。

![Illustration of UWB signal and emulate signal](https://github.com/Lixinran1826/Paper-Reading/blob/main/notes/WULoc/Illustration%20of%20UWB%20signal%20and%20emulate%20signal.png?raw=true)

<img src="https://github.com/Lixinran1826/Paper-Reading/blob/main/notes/WULoc/UWB%20code%20index.png?raw=true" alt="UWB code index" style="zoom:50%;" />

#### 挑战2: 带宽差距

Wi-Fi 和 UWB 之间存在显著的带宽差距（Wi-Fi 设备通常只能产生 500 MHz 带宽的信号，而 UWB 具有更宽的带宽）。

#### 解决方案2: 

**非相干检测（non-coherent detection）**是一种无需精确相位信息的信号检测方法。在传统的相干检测中，接收器需要估计信号的相位和频率，以便进行精确的解调。而在非相干检测中，接收器仅依赖于信号的幅度而非相位，从而能够在信号受到噪声或失真的情况下仍然有效地进行解调。利用 UWB 非相干检测特性，在前导码中匹配解调后的符号序列来实现非相干检测，这样只要前导码的解调符号序列与预期的前导码序列匹配，接收器就能正确地接收数据，这种方法允许 Wi-Fi 设备产生的**窄带信号**仍能被 UWB 设备正确接收。

此外，充分利用**UWB 物理层的鲁棒性（抗干扰能力）**，保证即使 Wi-Fi 信号受限，UWB 设备仍然可以正确接收数据：UWB 通过分布大量的低功率脉冲，使其更能抵抗干扰、多径衰落和频率选择性衰落。即使部分信号受损，其他子载波仍可提供有效信息传输；UWB 采用二进制相移键控（BPSK）等技术，提高抗干扰能力；UWB 系统采用**前向纠错码（FEC）**，即使信号发生失真，也能纠正比特级错误，提高解码成功率。

### Wi-Fi-UWB TDoA Calculation

将多个 UWB 设备作为 anchor，利用这些 anchor 提供的高精度时间戳来定位 Wi-Fi 目标，从而计算出每对 UWB 锚点之间的 TDoA。



### Wi-Fi-UWB TDoA based Localization
