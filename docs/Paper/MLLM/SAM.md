# Segment Anything

## Background

基础模型也在计算机视觉中得到了探索，尽管程度较小。最突出的illustration是将网上的文本和图像对齐。例如，CLIP和ALIGN 使用对比性学习来训练文本和图像编码器来对齐这两种模式。一旦经过训练，工程文本提示可以使zero shot泛化到新的视觉概念和数据分布。这些编码器还可以有效地与其他模块进行组合，以实现下游任务，如图像生成(如DALL·E）。

> 任务的设计灵感来自于NLP领域，例如NLP中可以通过预测next token作为预训练任务，而在下游任务中可以使用prompt engineering做应用。因此，为了建立分割的基础模型，任务的设计目标是也需要具有类似的能力。 这里作者扩展了下NLP里prompt在图像分割里的用法， prompt可以是以下几种类型：
>
> - point
> - box
> - mask
> - 任意格式的文本

## Pipeline

![image-20250225111505136](./SAM.assets/image-20250225111505136.png)

- image encoder旨在映射待分割的图像到图像特征空间
- prompt encoder则是负责映射输入的prompt到prompt的特征空间，这里有一点要提就是作者定义了sparse和dense两种prompt，其中sparse prompt比较好理解，就是指demo中我们可以输入的点，目标框或者是描述目标的text，而dense prompt在目前的线上demo中体验不到，paper中也只说它对应的是mask类型的prompt，从代码里看应该是训练时候用的比较多，一般是上一次迭代预测出的一个粗分割的mask，粗略指出待分割的目标区域。
- mask decoder的意义从功能上说有两个，一是整合image encoder和prompt encoder分别输出的两个embedding，然后从这个embedding的feature map解码出最终的分割mask。
