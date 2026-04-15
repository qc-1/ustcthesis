# 人形机器人遥操作与通用全身运动控制研究

## 摘要

**摘要**

人形机器人作为具身智能的终极载体，在复杂非结构化环境中具有重要应用前景。针对其全身运动控制面临的高维度、强耦合、欠驱动难题，本文提出"人在回路"的研究范式，构建了从遥操作数据采集到自主任务执行的完整技术链路。主要贡献包括：（1）建立了多源异构运动数据采集体系，涵盖Noitom惯性动捕、Nokov光学动捕及基于SONIC框架的仿真交互数据，实现了到Unitree G1及全尺寸机器人的GMR重定向；（2）提出了上下肢解耦的通用全身控制框架，下肢采用强化学习训练了前进、后退、旋转、左右平移四种Locomotion模式，上肢建立遥操作数据接口实现高保真跟踪；（3）开发了感知-控制闭环系统，在CyboRacket动态球拍任务中验证了高速视觉-运动耦合控制，在OpenClaw语音服务任务中实现了"拿瓶水"等指令的端到端自主执行。实验结果表明，所提方法可有效支撑人形机器人的全身运动控制与自主作业。

**关键词**：人形机器人；全身运动控制；遥操作；强化学习；感知-控制闭环

**Abstract**

Humanoid robots, as the ultimate embodiment of embodied intelligence, hold significant promise for complex unstructured environments. To address the challenges of high dimensionality, strong coupling, and underactuation in whole-body control, this thesis proposes a "human-in-the-loop" paradigm, establishing a complete technical pipeline from teleoperation data collection to autonomous task execution. The main contributions include: (1) a multi-source heterogeneous motion capture system covering Noitom inertial mocap, Nokov optical mocap, and SONIC-based simulation data, with GMR retargeting to Unitree G1 and full-scale robots; (2) a decoupled whole-body control framework where the lower body employs RL-trained locomotion policies (forward/backward/rotation/lateral) and the upper body maintains high-fidelity teleoperation interfaces; (3) a perception-control closed-loop system validated on CyboRacket dynamic racket tasks and OpenClaw voice-commanded service tasks. Experimental results demonstrate the effectiveness of the proposed methods for humanoid whole-body control.

**Key Words**: Humanoid Robot; Whole-Body Control; Teleoperation; Reinforcement Learning; Perception-Action Loop

---



---

## 第1章 绪论

### 1.1 研究背景与意义
- 人形机器人的战略价值与应用场景（家庭服务、灾难救援、太空探索）
- 全身运动控制的"圣杯"挑战：高维度、强耦合、欠驱动、动力学复杂性
- "人在回路"范式的必要性：遥操作采集→数据驱动控制→自主执行的技术路线

### 1.2 国内外研究现状
**2.2.1 遥操作与数据采集**
- 现有工作：OmniH2O [1], TWIST [2], HITBOT [3]
- 局限：单一数据源、重定向精度不足
- 本文工作：多源融合（惯性/光学/仿真）+ GMR重定向

**2.2.2 通用全身运动控制**
- 现有工作：SONIC [4], BeyondMimic [5], HIM [6]
- 局限：端到端训练成本高、 Sim-to-Real迁移困难
- 本文工作：上下肢解耦架构，兼顾灵活性与稳定性

**2.2.3 感知-控制闭环与自主任务**
- 现有工作：π0 [7], OpenClaw [8], Being-0 [9], **CyboRacket [10], Cybo-Waiter [11]**
- 本文作者贡献：参与发表了CyboRacket（负责全身控制接口）和Cybo-Waiter（负责Locomotion执行层）的相关工作

### 1.3 本文主要研究内容
- 一句话概括：构建"数据采集-控制策略-任务执行"的完整框架
- 技术路线：多源遥操作采集 → 解耦全身控制 → 感知闭环自主任务

### 1.4 论文组织结构
- 第2章：多源遥操作数据采集与重定向
- 第3章：解耦式通用全身运动控制策略  
- 第4章：感知-控制闭环的自主任务执行
- 第5章：结论与展望

---

## 第2章 多源遥操作数据采集与重定向

**本章定位**：解决"数据从哪里来、如何映射到机器人"的问题

### 2.1 系统架构概述
- 整体流程：人体运动捕捉 → GMR重定向 → 机器人可执行数据
- 架构图：展示Noitom/Nokov/SONIC → GMR → Unitree G1/全尺寸机器人的数据流

### 2.2 多源人体运动捕捉系统
**2.2.1 惯性动捕系统（Noitom Perception Neuron）**
- 适用场景：快速部署、低成本日常采集
- 数据格式：全身关节角序列（采集频率、精度指标）

**2.2.2 光学动捕系统（Nokov）**
- 适用场景：高精度科研数据采集  
- 数据格式：全身6DoF位姿轨迹（与惯性动捕的互补性）

**2.2.3 基于仿真的人机交互数据采集（SONIC框架）**
- 采集内容：Loco-manipulation数据（移动+操作复合动作）
- 数据规模：X小时交互数据，涵盖Y种物体类型
- 数据用途：训练第3章的机器人-物体交互策略

### 2.3 基于GMR的运动重定向
**2.3.1 人机形态差异建模**
- 骨骼长度差异处理
- 关节自由度映射：人体 → Unitree G1（23自由度）→ 全尺寸机器人（需注明型号）

**2.3.2 GMR重定向算法**
- 高斯混合回归的概率运动模型
- 关节限幅与自碰撞约束优化

**2.3.3 重定向精度评估**
- 末端执行器轨迹误差（RMSE）
- 关节空间角度误差
- 时序一致性指标

---

## 第3章 解耦式通用全身运动控制策略

**本章定位**：解决"如何控制机器人运动"的问题（只讲控制，不讲遥操作硬件原理）

### 3.1 上下肢解耦控制架构设计
- **设计动机**：上肢重操作精度、下肢重平衡移动，降低学习复杂度
- **系统架构图**：

- **接口定义**：上下肢信息交换机制（姿态同步、重心补偿）

### 3.2 下肢强化学习Locomotion策略
**3.2.1 策略训练环境配置**
- Isaac Gym/Isaac Lab仿真设置
- 域随机化参数（摩擦系数、质量分布、地形高度）

**3.2.2 状态空间与奖励设计**
- 观测空间：IMU姿态、关节角/速度、历史速度指令、足端力反馈
- 动作空间：12个下肢关节位置目标
- 奖励函数：速度跟踪项 + 平衡稳定项 + 能量效率项

**3.2.3 网络结构与训练**
- PPO算法配置（学习率、批次大小）
- 策略网络架构（MLP层数、隐藏层维度）

**3.2.4 策略性能评估**
| 运动模式 | 速度范围 | 成功率 | 鲁棒性测试 |
|:---|:---|:---|:---|
| 前进 | X m/s | XX% | 抗扰动测试 |
| 后退 | X m/s | XX% | 斜坡地形 |
| 原地旋转 | X rad/s | XX% | 旋转稳定性 |
| 左右平移 | X m/s | XX% | 侧向稳定性 |

### 3.3 上肢遥操作接口与全身协调
**3.3.1 遥操作数据接口（接收层）**
- 接收第2章GMR重定向后的上肢轨迹数据
- 数据平滑与插值处理（低通滤波、贝塞尔曲线拟合）

**3.3.2 逆运动学求解与跟踪**
- 解析IK与数值IK混合求解
- 关节限幅处理与奇异点规避

**3.3.3 全身协调机制**
- 上下肢运动冲突检测与优先级处理
- 动态重心补偿策略（上肢大范围动作时的下肢姿态调整）

### 3.4 Sim-to-Real迁移与真机验证
- 动力学参数辨识与适配方法
- 真机实验：4种Locomotion模式切换演示
- 性能曲线：速度跟踪误差、关节力矩分布、能耗指标

---

## 第4章 感知-控制闭环的自主任务执行

**本章定位**：展示第2、3章方法在具体任务中的应用（两个项目，清晰区分个人贡献）

### 4.1 感知-控制闭环架构概述
- **通用框架图**：
- **接口标准化**：规划器输出 → 第3章定义的上下肢控制接口

### 4.2 动态球拍运动控制（CyboRacket）
**4.2.1 任务背景与系统架构**
- 任务特点：高速球体（>10m/s）、实时拦截（<500ms反应时间）、全身协调
- **本文作者贡献声明**：负责全身控制接口与真机部署（将规划器输出转换为第3章的上下肢指令）
- 系统架构图（引用Fig.1，说明控制流）

**4.2.2 视觉感知与轨迹预测接口**
- 简要说明YOLO检测与物理轨迹预测（与本文控制方法的关系）
- **重点**：预测轨迹 → 下肢导航指令（调用第3章RL策略）
- **重点**：击球点 → 上肢末端目标（调用第3章遥操作接口的自动化版本）

**4.2.3 全身运动生成与实验验证**
- 拦截策略：下肢移动到击球区域 + 上肢挥拍动作协调
- 仿真验证（MuJoCo）：击球成功率、拦截位置误差
- 真机实验（Unitree G1）：连续击球演示、纯 onboard 视觉闭环

### 4.3 语音指令服务任务执行（OpenClaw + SAM3集成）
**4.3.1 系统架构与任务流程**
- 语音输入（飞书API）→ OpenClaw任务规划 → SAM3视觉感知 → 第3章全身控制
- 典型任务："给我拿瓶水"分解为[导航→检测→抓取→递送]

**4.3.2 任务规划与视觉感知**
- OpenClaw将高层指令分解为结构化子任务
- SAM3多目标分割与3D定位（物体+容器位姿估计）

**4.3.3 全身控制集成与执行**
- **导航阶段**：目标物体方位 → 第3章下肢RL策略速度指令（前进/旋转组合）
- **操作阶段**：目标物体位姿 → 第3章上肢IK接口（抓取轨迹生成）
- **全身协调**：移动中的抓取姿态稳定（第3章3.3.3节机制的应用）

**4.3.4 实验验证**
- 实验环境：室内办公场景， cluttered桌面
- 任务成功率、平均执行时间、失败案例分析（感知失败 vs 控制失败）

---

## 第5章 结论与展望

### 5.1 本文工作总结
- **数据采集层**：构建了Noitom/Nokov/SONIC多源采集体系，实现了到G1及全尺寸机器人的高精度GMR重定向
- **控制策略层**：提出了上下肢解耦的通用全身控制框架，下肢RL策略支持4种Locomotion模式，上肢实现高保真跟踪
- **应用验证层**：在CyboRacket动态任务和OpenClaw服务任务中验证了感知-控制闭环的有效性

### 5.2 主要创新点
1. 多源异构人体运动数据的统一重定向框架（惯性/光学/仿真）
2. 上下肢解耦的高效全身控制架构（遥操作+RL混合）
3. 从遥操作数据采集到自主感知控制的完整技术链路验证

### 5.3 未来工作展望
- **端到端全身学习**：当前解耦架构的端到端优化（VLA模型）
- **复杂长时任务**：多房间导航、多物体操作序列规划
- **全身VLA大模型**：将视觉-语言模型与全身控制策略深度融合

---



---

## 附录

### 附录A 硬件平台参数
- Unitree G1机器人规格（自由度、传感器配置）
- PICO VR设备参数
- RealSense D455相机标定参数

### 附录B 强化学习训练超参数
- PPO算法详细配置
- 网络结构图