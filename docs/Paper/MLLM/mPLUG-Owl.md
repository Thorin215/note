# mPLUG-Owl

## mPLUG-Owl

![image-20250221110303742](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250221110303742.png)

视觉抽象模块将较长的、细粒度的图像特征概括为少量可学习的 Token，从而实现对视觉信息的高效建模。生成的视觉 Token 与文本查询一起输入到语言模型中，以生成相应的回复。

- 第一阶段：先对齐视觉模态和语言模态。不同于先前的工作，mPLUG-Owl**提出冻住视觉基础模块会限制模型关联视觉知识和文本知识的能力。**因此mPLUG-Owl在第一阶段只冻住LLM的参数，采用LAION-400M，COYO-700M，CC以及MSCOCO**训练视觉基础模块和视觉摘要模块**。
- 第二阶段: 延续mPLUG和mPLUG-2中**不同模态混合训练对彼此有收益的发现**，Owl在第二阶段的指令微调训练中也同时采用了纯文本的指令数据（102k from Alpaca+90k from Vicuna+50k from Baize）和多模态的指令数据(150k from LLaVA)。通过详细的消融实验验证了引入纯文本指令微调在指令理解等方面带来的收益。第二阶段中**视觉基础模块、视觉摘要模块和原始LLM的参数都被冻住**，基于LoRA方式指令微调LLM

## mPLUG-Owl2

![image-20250221120534108](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250221120534108.png)

把两种模态映射到一个共享的语义空间中，保留各自模态的属性，解耦模态的表征。 具体操作：把visual abstractor的输出和文本concat。然后使用modality separated operation φ来进行单模态操作，类似beit3中的通过mask在self-attention模块中选择指定模态的token。 使用LN的时候，使用分离的，也是惯例操作。 这里的创新是，计算k和v的matrix，也做了模态上的分离。**但是query是使用共享的**。 通过这种方式，作者可以计算共享语义空间内这两种模态之间的相似性，同时还通过不同的值投影层保留每种模态的独特特征。**此外，通过解耦k和v的matrix，我们可以避免两种模式之间的干扰，特别是在粒度不匹配方面**



![image-20250222134604211](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250222134604211.png)

### 自注意力融合上下文

 $$\mathcal{C}^{i}=Attn\left(\mathcal{V}^{i},\left[\mathcal{I};\mathcal{V}^{i}\right],\left[\mathcal{I};\mathcal{V}^{i}\right]\right)$$ 

1. 输入说明：   
   - 查询（Query）：$\mathcal{V}^i$是第$i$层的压缩视觉表示，初始化为可学习的查询向量$\mathcal{Q}$。   
   - 键和值（Key/Value）：$\left[\mathcal{I};\mathcal{V}^i\right]$将原始图像块特征$\mathcal{I}$与当前压缩特征$\mathcal{V}^i$在序列维度拼接。   
   - $\mathcal{I}$来自视觉编码器（如ViT），包含图像块的高维特征（数量为$P$），$\mathcal{V}^i$是压缩后的低维特征（数量为$K$，$K \ll P$）。 
2.  核心作用：   
   - 动态信息筛选：通过自注意力机制，$\mathcal{V}^i$作为查询，与拼接后的键/值（包含原始图像和当前压缩特征）交互，筛选出重要信息。   
   - 上下文融合：将原始图像的细节（如局部纹理）与压缩后的高层语义（如物体关系）结合，生成新的上下文特征$\mathcal{C}^i$。   
   - 计算优化：复杂度从$O(P^2)$降至$O(K^2)$，适合处理高分辨率图像。

### SwiGLU非线性增强

$$\mathcal{V}^{i+1}=SwiGLU\left(\mathcal{C}^{i}W*_{1}\right)W_*{2}$$ 

1. 计算步骤：  
   - 线性变换：$\mathcal{C}^i$通过参数矩阵$W*_1 \in \mathbb{R}^{d \times d'}$映射到中间维度$d'$。 
   - SwiGLU激活：对中间特征进行非线性变换，公式为：$$SwiGLU(x) = x \cdot \sigma(\beta x)$$  *其中$\sigma$是Sigmoid函数，$\beta$为可学习参数，通过门控机制过滤冗余信息。*   
   - 维度恢复：通过$W_*2 \in \mathbb{R}^{d' \times d}$将特征映射回原维度$d$，得到下一层压缩表示$\mathcal{V}^{i+1}$。 
2. 核心作用：   
   - 特征提炼：增强非线性表达能力，保留关键语义，抑制噪声（如背景冗余）。   
   - 参数共享：通过共享的$W*_1$和$W_*2$，逐步压缩特征并维持模型轻量化。 
3. 整体意义 
   - 渐进压缩：通过多层堆叠的视觉抽象器，原始图像的$P$个高维特征（如$256$个块）被逐步压缩为$K$个低维语义特征（如$64$个查询），显著减少计算量。 
   - 模态协作：在压缩过程中融合视觉和语言模态的共享语义空间，同时通过位置编码保留空间信息，提升跨模态任务（如VQA、图像描述）的性能。 
   - 平衡效率与性能：既降低了自注意力的复杂度，又通过动态筛选和门控机制保留了关键视觉信息。 



![image-20250222134320132](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250222134320132.png)

![image-20250222134333392](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250222134333392.png)

![image-20250221125239070](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250221125239070.png)

> However, we find that simply freezing a pre trained vision encoder and training a vision-language pro ector to align visual data with language models can limit their capacity to interpret complex visual information, such as scene text and visual knowledge.

## mPLUG-Owl3

![image-20250221120745110](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250221120745110.png)

mPLUG-Owl3 与传统模型的不同之处在于，它不需要提前将视觉序列拼接到语言模型的文本序列中，从而规避了长视觉序列带来的巨额计算开销和显存占用。为了实现视觉信息融入语言模型，团队提出了一种轻量化的 hyperattention 模块



![image-20250221150701655](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250221150701655.png)

1. Hyperattention 模块的工作原理
   通过在整个语言模型中稀疏地扩展 4 个 transformer block，就能用**较小的代价将 LLM 升级成多模态 LLM**。视觉特征在从视觉编码器抽取出来后，会通过一个简单的线性映射把维度对齐到语言模型的维度。随后，**视觉特征只会在这 4 层 transformer block 与文本交互**，保留了细粒度的信息。这种设计的优势在于，它既能够有效地融合视觉信息和语言信息，又能够避免传统方法中因拼接视觉序列而带来的计算负担。同时，通过稀疏扩展 transformer block，模型可以更加高效地处理多模态数据，提高推理速度。

2. 共享语言模型的 layer norm
   在 hyperattention 模块中，选择共享语言模型的 layer norm 对于稳定学习新引入的 cross-attention 至关重要。Layer norm 可以对输入数据进行归一化处理，使得模型在训练过程中更加稳定，避免了梯度消失或爆炸等问题。通过共享 layer norm，模型可以更好地融合视觉和语言信息，提高模型的性能。

3. 并行 cross-attention 和 self-attention
   **hyperattention 采取了一种并行 cross-attention 和 self-attention 的策略**。使用共享的 query 去与视觉特征交互，通过一个 adaptive gate （代码没开源）合两者的特征，使得 query 可以根据自身语义针对性地选择与之相关的视觉特征。这种设计可以提高模型对多模态数据的理解能力，同时也能够提高模型的效率。

4. 多模态交错的旋转位置编码 mi-rope
   为了**建模图像在原始上下文中与文本的相对位置关系**，这种编码方式可以为视觉的 key 建模位置信息，并在 cross-attention 引入 attention mask，让原始上下文中在图片之前的文本不能看到后面的图片对应的特征。这些设计点为 mPLUG-Owl3 带来了进一步的效率提升，保障了它仍然能具备一流的多模态能力。**绝对位置（第几张图）和相对位置（和文本关系）**

![image-20250222154608633](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250222154608633.png)



### 总结与思考

**1. 创新点**

- **高效跨模态融合**：HATB平衡性能与效率，为长序列处理提供新思路。
- **位置感知设计**：MI-Rope解决多图像位置混淆问题，提升逻辑推理能力。

**2. 局限性**

- **视觉编码器冻结**：限制低层视觉任务（如BLINK）的性能。
- **超长视频处理**：默认采样8帧，可能丢失时序细节。

**3. 未来方向**

- **动态帧采样**：根据视频内容自适应选择关键帧。
- **端到端训练**：解冻视觉编码器，增强细粒度感知。
- **多模态扩展**：整合音频等模态，支持更复杂场景。

**思考**：mPLUG-Owl3通过轻量级架构设计，在效率与性能间取得平衡，但其依赖预训练视觉编码器可能成为瓶颈。未来可探索更灵活的模态融合机制，或结合强化学习优化长序列建模。



??? question "如何训练"
      ![image-20250222201213951](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250222201213951.png)

     - During pre-training, only the newly introduced modules are trainable, which include the linear layer following the vision encoder, the visual KV projection, and the Adaptive Gate in the Hyper Attention Transformer Block.

     - Both linear projection and the full language model are trainable. With the help of **tensor parallelism**, the model is spilted into four parts, effectively reducing the memory usage on a single GPU to between 32 and 40 GB.