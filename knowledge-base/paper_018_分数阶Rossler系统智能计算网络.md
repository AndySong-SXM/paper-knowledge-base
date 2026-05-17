# 论文精读笔记 018：非线性混沌分数Rossler系统的智能计算网络设计

---

## 一、基本信息

| 项目 | 内容 |
|------|------|
| **论文标题** | Design of intelligent computing networks for nonlinear chaotic fractional Rossler system |
| **中文标题** | 非线性混沌分数Rossler系统的智能计算网络设计 |
| **作者** | Ayaz Hussain Bukhari, Muhammad Asif Zahoor Raja, Naila Rafiq, Muhammad Shoaib, Adiqa Kausar Kiani, Chi-Min Shu |
| **机构** | COMSATS University Islamabad (巴基斯坦); National Yunlin University of Science and Technology (台湾) |
| **期刊** | Chaos, Solitons and Fractals |
| **年份** | 2022 |
| **卷期** | Vol. 157, 111985 |
| **DOI** | https://doi.org/10.1016/j.chaos.2022.111985 |
| **关键词** | Chaotic systems, Artificial intelligence, Lyapunov exponent, Average mutual information, Radial basis neural networks, Fractional differential equations, Rossler attractor |
| **研究方向** | 分数阶混沌系统 · 神经网络求解器 · 相空间重构 |

---

## 二、研究背景与问题

### 2.1 研究背景

分数阶混沌系统因其复杂性和功率谱特性，在以下领域具有重要应用：
- **密码学与信息安全**：混沌信号的随机性和敏感性适合加密应用
- **控制系统**：分数阶导数提供更丰富的动力学行为
- **生物医学**：心律、呼吸等节律性过程的混沌建模
- **物理科学**：非平衡和复杂现象研究

### 2.2 核心问题

**传统方法的局限性**：
1. **解析解困难**：分数阶非线性混沌系统的解析解难以获得
2. **频域近似缺陷**：频域近似方法可能掩盖混沌行为
3. **神经网络收敛慢**：传统神经网络对混沌信号的拟合收敛速度慢

### 2.3 研究目标

1. 计算分数阶Rossler微分方程系统的混沌轨迹
2. 分析非线性时间序列动力学并重构相空间
3. 开发变换函数提升神经网络收敛性能
4. 基于RBFN构建计算智能系统

---

## 三、核心方法与创新点

### 3.1 分数阶Rossler系统

**整数阶Rossler系统**：
$$
\frac{dx}{dt} = -(y + z), \quad \frac{dy}{dt} = x + ay, \quad \frac{dz}{dt} = b + xz - cz
$$

**分数阶形式**：
$$
D^\alpha x(t) = -(y + z), \quad D^\alpha y(t) = x + ay, \quad D^\alpha z(t) = b + xz - cz
$$

其中 $D^\alpha$ 表示分数阶导数算子，$\alpha \in (0, 1]$。

### 3.2 分数阶导数定义

论文系统介绍了四种分数阶导数定义：

| 定义 | 表达式 | 特点 |
|------|--------|------|
| **Grünwald-Letnikov** | $D^\alpha f(t) = \lim_{h \to 0} \frac{1}{h^\alpha} \sum_{j=0}^{\infty} (-1)^j \binom{\alpha}{j} f(t-jh)$ | 离散化计算方便 |
| **Caputo** | $^cD^\alpha f(t) = \frac{1}{\Gamma(n-\alpha)} \int_0^t \frac{f^{(n)}(\xi)}{(t-\xi)^{\alpha+1-n}} d\xi$ | 初始条件物理意义明确 |
| **Atangana-Baleanu** | $^a_0 D^\alpha f(x) = \frac{B(\alpha)}{1-\alpha} \int_0^x f'(s) E_\alpha \left[ -\frac{\alpha}{1-\alpha}(x-s)^\alpha \right] ds$ | 非奇异核，记忆效应强 |
| **Riemann-Liouville** | $D^\alpha f(t) = \frac{1}{\Gamma(n-\alpha)} \frac{d^n}{dt^n} \int_0^t (t-\xi)^{n-\alpha-1} f(\xi) d\xi$ | 经典定义，理论分析常用 |

### 3.3 分数阶Runge-Kutta方法 (FRKM)

**核心算法**：
$$
y_{n+1} = y_n + \frac{h^\alpha}{6\Gamma(\alpha+1)} (K_1 + 2K_2 + 2K_3 + K_4)
$$

其中 $K_1, K_2, K_3, K_4$ 为各阶段斜率函数，$h$ 为步长，$\alpha$ 为分数阶次。

### 3.4 相空间重构

**时间延迟嵌入法**：
$$
X(t_i) = [x(t_i), x(t_i+\tau), x(t_i+2\tau), ..., x(t_i+(m-1)\tau)]^T
$$

**平均互信息(AMI)算法**：用于确定最优时间延迟 $\tau$ 和嵌入维度 $m$。

### 3.5 径向基函数神经网络 (RBFN)

**网络结构**：三层网络（输入层 → 隐藏层 → 输出层）

**高斯RBF激活函数**：
$$
h_j(t) = \exp\left( -\frac{\|x(t) - c_j(t)\|^2}{2\sigma_j^2} \right)
$$

**输出层**：
$$
y(t) = \sum_{j=1}^{m} w_j h_j(t)
$$

### 3.6 核心创新：变换函数

**问题**：混沌信号的非高斯双峰分布导致神经网络收敛缓慢

**解决方案**：引入变换函数将混沌信号转化为有界信号

| 系统行为 | 变换函数 | 概率分布效果 |
|----------|----------|--------------|
| 高度随机非线性混沌 | $T = t^4 + t^{-4}$ | 双峰高峰度 |
| | $T = t^4 - t^{-4}$ | |
| | $T = t^3 + t^{-3}$ | |
| | $T = t^3 - t^{-3}$ | |
| | $T = t^{-3}$ | |
| | $T = t^{-4}$ | |

**效果**：显著加速RBFN收敛，实现快速全局优化。

---

## 四、实验设计与结果

### 4.1 实验案例设置

**Case 1**：$\alpha = 0.80, a = 0.4, b = 0.4, c = 6$
$$
D^{0.8}x = -(y+z), \quad D^{0.8}y = x + 0.4y, \quad D^{0.8}z = 0.4 + z(x-6)
$$

**Case 2**：$\alpha = 0.60, a = 0.1, b = 0.1, c = 18$
$$
D^{0.6}x = -(y+z), \quad D^{0.6}y = x + 0.1y, \quad D^{0.6}z = 0.1 + z(x-18)
$$

### 4.2 振荡域分析

| 案例 | 变量 | 最小值 | 最大值 |
|------|------|--------|--------|
| Case 1 | x | -11.78 | 14.57 |
| | y | -17.00 | 22.72 |
| | z | 0.20 | 55.82 |
| Case 2 | x | -214.68 | 27.77 |
| | y | -268.19 | 22.72 |
| | z | 8.01 | 55.82 |

### 4.3 Lyapunov指数计算

| 案例 | 变量 | 时间延迟 | 嵌入维度 | 最大Lyapunov指数 |
|------|------|----------|----------|------------------|
| Case 1 | x | 7 | 3 | 2.06 |
| | y | 9 | 3 | 2.10 |
| | z | 8 | 5 | 2.09 |
| Case 2 | x | 8 | 3 | 2.10 |
| | y | 8 | 3 | 2.08 |
| | z | 6 | 5 | 2.61 |

**结论**：所有Lyapunov指数均为正值，验证了系统的混沌特性。

### 4.4 RBFN训练性能

| 案例 | 变量 | 神经元数 | MSE | 训练轮次 |
|------|------|----------|-----|----------|
| Case 1 | x | 2000 | 4.04×10⁻³¹ | 1000 |
| | y | 2000 | 1.74×10⁻³¹ | 2000 |
| | z | 2000 | 1.72×10⁻³¹ | 200 |
| Case 2 | x | 2000 | 1.06×10⁻⁵ | 900 |
| | y | 2000 | 5.23×10⁻¹⁰ | 1800 |
| | z | 2000 | 1.40×10⁻⁵ | 1000 |

**关键发现**：
- Case 1的MSE达到10⁻³¹量级，精度达到10-15位小数
- 变换函数显著加速收敛（对比图16-17）
- RBFN成功捕捉分数阶混沌系统的非线性动力学

---

## 五、主要结论

### 5.1 方法论贡献

1. **FRKM算法有效性**：分数阶Runge-Kutta方法成功求解非线性分数阶Rossler系统
2. **相空间重构**：AMI算法有效确定时间延迟和嵌入维度，重构相空间揭示混沌结构
3. **变换函数创新**：提出的变换函数显著提升神经网络收敛速度
4. **高精度建模**：RBFN模型达到10⁻³¹量级的均方误差

### 5.2 应用价值

- 分数阶混沌系统建模与预测
- 安全通信中的混沌同步控制
- 非线性动力系统参数识别
- 生物医学信号处理

### 5.3 局限性

- RBFN收敛依赖于输入空间的适当变换
- 需要针对不同混沌模式选择合适的变换函数
- 计算复杂度随神经元数量增加

---

## 六、批判性评价

### 6.1 优点

| 方面 | 评价 |
|------|------|
| **方法创新** | 首次系统地将变换函数与RBFN结合用于分数阶混沌系统求解 |
| **精度验证** | 10⁻³¹量级的MSE展示了极高的数值精度 |
| **理论完整** | 涵盖四种分数阶导数定义，理论基础扎实 |
| **实验充分** | 两组案例验证方法的普适性 |
| **可视化丰富** | 相空间图、误差曲线、概率分布图等完整呈现 |

### 6.2 不足

| 方面 | 问题 |
|------|------|
| **硬件实现** | 未涉及DSP/FPGA等硬件验证 |
| **应用演示** | 缺少图像加密等实际应用场景 |
| **对比实验** | 未与其他神经网络（LSTM、BP等）进行对比 |
| **参数敏感性** | 未分析分数阶次α对收敛性能的影响规律 |

### 6.3 与本知识库论文的关联

**与002号论文（分数阶多翼混沌系统）对比**：
- 相同点：均研究分数阶混沌系统，使用Caputo定义
- 不同点：本文侧重数值求解方法，002侧重多稳定性分析

**与013号论文（ADE参数识别）对比**：
- 互补性：本文提供高精度求解器，013提供参数识别方法
- 潜在结合：RBFN求解器可作为ADE算法的正向模型

---

## 七、论文间关联与对比

### 7.1 研究脉络定位

```
分数阶混沌系统研究脉络
├── 分数阶导数理论
│   ├── R-L定义 (经典)
│   ├── Caputo定义 (物理意义明确) ← 本文使用
│   ├── CF定义 (002论文)
│   └── A-B定义 (非奇异核) ← 本文介绍
│
├── 数值求解方法
│   ├── Adomian分解法 (002, 009论文)
│   ├── 分数阶Runge-Kutta ← 本文核心方法
│   └── 频域近似法 (本文指出其缺陷)
│
├── 智能计算方法
│   ├── 差分进化算法 (013论文)
│   ├── RBFN神经网络 ← 本文核心方法
│   └── 深度学习 (005论文)
│
└── 应用领域
    ├── 图像加密 (004, 008, 010论文)
    ├── 参数识别 (013论文)
    └── 系统建模 ← 本文应用方向
```

### 7.2 方法对比表

| 论文 | 系统类型 | 求解方法 | 精度指标 | 应用场景 |
|------|----------|----------|----------|----------|
| **本文** | 分数阶Rossler | FRKM + RBFN | MSE ~ 10⁻³¹ | 系统建模 |
| 002 | 分数阶多翼 | Adomian分解 | IE ~ 7.999 | 图像加密 |
| 009 | 分数阶忆阻HNN | Adomian分解 | IE = 7.9995 | 图像加密 |
| 013 | 离散忆阻映射 | ADE算法 | 相对误差 < 0.01% | 参数识别 |

---

## 八、关键公式速查

### 8.1 分数阶Rossler系统

$$
\begin{cases}
D^\alpha x(t) = -(y + z) \\
D^\alpha y(t) = x + ay \\
D^\alpha z(t) = b + xz - cz
\end{cases}
$$

### 8.2 分数阶Runge-Kutta

$$
y_{n+1} = y_n + \frac{h^\alpha}{6\Gamma(\alpha+1)} (K_1 + 2K_2 + 2K_3 + K_4)
$$

### 8.3 RBFN输出

$$
y(t) = \sum_{j=1}^{m} w_j \exp\left( -\frac{\|x(t) - c_j(t)\|^2}{2\sigma_j^2} \right)
$$

### 8.4 相空间重构

$$
X(t_i) = [x(t_i), x(t_i+\tau), x(t_i+2\tau), ..., x(t_i+(m-1)\tau)]^T
$$

### 8.5 Lyapunov指数

$$
\lambda = \lim_{t \to \infty} \frac{1}{t} \ln \frac{\|\delta X(t)\|}{\|\delta X(0)\|}
$$

### 8.6 RMSE指标

$$
RMSE = \sqrt{\frac{1}{N} \sum_{t=1}^{N} (y_t - \hat{y}_t)^2}
$$

### 8.7 变换函数（核心创新）

$$
T = t^4 + t^{-4}, \quad T = t^3 - t^{-3}, \quad T = t^{-4}
$$

---

## 九、参考文献

1. Bukhari A H, Raja M A Z, Rafiq N, et al. Design of intelligent computing networks for nonlinear chaotic fractional Rossler system[J]. Chaos, Solitons and Fractals, 2022, 157: 111985.
2. Grünwald A K. Über "begrenzte" Derivationen und deren Anwendung[J]. Z. Angew. Math. Phys., 1867.
3. Caputo M. Linear models of dissipation whose Q is almost frequency independent-II[J]. Geophys. J. R. Astron. Soc., 1967.
4. Atangana A, Baleanu D. New fractional derivatives with nonlocal and non-singular kernel[J]. Therm. Sci., 2016.

---

*笔记创建时间：2026-05-17*
*论文编号：018*
*研究方向：分数阶混沌系统 · 神经网络求解器*
