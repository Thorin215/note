# ViT

## Background

ViT是2020年Google团队提出的将Transformer应用在图像分类的模型，虽然不是第一篇将transformer应用在视觉任务的论文，但是因为其模型“简单”且效果好，可扩展性强（scalable，模型越大效果越好），成为了transformer在CV领域应用的里程碑著作，也引爆了后续相关研究。



## Pipeline

![image-20250223151720502](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250223151720502.png)

![image-20250223151826362](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250223151826362.png)

- patch embedding：
  - 例如输入图片大小为224x224，将图片分为固定大小的patch，patch大小为**16x16**，则每张图像会生成`224x224/16x16=196`个patch，即输入序列长度为**196**，每个patch维度**16x16x3=768**，线性投射层的维度为**768xN** (N=768)，因此输入通过线性投射层之后的维度依然为**196x768**，即一共有196个token，每个token的维度是768。
  - 这里还需要加上一个特殊字符cls，因此最终的维度是**197x768**。到目前为止，已经通过patch embedding将一个视觉问题转化为了一个seq2seq问题。

- positional encoding（standard learnable 1D position embeddings）：
  - ViT同样需要加入位置编码，位置编码可以理解为一张表，表一共有N行，N的大小和输入序列长度相同，每一行代表一个向量，向量的维度和输入序列embedding的维度相同（768）。注意位置编码的操作是sum，而不是concat。加入位置编码信息之后，维度依然是**197x768**
  - 可重复 $L$ 个，其中第 $l$ 个输出为 $z_l$

- `LN -> multi-head attention -> LN`：

  - LN输出维度依然是197x768，多头自注意力时，先将输入映射到q，k，v，如果只有一个头，qkv的维度都是197x768。

  - 如果有12个头（768/12=64），则qkv的维度是197x64，一共有12组qkv，最后再将12组qkv的输出拼接起来，输出维度是197x768，然后在过一层LN，维度依然是197x768

  - 可重复 $L$ 个，其中第 $l$ 个输出为 $z_l$

    

  ![img](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/04857bfb174ce631abbdb18c9d2a63a3.png)

- MLP：
  - 将维度放大再缩小回去，197x768放大为197x3072，再缩小变为197x768，一个block之后维度依然和输入相同，都是197x768，因此可以堆叠多个block。
  - 最后会将特殊字符cls对应的输出 $z_L^0$ 作为encoder的最终输出 ，代表最终的image presentation（另一种做法是不加cls字符，对所有的tokens的输出做一个平均）



## Experiments

![image-20250223165134254](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250223165134254.png)

1. ViT：参考BERT，共设置了三种模型变体（增加了Huge变体）如下图所示。例如ViT-L/16，代表Large变体，输入patch size为16x16。
2. CNN：baseline CNNs选择ResNet，同时用Group Normalization替代Batch Normalization，使用standardized convolutions，以提升模型迁移性能。
3. Hybrid：混合模型就是使用ResNet50输出的特征图，不同stage会得到不同大小的特征图，即生成不同长度序列



## Visualization

![image-20250223165631441](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250223165631441.png)

- 前面层的 “感受野” 虽然差异很大，但总体相比后面层 “感受野” 较小；而模型后半部分 “感受野” 基本覆盖全局，和 CNN 比较类似，说明 ViT 也最后学习到了类似的范式。
- 上图是`VIT-L/32`模型下的位置编码信息，图中每一个方框表示一个patch，图中共有7*7个patch。而每个方框内，也有一个7*7的矩阵，这个矩阵中的每一个值，表示当前patch的position embedding和其余对应位置的position embedding的余弦相似度。**颜色越黄，表示越相似，也即patch和对应位置间的patch密切相关**。注意到每个方框中，最黄的点总是当前patch所在位置，这个不难理解，因为自己和自己肯定是最相似的。除此以外**颜色较黄的部分都是当前patch所属的行和列，以及以当前patch为中心往外扩散的一小圈。这就说明VIT通过位置编码，已经学到了一定的空间局部性**