# MQT-LLaVA : Matryoshka Query Transformer for Large Vision-Language Models

## 研究背景

随着大视觉语言模型（Large Vision-Language Models, LVLMs）的快速发展，如OpenAI的GPT-4、LLaVA等模型在多种视觉-语言任务中表现出色。这些模型通常将图像编码为固定数量的视觉标记（visual tokens），然后与文本输入一起输入到大型语言模型（LLM）中进行处理和推理。然而，现有的LVLMs在处理不同计算资源约束时面临挑战，尤其是在需要灵活调整视觉标记数量的场景中。固定的视觉标记数量可能导致计算资源的浪费或性能的不足，特别是在计算资源有限的情况下。

> - Inspired by Matryoshka Representation Learning, we introduce the Matryoshka Query Transformer (MQT), capable of encoding an image into *m* visual tokens during inference, where *m* can be *any* number up to a predefined maximum.  
>
> - Our exploration of the trade-off between the accuracy and computational cost brought about by the number of visual tokens facilitates future research to achieve the best of both worlds.

??? question "什么是Matryoshka Representation Learning"
      - 人类感知世界是从粗到细粒度，但是模型往往把信息压缩成一个向量。而不同的粒度（维度）的学习可以共享不同的语义空间，比压缩成单独一个向量的方法好。
      
      ![image-20250220192811817](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250220192811817.png)
      ![image-20250220192943496](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250220192943496.png)

??? tip "MRL训练方法"
      - MRL框架下我们先提前指定下后续推理可能要用到的维度。比如最大是2048，最小是8。`nesting_list = [8, 16, 32, 64, 128, 256, 512, 1024, 2048]`
      - 这里有两种方法来训练:
          - Matryoshka Representation Learning（MRL）：该种方法是在bert后面接9个mlp层。mlp(768,8),(768,16),...(768,2048)。然后把bert编码得到的768维向量，在同时通过这9个mlp得到不同维度的向量，然后计算9个loss，累加起来进行训练。
          - Efficient Matryoshka Representation Learning（MRL-E）：该种方法是在bert后面接1个mlp层。mlp(768,2048)，将bert出来的768维度的向量，通过分类层，得到2048维度的向量。取前8个维度，来得到一个向量；取前16个维度，得到一个向量；依次类推，得到9个向量，然后计算9个loss。累加起来进行训练。正如图1中的方法。

## 研究动机

现有的LVLMs通常为所有图像**预定义固定数量的视觉标记**，这限制了模型在不同任务和计算资源下的适应性。不同的任务可能需要不同数量的视觉标记来平衡效率和性能。例如，某些任务可能需要较少的视觉标记以实现实时处理，而其他任务可能需要更多的视觉标记以捕捉更丰富的视觉信息。因此，如何在不牺牲性能的前提下，灵活调整视觉标记的数量，成为了一个重要的研究问题。

## 研究内容

本文提出了**Matryoshka Query Transformer (MQT)**，旨在解决现有LVLMs在视觉标记数量上的不灵活性。具体研究内容包括：

1. **灵活视觉标记数量的实现**：通过引入MQT，模型能够在推理时根据任务需求灵活选择视觉标记的数量，从而适应不同的计算资源约束。

2. **Matryoshka结构的视觉标记**：MQT通过随机选择前*m*个视觉标记进行训练，丢弃剩余的标记，使得模型能够在推理时根据需求选择任意数量的视觉标记。

   > **使用查询变换器（Query Transformer）**：
   >
   > - 首先，图像通过视觉编码器（如 CLIP ViT-L/14）被处理成网格特征（Grid Features）。这些特征会作为输入传递给查询变换器（Query Transformer）。
   > - 查询变换器通过一组潜在的查询 token 来压缩这些图像特征，生成视觉 token。查询变换器的输入是一个包含 M 个潜在查询 token 的集合（例如 M=256）。每个查询 token 通过跨注意力机制与图像特征进行交互，最终将这些信息压缩为视觉 token。
   >
   > **训练过程中的动态选择**：
   >
   > - 在训练过程中，模型会**随机选择**一个数字 **m**，表示要使用的视觉标记数，且 **m ≤ M**（M 是最大标记数）。例如，在某次训练步骤中，可能选择使用 16 个标记，另一时刻则可能选择使用 64 个标记。
   > - 训练过程中，只有选择的前 **m** 个查询 token 被用来生成视觉 token，剩下的 token 会被丢弃。这种方法通过减少每个训练步骤中使用的标记数，减少了计算开销，同时保持了模型的灵活性和适应性。
   >
   > **推理过程中的灵活性**：
   >
   > - 在推理时，模型能够根据需求选择使用 **m** 个视觉 token（m 的数量可以根据任务的计算需求灵活调整）。例如，在某些任务中可能需要更少的视觉 token（例如 2 或 4 个），而在其他任务中，可能需要更多的视觉 token（例如 256 个）来捕捉更细致的图像信息。
   > - 这种灵活性使得模型能够根据不同的计算资源（如内存、处理能力）和任务要求，选择合适的视觉 token 数量，从而在确保性能的同时，优化计算资源的使用。
   >
   > **训练的效率和推理性能**：
   >
   > - 通过这种灵活的训练和推理策略，MQT 能够在推理时减少计算负担，甚至在使用非常少的视觉 token（例如 2 个 token）的情况下，仍能保持较高的性能。例如，MQT 在只使用 2 个视觉 token 时，相比使用 256 个 token，性能的下降只有 3% 左右。

3. **性能与计算效率的权衡**：通过实验验证，MQT能够在减少视觉标记数量的同时，保持或提升模型性能，尤其是在计算资源有限的情况下。

## 研究方法

本文的主要研究方法是基于**Matryoshka Representation Learning (MRL)**的思想，提出了一种新的视觉标记生成方法——Matryoshka Query Transformer (MQT)。具体研究路线如下：

1. **视觉标记的生成**：使用查询变换器（Query Transformer）将图像编码为M*M*个潜在的视觉标记。在训练过程中，随机选择前m*m*个标记进行训练，丢弃剩余的标记。
2. **Matryoshka结构的训练**：通过这种尾部标记丢弃策略，模型能够在推理时灵活选择任意数量的视觉标记，形成类似俄罗斯套娃（Matryoshka）的结构。
3. **模型训练与推理**：在训练阶段，模型仅使用前m*m*个视觉标记进行训练；在推理阶段，模型可以根据需求选择任意数量的视觉标记，从而实现灵活的计算资源分配。

![image-20250220192718993](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250220192718993.png)

![图2：MQT模型结构](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250220195854279.png)

## 实验

![image-20250220162340037](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250220162340037.png)

![image-20250220162842613](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250220162842613.png)

本文通过多个实验验证了MQT的有效性，具体实验设置如下：

### 1. 数据集

实验在11个主流的视觉-语言基准数据集上进行，包括：

- **VizWiz**：盲人视觉问答数据集。
- **ScienceQA-IMG**：科学问题回答数据集。
- **VQA-v2**：视觉问答数据集。
- **GQA**：真实世界的视觉推理和组合问答数据集。
- **POPE**：对象幻觉评估数据集。
- **MME Perception** 和 **MME Cognition**：多模态评估数据集。
- **MMBench**：多模态模型综合评估基准。
- **LLaVA-Bench (In-the-Wild)**：LLaVA模型的野外评估基准。
- **MM-Vet**：多模态模型的综合能力评估基准。

### 2. 对比算法和主算法

- **对比算法**：LLaVA-1.5、BLIP-2、InstructBLIP、Shikra、IDEFICS、Qwen-VL等。
- **主算法**：MQT-LLaVA，基于LLaVA-1.5，使用MQT生成视觉标记。

### 3. 评估方式

实验通过在不同数量的视觉标记下（如2、4、8、16、36、64、144、256）评估模型性能，比较了MQT-LLaVA与其他模型的性能差异。评估指标包括准确率、计算效率（TFLOPs）等。

### 4. 结论

- **性能对比**：MQT-LLaVA在使用256个视觉标记时，在11个基准数据集上表现优于或与LLaVA-1.5相当，且在6个数据集上表现更好。
- **计算效率**：MQT-LLaVA在使用16个视觉标记时，计算效率提升了8倍，性能仅下降了2.4个点。
- **灵活性**：MQT-LLaVA能够在推理时灵活选择任意数量的视觉标记，适应不同的计算资源需求。

## 总结与思考

本文提出的MQT-LLaVA模型通过引入Matryoshka Query Transformer，成功实现了在推理时灵活调整视觉标记数量的目标。这一创新不仅提升了模型的计算效率，还在多个基准数据集上保持了较高的性能。通过实验验证，MQT-LLaVA在减少视觉标记数量的同时，性能损失较小，尤其是在计算资源有限的情况下表现出色。**低维度长文本，不是一味的刷榜，迁移到其他领域。其实工作量也不大**

### 心得体会

1. **灵活性与效率的平衡**：MQT-LLaVA的成功表明，通过灵活调整视觉标记数量，可以在不显著牺牲性能的前提下，大幅提升计算效率。这对于实际应用中的资源分配具有重要意义。
2. **任务依赖性**：不同任务对视觉标记数量的需求不同，MQT-LLaVA能够根据任务需求灵活调整，显示出其强大的适应性。
3. **未来研究方向**：尽管MQT-LLaVA在256个视觉标记内表现出色，但未来可以探索更大范围的视觉标记数量，以进一步提升模型的灵活性和性能。

### 疑难问题

1. **最大视觉标记数量的限制**：MQT-LLaVA目前支持的最大视觉标记数量为256，未来是否可以扩展到更大的范围？
2. **训练成本**：虽然MQT减少了推理时的计算成本，但训练过程中仍然需要处理大量的视觉标记，如何进一步降低训练成本是一个值得探讨的问题。
