# Streaming Dense Video Captioning

## Background

- 提出了一种新的记忆模块，基于对传入令牌的聚类，该模块能够处理任意长度的视频，因为记忆的大小是固定的。
- 开发了一种流式解码算法，使模型能够在处理完整个视频之前做出预测。 实现了这种流式处理能力，并在三个密集视频字幕生成基准测试中显著提高了最先进水平。
  

![image-20250302145033509](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250302145033509.png)



![image-20250302144917100](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250302144917100.png)

- Clustering不是把前面的stream features融入了当前聚类，为什么还需要显式记忆？
- 此外，我们将早期解码点的预测作为后来解码点的上下文。这一上下文避免了预测重复的事件，并可以作为自然语言总结早期视频的“显式”记忆。

![image-20250302145146781](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250302145146781.png)