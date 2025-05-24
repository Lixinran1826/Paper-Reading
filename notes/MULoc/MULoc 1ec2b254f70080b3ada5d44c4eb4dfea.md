# MULoc

#### **消除ToF和相位的误差**

**Tag Overhearing (TO)**：anchors由全局时钟同步，按顺序广播测距数据包，tag监听这些数据包并使用单向测距计算和每个anchor的ToF，计算不同ToF的差值，通过三角测距确定自己的位置

**Anchor Overhearing (AO)**：anchor在发送数据包后持续监听，可以监听其anchor发送的数据包。anchor和tag都计算ToF、PoA

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image.png)

**ToF for TO：**

$i_{th}$ anchor和tag之间的距离：$d_{i,tag}$      **信号传播时间**： $\tau_{i,tag}=d_{i,tag}/c$

$\tau_{i,ref}=d_{i,ref}/c$ 

震荡时钟偏移：$\delta_{tag}$ 、$\delta_{i}$                          

设备启动的初始时间偏移：$\Delta T$             天线偏移：$\psi_{tag}、\psi_i$

数据包发射时刻：$t_i$                                 数据包接收时刻：$t_{i,tag}$

ToF：$r_{i,tag}$

$r_{i,tag} = \tau_{i,tag} + \delta_{tag} \cdot t_{i,tag}-\delta_{i} \cdot t_i + \Delta T_{tag}-\Delta T_{i} + \psi_{tag}-\psi_{i}$

$r_{i,tag} \approx \tau_{i,tag} + (\delta_{tag} -\delta_{i}) \cdot t_i + \Delta T_{tag}-\Delta T_{i} + \psi_{tag}-\psi_{i}$

**Phase for TO:**

$\phi_{i,tag} = [-2\pi f_c \tau_{i,tag} -2\pi f_c (\delta_{tag}-\delta_i) \cdot t_i + \theta_{i,tag}] \mod 2\pi$

$\theta_{i,tag}$：tag和anchor的**初始偏移、天线偏移**

$\theta_{i,tag}= -（\phi_{ini}^{tag}-\phi_{ini}^{i}）-（\phi_{ant}^{tag}-\phi_{ant}^{i}）$

CFO：$2\pi f_c (\delta_{tag}-\delta_i) \cdot t_i$

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image%201.png)

**用AO消除ToF和相位的误差：**选择 $ref（ref\neq i）$anchor，收$i_{th}$ anchor发的数据包

**ToF for AO:**

$r_{i,ref} = \tau_{i,ref} + (\delta_{ref}-\delta_{i}) \cdot t_i + \Delta T_{ref} - \Delta T_{i}+ \psi_{ref}-\psi_{i}$

**Phase for AO:**

$\phi_{i,ref} = [-2\pi f_c \tau_{i,ref} -2\pi f_c(\delta_{ref}-\delta_{i})\cdot t_i + \theta_{i,ref}] \mod 2\pi$

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image%202.png)

TO与AO计算的ToF、Phase中均有关于$i_{th}$ anchor的分量，相减，得到一次差分**Single Difference**

这样可以抵消所有与信号发射$i_{th}$ anchor相关的误差

$r_{i,tag} - r_{i,ref} = \tau_{i,tag} - \tau_{i,ref} + (\delta_{tag} - \delta_{ref}) \cdot t_i + (\Delta T_{tag} - \Delta T_{ref}) + (\psi_{tag} - \psi_{ref})$

$r_{i,tag} - r_{i,ref} = \tau_{i,tag} - d_{i,ref}/c + (\delta_{tag} - \delta_{ref}) \cdot t_i + (\Delta T_{tag} - \Delta T_{ref}) + (\psi_{tag} - \psi_{ref})$

$r_i^{SD}$：一次差分后的$TDoA$

$r_i^{SD}=\tau_{i,tag} - d_{i,ref}/c + (\delta_{tag} - \delta_{ref}) \cdot t_i + (\Delta T_{tag} - \Delta T_{ref}) + (\psi_{tag} - \psi_{ref})$

相位相减：

$\phi_{i,tag} - \phi_{i,ref} = [-2\pi f_c (\tau_{i,tag} - \tau_{i,ref}) - 2\pi f_c (\delta_{tag} - \delta_{ref}) \cdot t_i + (\theta_{i,tag} - \theta_{i,ref})]\mod 2\pi$

$\phi _i^{SD}$：一次差分后的$PDoA$

$\phi _i^{SD}=[-2\pi f_c \tau_{i,tag} - 2\pi f_c (\delta_{tag} - \delta_{ref}) \cdot t_i + (\theta_{i,tag} - \theta_{i,ref})] \mod 2\pi$

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image%203.png)

更换不同的ref anchor计算single difference，ref anchor和tag之间的误差是一致的

因此引入新的$j_{th}$  anchor 作为reference计算二次差分**Double Difference**，消除ref anchor和tag之间的误差、消除初始时间偏移$\Delta T$和天线偏移$\psi$

只剩下有用的**距离差**项 $\tau_{i,tag} - \tau_{j,tag}$和两个anchor之间的**时钟偏移误差项$(\delta_{tag} - \delta_{ref}) (t_i - t_j)$** 

ToF二次差分：

$r_{i,j}^{DD} = (r_{i,tag} - r_{i,ref}) - (r_{j,tag} - r_{j,ref})$ 

$r_{i,j}^{DD}= (\tau_{i,tag} - \tau_{j,tag}) + (\delta_{tag} - \delta_{ref}) (t_i - t_j)$

Phase二次差分（$PDoA_i - PDoA_j$）：

$\phi_{i,j}^{DD} = (\phi_{i,tag} - \phi_{i,ref}) - (\phi_{j,tag} - \phi_{j,ref})$

$\phi_{i,j}^{DD}= [-2\pi f_c (\tau_{i,tag} - \tau_{j,tag}) - 2\pi f_c (\delta_{tag} - \delta_{ref}) (t_i - t_j) ] \mod 2\pi$

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image%204.png)

**残差消除：消除与$(\delta_{tag} - \delta_{ref}) (t_i - t_j)$ 有关的项**

同一个ref anchor发送的两个相邻时刻$t$和$t’$$(t’-t=\Delta t)$的数据包，相位差为$\Delta \phi$ 

$\Delta \phi =[-2\pi f_c (\tau_{ref,tag}-\tau_{ref,tag}')+2\pi f_c(\delta_{tag}-\delta_{ref})\Delta t]\mod 2\pi$

$\Delta \phi \approx [2\pi f_c(\delta_{tag} - \delta_{ref}) \Delta t)]\mod 2\pi$

$\delta_{tag} - \delta_{ref} = (\frac{\Delta \phi}{2\pi}+l)\frac{1}{f_c \Delta t}$

$l$ 是整数，代表在$(\delta_{tag} - \delta_{ref})$中包含的$\frac{1}{f_c \Delta t}$个数。UWB可以提供粗粒度的时钟偏移值，因此 $l$ 是可以估计的：

$l = \left\lfloor \frac{\widetilde{\delta_{tag}}-\widetilde{\delta_{ref}}}{\frac{1}{f_c \Delta t}} \right\rfloor=\left \lfloor (\widetilde{\delta_{tag}}-\widetilde{\delta_{ref}})f_c \Delta t\right\rfloor$

用估计的 $l$ 值计算时钟偏移，结果为0.005 ppm，乘发射时间间隔，得到残差的准确数值

剩下的时变的ToF和Phase为：

$r_{i,j}^{rec}= \tau_{i,tag} - \tau_{j,tag}$

$\phi_{i,j}^{rec}= [-2\pi f_c (\tau_{i,tag} - \tau_{j,tag}) ] \mod 2\pi$

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image%205.png)

---

消除初始偏移、天线偏移、CFO的思路与测距论文使用的思路相似

#### **tag定位**

仅使用ToF计算距离分辨率为10cm

使用相位计算$i_{th}$ 和 $j_{th}$ anchor 的距离：

$D_{i,j}^{phase}= c \cdot (\tau_{i,tag} - \tau_{j,tag})=\frac{c\phi_{i,j}^{rec}}{2\pi f_c}+N_{i,j}\cdot \frac{c}{f_c}=(\frac{\phi_{i,j}^{rec}}{2\pi}+N_{i,j})\lambda$

$N_{i,j}$ 是相位模糊数，表示$D_{i,j}^{phase}$ 中有多少个波长；当$\phi_{i,j}^{rec}=0.3rad$  时距离分辨率最小为：

$D_{i,j}^{phase}= \frac{0.3rad}{2\pi} \cdot \frac{3\times 10^8 m/s}{3.5 \times 10^9 Hz}=0.41mm$

小于仅使用ToF的情况，因此使用相位计算的距离作为三角测距的输入

**解决相位模糊：**使用ToF计算出的距离校正*，*$N_{i,j}=\left\lfloor \frac{D_{i,j}^{ToF}}{\lambda} \right\rfloor$

当$D_{i,j}^{ToF}$ 中的误差大于$\frac{\lambda}{2}$ 时会round出错，导致估计的$N_{i,j}$ 跑到相邻区间

分析$D_{i,j}^{ToF}$ 误差的来源：

随机噪声、tag位置偏好

**降低随机噪声**：$D_{i,j}^{phase}$ 的相对改变量是unambiguous的，可提供毫米级精度的tag位移信息

本质是利用相邻时间帧之间相位的连续性来抑制随机噪声。计算$k_{th}$ 和 $(k+1)_{th}$ 次测距的距离差：

$\Delta D_{i,j}^{phase}(k)=[\phi_{i,j}^{rec}(k+1)-\phi_{i,j}^{rec}(k)]\frac{\lambda}{2\pi}$

为了避免相位跳变，两次测距之间tag的移动距离不能大于半波长，两次定位的时间间隔为$\Delta t$ 时，tag的移动速度在$\frac{\lambda}{2\Delta t}$ 以内时关于$\Delta D_{i,j}^{phase}(k)$的公式有效

$\Delta t$ = 2.5ms 时，tag的最大移动速度为17.14 m/s，用这个数值计算出$\Delta D_{i,j}^{phase}$

得到准确的$\Delta D_{i,j}^{phase}$后，与$D_{i,j}^{ToF}$ 融合：

$D_{i,j}^{filt} (k+1)=\frac{1}{F}D_{i,j}^{ToF} (k+1)+\frac{F-1}{F}[D_{i,j}^{filt}(k)+\Delta D_{i,j}^{phase}(k)  ]$

$F$ 是自定义的整数

这样不断消除偏移是为了保证$D_{i,j}^{ToF}$ 和 $D_{i,j}^{phase}$ 在 $k$ 和$(k+1)$ 次测距之间是一致的

比较只使用phase的测距结果和融合之后的测距结果，融合之后的距离误差更小：

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image%206.png)

**解决tag位置偏好**：跳频，使用不同频率的UWB来模拟一个扩大的模糊周期

![image.png](MULoc%201ec2b254f70080b3ada5d44c4eb4dfea/image%207.png)

在两个不同频率上测得相位：$\phi_{i,j}^{rec,1}$ 和 $\phi_{i,j}^{rec,2}$，对应的波长：$\lambda_1=\frac{c}{f_1}$，$\lambda_2=\frac{c}{f_2}$

合成的虚拟波长：$\lambda_v = \frac{c}{f_2-f_1}$

使用合成的波长计算距离：$D_{i,j}^{phase}=(\frac{\phi_{i,j}^{rec,1}-\phi_{i,j}^{rec,2}}{2\pi}+N_{i,j}^v)\lambda_v$

