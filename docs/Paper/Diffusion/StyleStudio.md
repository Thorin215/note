# StyleStudio: Text-Driven Style Transfer with Selective Control of Style Elements

## Method

![image-20250305154550682](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250305154550682.png)

1. **跨模态的AdaIN机制**：通过自适应实例归一化（AdaIN）将风格和文本特征更好地融合，提升生成图像的对齐效果。
2. **基于风格的分类器自由引导（SCFG）**：通过生成一个“负样本”图像，帮助模型专注于目标风格元素，减少无关风格的影响。
3. **教师模型**：在生成的早期阶段引入教师模型，稳定空间布局，减少伪影。