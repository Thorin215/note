# Visual Autoregressive Modeling: Scalable Image Generation via Next-Scale Prediction

## Background

### VQVAE

VQVAE 的生成示例, 根据 PixelCNN 输出的类别分布，我们可以采样出一些由离散值构成的压缩图像。这些离散值就和 NLP 里的文字一样，每一种值都有一种特殊的含义。我们可以认为离散值表示原始图像中一大块像素的颜色。借助图像压缩网络的解码器，我们可以把压缩图像复原成清晰的原始图像。

VQVAE 的训练顺序和生成顺序相反。我们先训练一个图像压缩网络。这种由编码器和解码器组成的图像压缩网络被称为自编码器，压缩出来的图像被称为隐图像（latent image）。训练好了自编码器后，我们再把训练集的所有图像都转成隐图像，让 PixelCNN 学习生成隐图像。比较有趣的是，训练 PixelCNN 时，只会用到编码器；而生成时，只会用到解码器



具体示例如下所示。编码器可以输出一个由任意向量构成的二维特征。通过查找嵌入层里的最近邻，这些任意的向量会被转换成整数，表示最近邻的索引。索引可以被认为是 NLP 里的词元 (token)，这样编码器输出特征就被转换成了词元构成的隐图像。而在将隐图像输入进解码器时，我们把嵌入层当成一张表格，利用隐图像里的索引，以查表的形式将隐图像转换成由嵌入构成的特征。准确来说，这个把图像压缩成离散隐图像的自编码器才被叫做 "VQVAE"，但有时我们也会用 VQVAE 代表整套两阶段生成方法。

![image-20250302162836294](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250302162836294.png)





### VQGAN

VQVAE 的效果并不理想，这是因为它的压缩网络和生成网络都不够强大。为此，VQGAN 工作同时改进了 VQVAE 的两个网络。

- VQGAN 工作将离散自编码器 VQVAE 换成了 VQGAN。在 VQVAE 的基础上，VQGAN 在训练时添加了感知误差和 GAN 误差，极大提升了自编码器的重建效果。

- VQGAN 工作还把生成模型从 PixelCNN 换成了 Transformer。

  

![image-20250302165531588](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250302165531588.png)

1）除了给每个词元加上一维位置编码外，同一尺度的词元还会加上同一个表示尺度序号的位置编码。所有位置编码都是可学习的，而不是预定义的正弦位置编码。2）Transformer 与解码器的共用嵌入层。另外，在生成新一层时，为了复用已经生成好的图像的信息，新一层的初始嵌入是通过对上一层的生成结果 bicubic 上采样得到的。

## VAR

原来 VQVAE 仅对最大尺度的特征做向量离散化，离散化后的误差会很大；而 VAR 把向量离散化引入的误差分散到多尺度离散化中，巧妙地降低了离散化的误差，提升了 VQVAE 的重建精度

![image-20250306150442800](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250306150442800.png)

![image-20250307144632923](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250307144632923.png)



![image-20250307144630226](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250307144630226.png)