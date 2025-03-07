# Infinity: Scaling Bitwise AutoRegressive Modeling for High-Resolution Image Synthesis

## Background

近年来，视觉自回归建模（Visual AutoRegressive Modeling, VAR）通过将自回归学习重新定义为从粗到细的“下一尺度预测”（next-scale prediction），显著提升了生成质量和采样速度。VAR利用了大型语言模型（LLMs）的强大扩展能力，并能够同时优化前一个尺度的步骤，从而结合了扩散模型的优势。然而，VAR仍然受限于离散Tokenizer的词汇量大小，导致在高分辨率图像中难以重建细粒度细节。



## Pipeline

- **比特粒度多尺度量化器（Bitwise Multi-scale Quantization Tokenizer）：** 将图像特征转换为二进制 token，降低计算开销，并实现更细粒度的图像表示。

- **基于 Transformer 的自回归模型：** 根据文本提示和先前生成的图像内容，预测图像的剩余部分。

- **比特自校正机制：** 在训练过程中引入随机比特翻转，增强模型对错误的鲁棒性，并提高生成图像的质量。

  > 为了更好地理解 Index-wise Token 和 Bitwise Token 的区别，可以将它们类比为两种不同的颜色编码方式：
  >
  > - **Index-wise Token:** 类似于用数字来表示颜色，例如 1 代表红色，2 代表绿色，3 代表蓝色。当颜色发生细微变化时，对应的数字可能会发生跳变，导致模型难以学习。
  > - **Bitwise Token:** 类似于用二进制代码来表示颜色，例如 001 代表红色，010 代表绿色，100 代表蓝色。当颜色发生细微变化时，只有少数比特位发生改变，其他比特位保持不变，从而提供更稳定的监督信号，使模型更容易学习。
  >
  > Bitwise Token 的优势在于：
  >
  > - **更细粒度的表示:** 能够学到更细粒度的高频信号，生成图像的细节更加丰富。
  > - **更容易优化:** 当量化前的连续特征发生微小扰动后，Bitwise Token 仅有一个比特标签发生翻转，其他比特标签仍能提供稳定监督。
  > - Infinity相比VAR的最大的变动还是在tokenizer，Infinity的tokenizer是采用了LFQ(Lookup-Free Quantizer)来量化特征，之前经典的VQ量化是需要去计算特征和codebook中所有code embedding之间的距离，并选择距离最近的code来量化。而LFQ是直接将特征按值的符号进行二值化量化

![image-20250307134512616](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250307134512616.png)

![image-20250307134550629](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250307134550629.png)

![image-20250307134619652](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250307134619652.png)

![image-20250307134705627](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250307134705627.png)



![image-20250307135452369](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250307135452369.png)





![image-20250306235400854](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250306235400854.png)





![image-20250306235915467](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250306235915467.png)

具体来说，IVC通过 *d* 个二进制分类器并行预测下一个尺度残差的二进制位，从而避免了传统分类器在大词汇量下的计算瓶颈。IVC不仅显著减少了参数数量和内存消耗，还提高了模型的优化能力，尤其是在处理接近零值的特征时表现出色

## Conclusion

- 自校正通过random flip实现的吗
- 
