# 📄 Cryptography from Pseudorandom Quantum States (来自伪随机量子态的密码学)

> **Prabhanjan Ananth, Luowen Qian, Henry Yuen**
> UCSB / Boston University / Columbia University
> *CRYPTO 2022* / *arXiv:2112.10020v2*

---

## 一、基本信息

| 项目 | 内容 |
|------|------|
| **论文标题** | Cryptography from Pseudorandom Quantum States |
| **中文译名** | 来自伪随机量子态的密码学 |
| **作者** | Prabhanjan Ananth (UCSB), Luowen Qian (Boston University), Henry Yuen (Columbia University) |
| **发表年份** | 2022 |
| **会议/期刊** | CRYPTO 2022 (国际密码学顶级会议) |
| **arXiv** | 2112.10020v2 |
| **页数** | 50页 |
| **研究领域** | 量子密码学 / 理论密码学 / 量子计算 |
| **核心假设** | 伪随机量子态 (PRS) — 不依赖单向函数 |

### 论文定位

本文是**量子密码学基础理论**的重大突破——证明了在**不需要单向函数（One-Way Functions）** 的世界中，仅基于**伪随机量子态（PRS）** 即可构造有意义的密码学原语（承诺方案、伪随机一次性密钥加密、安全多方计算等）。这从根本上修正了经典密码学中"所有有趣的密码学原语都蕴含单向函数"的信念。

---

## 二、研究背景与问题

### 2.1 经典密码学的基本信念

在经典密码学中，Goldreich (1990) 证明了以下深刻结果：加密、承诺方案、伪随机生成器等大多数"有趣的"密码学任务都**蕴含单向函数的存在**。单向函数（容易正向计算但难以求逆的函数）因此被视为密码学的**最小必要假设**。Impagliazzo (1995) 在其著名的"五个世界"框架中，将没有单向函数的世界称为"Algorithmica"——一个算法天堂但密码学荒原。

### 2.2 量子信息的挑战

量子信息处理为密码学带来了新的可能性：
- Bennett-Brassard (BB84) 量子密钥分发实现了**无条件安全**的密钥交换，无需计算假设
- Bartusek-Coladangelo-Khurana-Ma (BCKM21b) 和 Grilo-Lin-Song-Vaikuntanathan (GLSV21) 展示了基于**后量子单向函数**即可实现安全多方计算（而经典情况下需要更强的假设）

这些例子引出一个自然问题：**在量子世界中，单向函数是否仍然是必要的？**

### 2.3 伪随机量子态 (PRS) 的引入

Ji, Liu, Song (Crypto'18) 引入了**伪随机量子态 (PRS)** 的概念：一个量子多项式时间 (QPT) 算法，输入 λ 位密钥，输出 n 量子比特的态，与 Haar 随机态计算不可区分。与经典 PRG 不同：
- PRS 的**输出长度难以伸缩**（既不能拉伸也不能缩短）
- PRS 的**各量子比特高度纠缠**，不能独立使用
- 丢弃任意一个量子比特都会导致混合态，与 Haar 随机态轻易可区分

### 2.4 Kretschmer 的突破性结果

Kretschmer (TQC'20) 构造了一个**谕示世界**：其中**不存在单向函数**但**伪随机量子态仍然存在**。这意味着 PRS 严格弱于单向函数，从而引发核心问题：

> **仅基于伪随机量子态，能构造哪些密码学任务？**

---

## 三、核心方法与创新点

### 3.1 新概念：伪随机函数型量子态 (PRFS)

**PRFS 是本论文最核心的创新概念**。PRS 的类比是经典 PRG（每个密钥输出一个伪随机串），而 PRFS 的类比是经典 PRF（每个密钥可对多个输入产生多个伪随机输出）。

**(d, n)-PRFS 生成器定义**：一个 QPT 算法 G，输入密钥 k ∈ {0,1}^λ 和输入 x ∈ {0,1}^d，输出 n 量子比特的态 |ψ_{k,x}⟩。安全性要求：对手即使自适应选择输入，也无法区分 (|ψ_{k,x_1}⟩, ..., |ψ_{k,x_s}⟩) 与独立 Haar 随机态的张量积。

**PRFS 相对于 PRS 的关键优势**：
- 输出可以**分解为张量积**，各分量可独立使用
- 同一密钥可产生**多个伪随机态**，实现密钥复用
- 有效解决了 PRS "输出纠缠不可分离" 的固有问题

### 3.2 PRFS 的构造（从 PRS 出发）

**核心洞察：后选择 (Post-Selection)**

从 (d+n)-qubit PRS 构造 (d, n)-PRFS：

1. 将 PRS 输出态写作 |ψ⟩ = Σ_{x∈{0,1}^d} α_x |x⟩ ⊗ |ψ_x⟩
2. 通过**后选择**前 d 个量子比特得到 |x⟩，残留下 n 个量子比特即为 PRFS 输出 |ψ_x⟩
3. 利用 **Lévy 引理**（Haar 测度的测度集中）证明：|α_x|² ≈ 1/2^d，使得重复 2^d·λ 次测量能以极高概率成功
4. 即使后选择可能失败（输出 |⊥⟩），仍满足**可识别中止 (Recognizable Abort)** 属性

**关键技术观察**：
- Haar 随机态经部分测量后，残存态在酉不变性下仍然是 Haar 随机的（Observation 2）
- PRS 的伪随机性保证了后选择概率在真实构造与理想 Haar 情况下相近

**参数范围**：对于 d = O(log λ) 和 n = d + ω(log log λ)，可以从 (d+n)-qubit PRS 构造 (d, n)-PRFS。

### 3.3 伪随机一次性密钥加密 (Pseudo QOTP)

从 (d, n)-PRFS（d ≥ ⌈log ℓ⌉ + 1, n = ω(log λ)）构造 μ-bit 消息的加密：

- **加密**：对消息 x 的第 i 位，生成 PRFS 态 G(k, (i, x_i))，密文为张量积
- **解密**：利用 Test 算法逐位验证，确定原始消息比特
- **安全性**：源于 PRFS 的伪随机性——密文与随机比特串的 Haar 张量积计算不可区分

**关键特征**：消息长度 ℓ = 2^d 可远大于密钥长度 λ，这在信息论上不可能，需要计算假设。

### 3.4 统计绑定量子承诺方案

基于 Naor 的经典承诺方案框架，用 PRFS 替代 PRG：

- **承诺阶段**：接收方发送随机 Pauli 算符 P；发送方采样密钥 k，发送 P^b·G(k,·)·P^b（b 为承诺比特）
- **揭示阶段**：发送方发送 (k, b)，接收方验证 P^b·c·P^b 是否为 PRFS 输出的张量积
- **隐藏性**：由 PRFS 安全性+Haar 随机态的酉不变性保证
- **绑定**：通过提取器定义（投影到 Π_b 子空间），证明了 real/ideal world 的统计不可区分性

**核心技术挑战**：
- Π_0 和 Π_1 正交吗？不一定。使用随机 Pauli 算符使 Tr(Π_0 Π_1) 在平均意义下极小
- PRFS 可能输出混合态（非完美状态生成），需处理 Recognizable Abort

### 3.5 安全多方计算 (MPC)

将上述承诺方案代入 BCKM21b 的框架，直接获得**不诚实多数下的恶意安全 MPC 协议**。这代表首次在单向函数的统一框架之外实现安全计算。

---

## 四、实验设计与结果

本文属于**理论密码学**论文，主要成果为安全性归约和数学证明，不涉及数值实验。其"结果"体现为以下定理：

### 定理 1（伪一次性密钥加密）
假设存在 (d, n)-PRFS，其中 d = O(log λ), n = ω(log λ)，则存在消息长度 ℓ = 2^d 的安全量子伪随机一次性密钥加密方案。

### 定理 2（统计绑定承诺）
假设存在 (d, n)-PRFS（具有 Recognizable Abort），且 2^d·n ≥ 7λ，则存在统计绑定且计算隐藏的量子承诺方案。

### 推论 1（安全多方计算）
在上述 PRFS 假设下，存在不诚实多数下的恶意安全多方计算协议。

### 定理 4（PRFS 的构造）
对于 d = O(log λ) 和 n = d + ω(log log λ)，若存在 (d+n)-qubit PRS，则可构造 (d, n)-PRFS。

### 推论 5（从 PRS 直达密码学）
- **ω(log λ)-qubit PRS** → 任意多项式长度消息的伪随机一次性密钥加密
- **(2 log λ + ω(log log λ))-qubit PRS** → 统计绑定承诺方案 → 安全多方计算

这些参数要求是**几乎最优的**：Brakerski-Shmueli (BS20) 证明了输出长度为 c·log λ（c < 1）的 PRS 可以是信息论安全的，因此密码学必须要求输出长度超过 log λ。

### 其他应用

- **CPA 安全的多密钥加密方案**（需 PRFS 具有多项式长度输入）
- **消息认证码 (MAC)**
- **量子混淆电路 (Quantum Garbling Schemes)**

---

## 五、主要结论

1. **修正了密码学基本信念**：在量子世界中，单向函数并非密码学的必要条件。仅依赖 PRS（严格弱于单向函数）即可实现承诺方案、安全计算等核心密码学任务。

2. **PRFS 概念的提出**：伪随机函数型量子态是 PRS 到密码学应用之间的"缺失环节"。它弥补了 PRS "输出纠缠不可分离"的结构性缺陷，使其能够模拟经典 PRG 的张量积结构。

3. **后选择技术**是从 PRS 到 PRFS 构造的关键，展示了 Haar 随机态的测度集中和酉不变性在密码学构造中的强大力量。

4. **与 Morimae-Yamakawa (MY21)** 的并⾏工作相比：MY21 证明的是较弱"sum-binding"概念，且需要 3λ→λ 的 PRS stretch；本文证明了更强的 statistical binding，且只需 2 log λ + ω(log log λ) 的输出长度。

5. **开放问题**：能否从 PRS 构造**数字签名**？能否实现 PRS 输出的**任意拉伸**（类比经典 PRG 的 stretch amplification）？

---

## 六、批判性评价

### 优势与创新

| 维度 | 评价 |
|------|------|
| **概念创新** | ⭐⭐⭐⭐⭐ 提出 PRFS 概念，精准填补了 PRS 与密码学应用之间的鸿沟 |
| **理论深度** | ⭐⭐⭐⭐⭐ 完整的归约链：PRS → PRFS → QOTP/承诺 → MPC，技术细节扎实 |
| **哲学意义** | ⭐⭐⭐⭐⭐ 从根本上修正了"密码学必须依赖单向函数"的经典信念 |
| **证明技巧** | ⭐⭐⭐⭐⭐ 后选择+Lévy引理+Haar酉不变性的组合使用非常精妙 |

### 局限与不足

| 维度 | 评价 |
|------|------|
| **假设的实际可行性** | PRS 在当前技术下尚无可证明的实例。随机量子电路被推测为候选构造，但无法在标准假设下证明 |
| **效率** | 承诺方案中的 2^d 因子在 d = O(log λ) 时是多项式的，但常数可能很大 |
| **PRFS 选择性安全** | 本文仅考虑"选择性安全"（挑战输入在密钥采样前固定），自适应安全的 PRFS 留待后续工作 [AQY22] |
| **CPA 加密和 MAC 的限制** | 需要多项式长度输入的 PRFS，而目前只能从 PRS 构造 O(log λ) 输入长度的 PRFS |
| **量子通信要求** | 所有构造都需要量子通信通道，在实际部署中受限 |

### 与 Morimae-Yamakawa 的详细对比

| 方面 | 本文 | MY21 |
|------|------|------|
| 绑定定义 | 强 statistical binding（提取器定义） | 较弱 sum-binding |
| PRS 参数要求 | 2 log λ + ω(log log λ) qubits | 3λ qubits（需 stretch） |
| 承诺轮数 | 两轮交互 | 非交互 |
| 打开消息 | 经典 | 量子 |
| 新概念 | PRFS（本文独有） | 无 |

---

## 七、论文间关联与对比

### 与本知识库中相关论文的联系

| 关联论文 | 关系 | 说明 |
|----------|------|------|
| Ji, Liu, Song (Crypto'18) — 未收录 | **前驱工作** | PRS 概念的首次提出 |
| Kretschmer (TQC'20) — 未收录 | **关键动机** | 证明 PRS oracle-separates from OWF |
| Brakerski-Shmueli (BS19/BS20) — 未收录 | **技术基础** | PRS 的可扩展构造 |
| BCKM21b / GLSV21 — 未收录 | **应用框架** | 量子承诺→MPC 框架 |
| Naor (1991) — 未收录 | **经典模板** | Naor 承诺方案（本文直接改编） |
| Bennett-Brassard (BB84) — 未收录 | **精神先驱** | 量子信息削弱密码学假设的先例 |

### 在 Impagliazzo 世界观中的位置

```
Impagliazzo五世界:
  Algorithmica  (P=NP, 无密码学)
      ↓  + PRS (本文)
  QAlgorithmica (P=NP, 但有量子密码学!)
      ↓  + OWF
  MiniCrypt     (有对称密码学, 无公钥)
      ↓  + 公钥假设
  Cryptomania   (公钥密码学)
```

本文核心贡献：在 Algorithmica 和 MiniCrypt 之间开辟了新的可能性——**QAlgorithmica**。

---

## 八、关键公式速查

### PRFS 安全性定义

区分器优势的上界：

$$\left| \Pr_{k \leftarrow \{0,1\}^\lambda}[\mathcal{A}(x_1,...,x_s, G_\lambda(k,x_1)^{\otimes t},...,G_\lambda(k,x_s)^{\otimes t}) = 1] - \Pr_{|\vartheta_i\rangle \leftarrow \mathcal{H}_n}[\mathcal{A}(x_1,...,x_s, |\vartheta_1\rangle^{\otimes t},...,|\vartheta_s\rangle^{\otimes t}) = 1] \right| \leq \text{negl}(\lambda)$$

### Lévy 引理（核心分析工具）

对于 K-Lipschitz 函数 f：ℂ^d → ℝ：

$$\Pr_{|\psi\rangle \leftarrow \mathcal{H}(\mathbb{C}^d)}\left[|f(|\psi\rangle) - \mathbb{E}f| \geq \delta\right] \leq \exp\left(-\frac{C d \delta^2}{K^2}\right)$$

其中 C > 0 为普适常数。

### SWAP 测试

Haar 随机态经 SWAP 测试的接受概率：

$$\frac{1}{2} + \frac{1}{2} \cdot \mathbb{E}_{|\varphi_1\rangle,|\varphi_2\rangle \leftarrow \mathcal{H}_n} |\langle \varphi_1|\varphi_2\rangle|^2 = \frac{1}{2} + \frac{1}{2} \cdot 2^{-n}$$

### PRFS 输出正交性

对于 (d, n)-PRFS 生成器 G 和 x ≠ y：

$$\mathbb{E}_{k \leftarrow \{0,1\}^\lambda} \text{Tr}(G_\lambda(k,x) G_\lambda(k,y)) \leq 2^{-n} + \text{negl}(\lambda)$$

### PRFS 输出纯度

$$\mathbb{E}_{k \leftarrow \{0,1\}^\lambda} \text{Tr}(G_\lambda(k,x)^2) \geq 1 - \text{negl}(\lambda)$$

### 量子伪随机一次性密钥加密构造

加密：$\sigma = \bigotimes_{i=1}^{\ell} G_\lambda(k, (i, x_i))$

解密：对每个 i，运行 $\text{Test}(k, (i, 0), \sigma_i)$，若接受则 x_i = 0，否则 x_i = 1。

### 参数约束汇总

| 目标原语 | PRFS 需求 | PRS 需求 |
|----------|-----------|----------|
| Pseudo QOTP | d = O(log λ), n = ω(log λ) | ω(log λ)-qubit PRS |
| 统计绑定承诺 | 2^d·n ≥ 7λ | (2 log λ + ω(log log λ))-qubit PRS |
| 安全 MPC | 同上 | 同上 |
| CPA 加密 / MAC | d = ω(log λ) | 未解决（开放问题） |

---

> **精读日期**：2026-07-25
> **精读者**：AI 文献助手
> **源文件**：2022-Eng-Ananth-来自伪随机量子态的密码学.pdf
