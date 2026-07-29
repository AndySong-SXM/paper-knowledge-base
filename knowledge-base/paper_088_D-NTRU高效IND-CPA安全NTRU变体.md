# Paper 088: D-NTRU — 更高效且满足平均情况IND-CPA安全的NTRU变体

> **论文标题**: D-NTRU: More efficient and average-case IND-CPA secure NTRU variant
> **精读日期**: 2026-07-29
> **标签**: #格密码 #NTRU #可证明安全 #IND-CPA #公钥加密 #抗量子密码

---

## 一、基本信息

| 项目 | 内容 |
|------|------|
| **论文标题** | D-NTRU: More efficient and average-case IND-CPA secure NTRU variant |
| **作者** | Baocang Wang (王保仓), Hao Lei (雷浩), Yupu Hu (胡予濮) |
| **第一作者单位** | 西安电子科技大学 综合业务网理论及关键技术国家重点实验室 |
| **第二作者单位** | 华为技术有限公司 盾构实验室(Shield Laboratory) |
| **期刊** | Information Sciences |
| **卷/页** | 438: 15–31 |
| **发表年份** | 2018 |
| **DOI** | https://doi.org/10.1016/j.ins.2018.01.037 |
| **收稿日期** | 2017-06-08 |
| **修订日期** | 2017-09-21 |
| **接收日期** | 2018-01-21 |
| **资助项目** | 国家重点研发计划(2017YFB0802000)、国家自然科学基金(61572390, U1736111等)、河南省科技创新人才计划(184100510012) |
| **论文类型** | 基础研究 — 格基密码学方案构造与安全性证明 |

---

## 二、研究背景与问题

### 2.1 研究背景

公钥密码学（PKC）是网络与信息安全工程中最核心的密码工具。传统的RSA和ECC因模幂运算等昂贵操作，在加解密时仅达到立方复杂度，难以适用于资源受限环境（RFID、无线传感器网络、Ad Hoc网络等）。

**NTRU密码体制**由Hoffstein、Pipher和Silverman于1998年提出，其加解密速度远优于RSA和ECC，且基于理想格（ideal lattice）代数结构，天然具备抗量子计算攻击的潜力。然而，原版NTRU存在两个核心缺陷：

1. **缺乏可证明安全目标**：原版NTRU仅有启发式安全论证，没有可证明安全（provable security），这在实践中是一个标准要求。
2. **高密文膨胀率（ciphertext expansion）**：NTRU的密文膨胀率为 $\log_p q : 1$。在参数 $(p, q) = (3, 256)$ 下为 $5.047:1$，$(3, 512)$ 下为 $5.678:1$，远超RSA的 $1:1$ 和ECC的 $2:1$。

### 2.2 已有改进方案及其局限

此前已有三种可证明安全的NTRU改进方案：

| 方案 | 安全目标 | 安全模型 | 安全假设 | 主要缺陷 |
|------|---------|---------|---------|---------|
| **NAEP** | IND-CCA | 随机预言机(RO) | 平均情况NTRU单向性 | 引入额外计算，降低明文长度，效率严重折损 |
| **pNE** | IND-CPA | 标准模型 | 最坏情况理想格问题 | 公私钥随安全参数平方增长，作者承认"实际效率远不如原版" |
| **NTRUCCA** | IND-CCA | 标准模型 | 最坏情况理想格问题 | 比pNE更慢（需签名生成与验证），增大模数$q$ |

**核心矛盾**：现有可证明安全方案均以显著降低效率为代价，而原版NTRU高效却无安全性证明。

### 2.3 研究问题

> 是否可能构造一种改进的NTRU变体，使其既具备**可证明安全**（IND-CPA），又在**密文膨胀率**、**加密效率**、**解密效率**上全面优于原版NTRU？

---

## 三、核心方法与创新点

### 3.1 总体设计思想：双NTRU + 一次性填充（One-Time-Pad）架构

D-NTRU（Double NTRU）将加密设计为**两个并行NTRU加密**的组合：

$$c = (c_1, c_2) = \left( \langle r_1 \cdot h_1 + r_2 \rangle_{q_1}, \; \langle r_1 \cdot h_2 + r_2 + M \rangle_{q_2} \right)$$

其中：
- $c_1$ 承载**一次性密钥** $(r_1, r_2)$（通过NTRU机制安全传输）
- $c_2$ 承载**一次性填充加密**的密文（$r_1, r_2$ 作为共享密钥）
- $q_1, q_2$ 是**孪生素数**（$q_2 = q_1 + 2 \approx q$）

### 3.2 关键技术组件

#### 3.2.1 C-NTRU（Composite NTRU）— 安全性归约桥梁

C-NTRU使用复合模数 $q_1 q_2$（$q_1$为素数），是NTRU直接推广：

- **密钥生成**：分别计算 $h_1 = \langle p \cdot f_{q_1}^{-1} \cdot g \rangle_{q_1}$ 和随机 $h_2 \in R_{q_2}$，通过中国剩余定理合并为 $h \equiv h_1 \pmod{q_1}, h \equiv h_2 \pmod{q_2}$
- **加密**：$c = \langle h \cdot \varphi + m \rangle_{q_1 q_2}$
- **解密**：仅需 $h_1$ 部分（$s = \langle f \cdot c \rangle_{q_1}$，$m = \langle s \cdot f_p^{-1} \rangle_p$）
- **退化关系**：当 $q_2 = 1$ 时，C-NTRU $=$ NTRU

#### 3.2.2 关键代数事实（Fact 1）

为保证安全性证明成立，秘密多项式 $f, g$ 必须满足：

$$\| \langle f_{q_1}^{-1} \cdot g \rangle_{q_1} \|_{\infty} > 2, \quad \| \langle f \cdot g_{q_1}^{-1} \rangle_{q_1} \|_{\infty} > 2$$

即 $f$ 与 $g$ 相乘后模约简的系数不能太小，否则攻击者可逼近明文。

#### 3.2.3 无效密文理论（Invalid Ciphertext Theory，Theorem 3）

**创新发现**：对于合法密文 $c$，可通过检查 $\langle c \pm X^i \rangle$ 是否为有效密文来判定明文特定位置系数。例如，当 $r_2[i] = 1$ 时，$\langle c_1 + X^i \rangle_{q_1}$ 不可能是D-NTRU的有效密文。这一理论工具在安全性证明中至关重要。

### 3.3 安全性证明体系（5个等价归约定理）

论文定义了四个密码学问题并建立等价性链：

1. **NTRU-OW问题**：给定公钥 $h$ 和密文 $c$，找到原像 $(\varphi, m)$
2. **C-NTRU-OW问题**：C-NTRU对应的单向性问题
3. **C-NTRU密文分布问题**：区分均匀随机分布 $R_C$ 与C-NTRU密文分布 $\mathcal{C}$
4. **D-NTRU分布问题**：区分 $R_D$（随机）与 $\mathcal{D}$（D-NTRU密文）

**等价性归约链**（Theorem 4–7）：

$$\text{NTRU-OW} \equiv_p \text{C-NTRU-OW} \equiv_p \text{C-NTRU Distribution} \equiv_p \text{D-NTRU Distribution}$$

**最终安全定理（Theorem 8）**：若 $q_1 > \delta + 2$，则D-NTRU在标准模型下满足IND-CPA安全，归约到平均情况NTRU单向性假设。

**关键证明思想**：利用一次性填充结构，将IND-CPA安全性归约为D-NTRU分布区分问题，再通过C-NTRU桥梁等价于经典NTRU单向性问题。

### 3.4 解密失败条件

**Theorem 1** 给出无解密失败条件：

$$\delta = 2p \cdot \min \left\{ 2d_g - 1, 2d \right\} + 2d_f - 1$$

对于NTRU需 $q > \delta$，对D-NTRU需 $q_1 > \delta$（加密/解密失败不影响安全性证明，已有文献[36]给出低失败率参数建议）。

---

## 四、实验设计与结果

### 4.1 参数设置

论文给出了三组建议参数（Table 4）：

| 安全级别 $\kappa$ | NTRU $(N,q,d)$ | D-NTRU $(N,q_1,q_2,d)$ | $\rho_N/\rho_D$ | ES比 | DS比 |
|:---:|------|------|:---:|:---:|:---:|
| 120 | (157, 256, 52) | (157, 269, 271, 52) | 2.525 | 2.506 | 1.688 |
| 172 | (223, 256, 74) | (223, 269, 271, 74) | 2.525 | 2.506 | 1.688 |
| 270 | (349, 512, 116) | (349, 521, 523, 116) | 2.840 | 2.833 | 1.917 |

### 4.2 密文膨胀率分析

- D-NTRU密文膨胀率：$\rho_D \approx 1 + \frac{\log_{q_2} q_1}{1} : 1 \approx 1.999 : 1$（接近理论下界2:1）
- NTRU密文膨胀率：$\rho_N = \log_p q : 1 \approx 5.047 \sim 5.678 : 1$
- **D-NTRU比原版NTRU节省约2.5–2.8倍通信带宽**

### 4.3 加解密速度分析（渐近分析）

当 $N \to \infty$：

- 加密速度比：$\frac{ES_D}{ES_N} = \frac{(\log_2^2 q + \log_2 q)\log_2 q_2}{(\log_2^2 q_1 + \log_2 q_1 + \log_2^2 q_2 + \log_2 q_2)\log_2 p} \approx 2.5 \sim 2.8$
- 解密速度比：$\frac{DS_D}{DS_N} \approx 1.688 \sim 1.917$

### 4.4 软件实现结果

**实现环境**：Intel Pentium CPU @2.60GHz, 2GB RAM, Windows XP, VC++6.0 Debug版

| 指标 | $\kappa=120$ | $\kappa=172$ | $\kappa=270$ |
|------|:---:|:---:|:---:|
| NTRU加密时(ms) | 7.922 | 17.266 | 42.485 |
| D-NTRU加密时(ms) | 18.656 | 36.062 | 87.172 |
| NTRU加密速度(bit/ms) | 31.411 | 20.471 | 13.020 |
| D-NTRU加密速度(bit/ms) | 68.016 | 49.978 | 36.155 |
| **ES_D / ES_N** | **2.165** | **2.441** | **2.777** |
| NTRU解密时(ms) | 13.078 | 22.718 | 52.438 |
| D-NTRU解密时(ms) | 45.250 | 91.187 | 182.469 |
| NTRU解密速度(bit/ms) | 19.027 | 15.558 | 10.549 |
| D-NTRU解密速度(bit/ms) | 28.042 | 19.765 | 17.273 |
| **DS_D / DS_N** | **1.474** | **1.270** | **1.637** |

> **注意**：实际实现中，单次加密绝对耗时D-NTRU反而更长（因为需要计算两个模数下的乘法），但当以**单位时间内加密的明文比特数**衡量时，由于D-NTRU单次加密的明文量（$N\log_2 q_2$ bits）远超NTRU（$N\log_2 p$ bits），D-NTRU具有显著优势。这种优势在传输大文件（视频、图片等）时尤为明显；传输极短报文（如对称密钥）时优势不显著。

### 4.5 密钥尺寸

| 指标 | NTRU | D-NTRU | 比值 |
|------|------|--------|:---:|
| 公钥 | $N\log_2 q$ | $N(\log_2 q_1 + \log_2 q_2)$ | ≈2 |
| 私钥 | $2N\log_2 p$ | $2N\log_2 p + N\log_2 q_1$ | 3.5–3.8 |

> D-NTRU的公私钥尺寸均大于原版NTRU，这是获得可证明安全的代价。

---

## 五、主要结论

1. **D-NTRU首次在标准模型下实现IND-CPA安全**，归约到平均情况NTRU单向性问题，安全性假设弱于pNE/NTRUCCA（后者需最坏情况理想格假设）。

2. **密文膨胀率降低至约2:1**，比原版NTRU节省约60%通信带宽（原版约5:1），逼近公钥密码的理论最优密文膨胀率。

3. **以单位明文比特数的加解密效率衡量**，D-NTRU加密速度是原版NTRU的2.17–2.78倍（实测），解密速度是1.27–1.64倍。

4. **代价是公私钥尺寸增大**：公钥约为原版2倍，私钥约为3.5–3.8倍。这在资源受限环境中可能成为瓶颈。

5. **未达到最高安全目标IND-CCA**：可通过Fujisaki-Okamoto变换升级为IND-CCA，但会折损效率。

---

## 六、批判性评价

### 6.1 论文优点

| 维度 | 评价 |
|------|------|
| **创新性** | ★★★★☆ 首次提出"双NTRU+一次性填充"架构，C-NTRU作为安全性归约桥梁设计精巧 |
| **理论深度** | ★★★★★ 安全性证明体系极其严密：5个定义→4个定理→完整等价性链，包括无效密文理论的严格证明 |
| **实用性** | ★★★★☆ 密文膨胀率从5:1降至2:1，在大文件传输场景中带宽节省显著 |
| **可复现性** | ★★★★☆ 提供具体实现参数（3组），代码实现1000次循环取均值 |
| **完整性** | ★★★★★ 从记号定义、代数结构、参数设计、复杂度分析、软件实现到FFT加速讨论全覆盖 |

### 6.2 论文不足与局限

| 局限 | 详细说明 |
|------|---------|
| **安全性仅达IND-CPA** | 在多数实际场景需IND-CCA安全（如主动攻击敌手），需额外的Fujisaki-Okamoto变换 |
| **安全假设为平均情况** | 不如pNE/NTRUCCA的最坏情况格归约安全声称强，对量子安全性未做专门讨论 |
| **单次加密绝对耗时更长** | 以"传输大量数据"为前提的效率优势在短报文场景（如密钥协商）中消失 |
| **实现环境过时** | VC++6.0(1998年发布)、Windows XP、Pentium CPU，与现代基准（AVX2/NEON加速、现代编译器优化）差距大 |
| **缺少与LWE类方案对比** | 未与同期的FrodoKEM、NewHope等LWE基抗量子方案进行实验比较 |
| **FFT加速未实现** | 仅做了理论讨论，未用NTT优化（基于幂次$N$的NTRU变体），不利于直接与NIST PQC候选方案对比 |
| **密钥尺寸增大** | 私钥3.5–3.8倍增长在RFID/智能卡等极端资源受限设备中可能无法接受 |
| **解密失败概率分析不充分** | 仅引用[36]，未对Table 4参数集做具体失败概率数值计算 |

### 6.3 与NIST后量子密码标准化的关系

本文发表于2018年，正值NIST PQC第一轮评估期。论文**未将D-NTRU直接提交为NIST候选方案**，但其设计思想（双模数、一次性填充安全性归约）对后续格基KEM设计有一定启发。注意NTRU-HRSS（后改为Streamlined NTRU Prime的变体）最终进入NIST第四轮，说明NTRU类方案在抗量子密码领域仍有生命力。

### 6.4 在混沌密码学研究中的位置

本文属于**格密码/后量子密码**范畴而非混沌密码。然而，鉴于本知识库的研究方向涵盖密码学理论基础，Shannon的保密系统通信理论（062号）已建立密码学的奠基框架，理解格密码的前沿构造（特别是其可证明安全方法学）有助于：
- 理解密码学安全证明的标准方法（归约、Game-based证明、CPA/CCA区分模型）
- 将类似的安全分析框架移植到混沌密码方案的可证明安全性研究中
- 理解为什么混沌密码缺乏广泛接受的"最坏情况到平均情况"安全归约

---

## 七、论文间关联与对比

### 7.1 与本知识库中密码学基础论文的关联

| 关联论文 | 关系说明 |
|---------|---------|
| **062号 — Shannon保密系统通信理论(1949)** | Shannon定义了完美保密、扩散与混淆、一次一密等核心概念。D-NTRU的 $c_2 = \langle r_1 \cdot h_2 + r_2 + M \rangle_{q_2}$ 本质上是"一次一密"在环上的推广实现。 |
| **084号 — 伪随机量子态密码学(2022)** | 同为密码学可证明安全方向。Aanth等人颠覆性地展示了从伪随机量子态即可构建密码学（无需单向函数），而D-NTRU仍依赖传统单向性假设。 |
| **072号 — 格上可证明安全PAKE协议(2022)** | 同为格密码可证明安全，展示了格问题在认证协议中的应用，D-NTRU专注加解密。 |
| **077号 — R-LWE抗量子物联网安全认证(2022)** | 同为格基抗量子方案，但使用R-LWE而非NTRU假设，展示了格密码在物联网中的实用性（D-NTRU目标场景之一）。 |

### 7.2 后续发展脉络

```
NTRU (1998) — Hoffstein/Pipher/Silverman
  ├── 格攻击发现 (Coppersmith-Shamir, 1997)
  ├── NAEP (2003) — IND-CCA in ROM
  ├── pNE (2011/Stehlé-Steinfeld) — worst-case ideal lattice
  ├── NTRUCCA (2012/Steinfeld et al.) — IND-CCA, standard model
  ├── ★ D-NTRU (2018/Wang et al.) — 本文，IND-CPA, average-case
  └── NTRU-HRSS → Streamlined NTRU Prime (NIST PQC Round 4)
```

---

## 八、关键公式速查

### 8.1 NTRU核心操作

| 公式 | 含义 |
|------|------|
| $R = \mathbb{Z}[X] / (X^N - 1)$ | 截断多项式环 |
| $h = \langle p \cdot f_q^{-1} \cdot g \rangle_q$ | NTRU公钥生成 |
| $c = \langle h \cdot \varphi + m \rangle_q$ | NTRU加密 |
| $s = \langle f \cdot c \rangle_q, \; m = \langle s \cdot f_p^{-1} \rangle_p$ | NTRU解密 |

### 8.2 D-NTRU方案

| 公式 | 含义 |
|------|------|
| $\delta = 2p \cdot \min\{2d_g-1, 2d\} + 2d_f - 1$ | 解密失败上界 |
| $q_1 > \delta + 2$ | D-NTRU无解密失败 + 安全性证明条件 |
| $h_1 = \langle p^{-1} \cdot g_{q_1}^{-1} \rangle_{q_1}$ | D-NTRU公钥第一部分 |
| $G = \langle f \cdot g_{q_1}^{-1} \rangle_{q_1}$ | D-NTRU私钥辅助分量 |
| $c = (c_1, c_2) = (\langle r_1 \cdot h_1 + r_2 \rangle_{q_1}, \langle r_1 \cdot h_2 + r_2 + M \rangle_{q_2})$ | D-NTRU加密 |
| $\rho_D = 1 + \log_{q_2} q_1 : 1 \approx 2:1$ | D-NTRU密文膨胀率 |

### 8.3 范数运算引理

| 引理 | 内容 |
|------|------|
| Lemma 1 | $\|a + b\|_{\infty} \leq \|a\|_{\infty} + \|b\|_{\infty}$ |
| Lemma 2 | $\|a \cdot b\|_{\infty} = |a| \cdot \|b\|_{\infty}$（$a \in \mathbb{Z}$） |
| Lemma 3 | $\|a \cdot b\|_{\infty} \leq \min\{\|a\|_1, \|b\|_1\}$（$a,b \in R_p$） |
| Lemma 4 | $\|a \cdot X^i\|_{\infty} = \|a\|_{\infty}$ |

### 8.4 安全性归约链

$$\text{NTRU-OW} \equiv_p \text{C-NTRU-OW} \equiv_p \text{C-NTRU-Dist} \equiv_p \text{D-NTRU-Dist} \implies \text{D-NTRU IND-CPA}$$

### 8.5 性能比公式（渐近）

$$\frac{ES_D}{ES_N} = \frac{(\log_2^2 q + \log_2 q)\log_2 q_2}{(\log_2^2 q_1 + \log_2 q_1 + \log_2^2 q_2 + \log_2 q_2)\log_2 p}$$

$$\frac{DS_D}{DS_N} = \frac{(\log_2^2 q + \log_2 q + \log_2^2 p + \log_2 p)\log_2 q_2}{(2\log_2^2 q_1 + 2\log_2^2 p + \log_2^2 q_2 + \log_2 p^2 q_2^1 q_2)\log_2 p}$$

---

*精读时间：2026-07-29 | 笔记编号：088 | PDF来源：`2018-Eng-Wang-D-NTRU更高效且IND-CPA安全的NTRU变体.pdf`*
