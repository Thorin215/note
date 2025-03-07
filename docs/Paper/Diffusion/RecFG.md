# Rectified Diffusion Guidance for Conditional Generation

这篇文章题为《Rectified Diffusion Guidance for Conditional Generation》，主要探讨了在扩散模型（Diffusion Probabilistic Models, DPMs）中，如何通过修正无分类器引导（Classifier-Free Guidance, CFG）来解决生成过程中的期望偏移问题。以下是文章的主要内容和分析：

### 1. **背景与问题**
扩散模型（DPMs）近年来在高分辨率图像生成方面取得了显著进展，尤其是在文本到图像生成等任务中表现出色。无分类器引导（CFG）是一种常用的技术，通过结合条件和无条件的得分函数来改善条件生成的效果。然而，CFG在理论上存在一个问题：它不能表示为扩散过程的逆过程，导致生成分布的期望与真实条件分布的期望不一致，尤其是在使用较大的引导强度时，这种现象更为明显。

### 2. **主要贡献**
文章的主要贡献在于提出了**Rectified Classifier-Free Guidance (RecFG)**，通过放松CFG中的引导系数约束，使得去噪过程严格符合扩散理论。具体来说，RecFG引入了两个系数 $\gamma_1$ 和 $\gamma_0$，分别用于条件得分函数和无条件得分函数，并通过理论分析和实验验证了其有效性。

### 3. **理论分析**
文章首先回顾了CFG的理论基础，指出CFG在引导过程中会导致生成分布的期望偏移。通过一个简单的玩具分布，文章定量分析了这种期望偏移的现象，并提出了RecFG的修正方法。RecFG通过引入两个系数 $\gamma_1$和 $\gamma_0$，使得生成分布的期望与真实条件分布的期望一致，同时保持较小的方差。

### 4. **实验验证**
文章在多个数据集和模型上进行了实验，验证了RecFG的有效性。实验结果表明，RecFG在保持生成图像质量的同时，能够显著减少期望偏移现象，尤其是在较大的引导强度下表现更为突出。实验还展示了RecFG在不同模型（如LDM、EDM2、SD3）和不同引导强度下的兼容性。

### 5. **技术细节**
- **期望偏移的定量分析**：文章通过一个玩具分布，推导出了期望偏移的闭式解，并展示了随着引导强度的增加，期望偏移会变得更加严重。
- **RecFG的实现**：RecFG通过预计算一个查找表（lookup table）来确定每个像素的引导系数 $\gamma_0$，从而在生成过程中动态调整引导强度。
- **查找表的优化**：文章还讨论了查找表的优化策略，提出可以通过遍历数据集中的部分样本来提高计算效率，而不需要遍历所有可能的条件。

### 6. **实验结果**
实验结果表明，RecFG在多个数据集（如ImageNet、CC12M）和模型（如LDM、EDM2、SD3）上均表现出色，尤其是在较小的NFE（Number of Function Evaluations）和较大的引导强度下，RecFG能够显著提升生成图像的质量和条件一致性。

### 7. **讨论与未来工作**
文章指出，尽管RecFG在理论上和实验中都表现出色，但仍有一些潜在的限制。例如，预计算查找表的过程可能耗时较长，尤其是在开放词汇的文本条件生成任务中。未来的工作可以探索如何通过预测网络来动态生成引导系数，从而进一步提高效率。

### 8. **结论**
文章总结了CFG的理论缺陷，并通过引入RecFG解决了期望偏移的问题。RecFG通过放松引导系数的约束，使得生成过程更加符合扩散理论，并且在实验中表现出色。文章还提出了未来的研究方向，鼓励社区进一步探索引导生成的理论和实践。

### 总结
这篇文章通过理论分析和实验验证，提出了一种新的引导方法RecFG，解决了CFG在扩散模型中的期望偏移问题。RecFG不仅保持了生成图像的高质量，还显著提升了条件生成的一致性，尤其是在较大的引导强度下表现尤为突出。文章的贡献在于为扩散模型的引导生成提供了一个新的视角，并为未来的研究提供了方向。



## 1. **背景与问题**

扩散模型（Diffusion Probabilistic Models, DPMs）在生成任务中表现出色，尤其是文本到图像生成。无分类器引导（Classifier-Free Guidance, CFG）通过结合条件得分函数和无条件得分函数来改善生成效果，但其理论存在缺陷：CFG 不能表示为扩散过程的逆过程，导致生成分布的期望与真实条件分布的期望不一致，称为**期望偏移**。

---

## 2. **CFG 的期望偏移问题**

### 2.1 CFG 的基本公式

CFG 的得分函数为：

$$
\nabla_{\mathbf{x}_t} \log q_{t,\gamma}(\mathbf{x}_t | c) = \gamma \nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t | c) + (1 - \gamma) \nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t),
$$

其中：
- $\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t | c)$ 是条件得分函数，
- $\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t)$ 是无条件得分函数，
- $\gamma$ 是引导强度。

### 2.2 期望偏移的理论分析

扩散模型的核心假设是得分函数的期望为零：

$$
\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t | c)] = 0.
$$

然而，CFG 的得分函数期望不为零：

$$
\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_{t,\gamma}(\mathbf{x}_t | c)] = (1 - \gamma) \mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t)].
$$

由于无条件得分函数的期望不为零，CFG 会导致生成分布的期望与真实条件分布的期望不一致，称为**期望偏移**。

---

## 3. **Rectified Classifier-Free Guidance (RecFG)**

### 3.1 RecFG 的基本公式

RecFG 引入了两个系数 $\gamma_1$ 和 $\gamma_0$，分别用于条件得分函数和无条件得分函数：

$$
\nabla_{\mathbf{x}_t} \log q_{t,\gamma_1,\gamma_0}(\mathbf{x}_t | c) = \gamma_1 \nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t | c) + \gamma_0 \nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t).
$$

### 3.2 期望偏移的消除

为了消除期望偏移，RecFG 需要满足：

$$
\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_{t,\gamma_1,\gamma_0}(\mathbf{x}_t | c)] = 0.
$$

代入 RecFG 的得分函数，得到：

$$
\gamma_1 \mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t | c)] + \gamma_0 \mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t)] = 0.
$$

由于 $\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t | c)] = 0$，上式简化为：

$$
\gamma_0 \mathbb{E}_{q_t(\mathbf{x}_t | c)}[\nabla_{\mathbf{x}_t} \log q_t(\mathbf{x}_t)] = 0.
$$

因此，$\gamma_0$ 的取值为：

$$
\gamma_0 = -(\gamma_1 - 1) \cdot \frac{\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\epsilon_\theta(\mathbf{x}_t, c, t)]}{\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\epsilon_\theta(\mathbf{x}_t, t)]},
$$

其中 $\epsilon_\theta(\mathbf{x}_t, c, t)$ 和 $\epsilon_\theta(\mathbf{x}_t, t)$ 分别是条件和无条件噪声预测模型。

---

## 4. **期望偏移的定量分析**

### 4.1 玩具分布的设定

文章使用了一个简单的玩具分布：

$$
q_0(\mathbf{x}_0 | c) \sim \mathcal{N}(c, 1), \quad q(c) \sim \mathcal{N}(0, 1), \quad q_0(\mathbf{x}_0) \sim \mathcal{N}(0, 2).
$$

### 4.2 期望偏移的闭式解

通过求解扩散过程的逆过程，文章推导出了 CFG 引导下生成分布的期望偏移的闭式解：

$$
q^{\text{deter}}_{0,\gamma}(\mathbf{x}_0 | c) \sim \mathcal{N}\left(c \phi(\gamma, T), 2^{1-\gamma} \frac{T+1}{(T+1)^\gamma (T+2)^{1-\gamma}}\right),
$$

其中 $\phi(\gamma, T)$ 是一个与引导强度 $\gamma$ 和时间步 $T$ 相关的函数。当 $T \to +\infty$ 时，$\phi(\gamma)$ 的表达式为：

$$
\phi(\gamma) = \lim_{T \to +\infty} \phi(\gamma, T).
$$

### 4.3 期望偏移的性质

- 当 $\gamma = 1$ 时，$\phi(1) = 1$，即没有期望偏移。
- 当 $\gamma > 1$ 时，$\phi(\gamma) > 1$，且随着 $\gamma$ 的增加，期望偏移变得更加严重。
- 当 $\gamma \to +\infty$ 时，$\phi(\gamma) \to 2$。

---

## 5. **RecFG 的实现**

### 5.1 查找表的构建

为了高效计算 $\gamma_0$，文章提出通过遍历数据集中的样本，预计算一个查找表（lookup table）。查找表中存储了每个条件 $c$ 和时间步 $t$ 下的期望比值：

$$
\text{Expectation Ratio} = \frac{\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\epsilon_\theta(\mathbf{x}_t, c, t)]}{\mathbb{E}_{q_t(\mathbf{x}_t | c)}[\epsilon_\theta(\mathbf{x}_t, t)]}.
$$

### 5.2 生成过程的实现

在生成过程中，RecFG 通过查找表动态调整 $\gamma_0$，从而确保生成分布的期望与真实条件分布的期望一致。

---

## 6. **总结**

RecFG 通过引入两个系数 $\gamma_1$ 和 $\gamma_0$$，并设计约束条件，消除了 CFG 的期望偏移问题。通过理论分析和实验验证，RecFG 在生成任务中表现出色，尤其是在较大的引导强度下表现尤为突出。



**噪声预测模型（Noise Prediction Model）** 是扩散模型（Diffusion Models）中的核心组件之一，用于估计在扩散过程中添加到数据中的噪声。它的作用是**预测给定时间步 $t$ 和数据 $\mathbf{x}_t$ 时，噪声的分布或具体值**。噪声预测模型在扩散模型的反向过程中起着关键作用，帮助模型逐步从噪声数据中恢复出原始数据。

---

## 1. **扩散模型的基本框架**

扩散模型是一种生成模型，通过两个过程来生成数据：
1. **前向过程（Forward Process）**：逐步向数据 $\mathbf{x}_0$ 添加噪声，生成一系列噪声数据 $\mathbf{x}_1, \mathbf{x}_2, \dots, \mathbf{x}_T$。
2. **反向过程（Reverse Process）**：从噪声数据 $\mathbf{x}_T$ 开始，逐步去除噪声，恢复出原始数据 $\mathbf{x}_0$。

在前向过程中，数据 $\mathbf{x}_t$ 可以通过以下公式生成：

$$
\mathbf{x}_t = \sqrt{\alpha_t} \mathbf{x}_0 + \sqrt{1 - \alpha_t} \epsilon_t,
$$

其中：
- $\alpha_t$ 是噪声调度（noise schedule），控制噪声的强度，
- $\epsilon_t \sim \mathcal{N}(0, \mathbf{I})$ 是标准高斯噪声。

---

## 2. **噪声预测模型的作用**

在反向过程中，模型需要从噪声数据 $\mathbf{x}_t$ 中恢复出原始数据 $\mathbf{x}_0$。为了实现这一点，模型需要**预测在前向过程中添加的噪声**，然后从 $\mathbf{x}_t$ 中减去预测的噪声，逐步恢复出 $\mathbf{x}_0$。

噪声预测模型的目标是**估计噪声 $\epsilon_t$**，即：

$$
\epsilon_\theta(\mathbf{x}_t, t) \approx \epsilon_t,
$$

其中 $\theta$ 是模型的参数。

---

## 3. **噪声预测模型的训练**

噪声预测模型通过最小化以下损失函数来训练：

$$
\mathcal{L}(\theta) = \mathbb{E}_{\mathbf{x}_0, \epsilon_t, t} \left[ \| \epsilon_\theta(\mathbf{x}_t, t) - \epsilon_t \|^2 \right],
$$

其中：
- $\mathbf{x}_0$ 是原始数据，
- $\epsilon_t$ 是前向过程中添加的噪声，
- $\mathbf{x}_t = \sqrt{\alpha_t} \mathbf{x}_0 + \sqrt{1 - \alpha_t} \epsilon_t$ 是噪声数据，
- $t$ 是时间步。

通过最小化这个损失函数，噪声预测模型能够学会从噪声数据 $\mathbf{x}_t$ 中预测出噪声 $\epsilon_t$。

---

## 4. **噪声预测模型在反向过程中的应用**

在反向过程中，噪声预测模型用于逐步去除噪声。具体步骤如下：

1. **输入**：噪声数据 $\mathbf{x}_t$ 和时间步 $t$。
2. **预测噪声**：使用噪声预测模型估计噪声：
   $$
   \hat{\epsilon}_t = \epsilon_\theta(\mathbf{x}_t, t).
   $$
3. **恢复数据**：从 $\mathbf{x}_t$ 中减去预测的噪声，得到更接近原始数据的 $\mathbf{x}_{t-1}$：
   $$
   \mathbf{x}_{t-1} = \frac{1}{\sqrt{\alpha_t}} \left( \mathbf{x}_t - \sqrt{1 - \alpha_t} \hat{\epsilon}_t \right).
   $$
4. **迭代**：重复上述步骤，直到恢复到原始数据 $\mathbf{x}_0$。

---

## 5. **条件噪声预测模型**

在条件生成任务中（如文本到图像生成），噪声预测模型还需要考虑条件信息 $c$（如文本描述）。此时，噪声预测模型的形式为：

$$
\epsilon_\theta(\mathbf{x}_t, c, t) \approx \epsilon_t,
$$

其训练目标为：

$$
\mathcal{L}(\theta) = \mathbb{E}_{\mathbf{x}_0, \epsilon_t, c, t} \left[ \| \epsilon_\theta(\mathbf{x}_t, c, t) - \epsilon_t \|^2 \right].
$$

---

## 6. **噪声预测模型的意义**

噪声预测模型是扩散模型的核心组件，其作用包括：
1. **实现反向过程**：通过预测噪声，逐步从噪声数据中恢复出原始数据。
2. **提高生成质量**：准确的噪声预测能够提高生成数据的质量和一致性。
3. **支持条件生成**：通过引入条件信息，噪声预测模型能够实现条件生成任务（如文本到图像生成）。

---

## 7. **总结**

噪声预测模型是扩散模型中的关键组件，用于预测在前向过程中添加到数据中的噪声。通过训练噪声预测模型，扩散模型能够在反向过程中逐步去除噪声，恢复出原始数据。噪声预测模型的设计和优化对扩散模型的性能至关重要，尤其是在条件生成任务中。