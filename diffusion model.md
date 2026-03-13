正向过程：
将给定的图片参考变成噪声。
学习：
如何将噪声还原到图片？
flow matching：
**Flow Matching（预测速度场）：** 直接给你一个指南针和一个速度计。它告诉你：“朝着东北方向（方向），以 120km/h 的速度开（速度）！”  
它直接在“沙漠（噪声）”和“家（目标图片）”之间，画了一条**笔直的高速公路**（这就是公式里 linear interpolate 线性插值的意义）

###  先拆解名词：什么是 ODE？

- **学术解释：** ODE 全称是 Ordinary Differential Equation（常微分方程）。在数学里，它专门用来描述一个东西**随时间连续变化的轨迹**。
    
- **大白话：** 你把它想象成一个**“极其精确的 GPS 导航轨迹”**。
    
    - 前面我们说了，生成图片是从“纯噪声（沙漠）”走到“清晰图片（目的地）”。
        
    - 在这个沙漠里，没有现成的路。模型预测的那个“方向和速度（Velocity Fields）”，就是指导你每一步怎么走的**规则**。
        
    - **ODE 求解（Solving ODE）：** 就是你根据这个规则，一步一步、严丝合缝地从沙漠走到了目的地，并在地上留下了一串脚印。**这串脚印组成的完整路径，就是 ODE 的解（Solution）。**

**KL 散度 (Kullback–Leibler Divergence)** 是一种衡量两个概率分布之间差异的指标。它回答的问题是：**如果真实分布是** P**，而我们用近似分布** Q **来表示，那么我们会损失多少信息？**![[image-54.png]]





# denoise diffusion model总体流程：
是一个去噪的过程，但越去噪噪声越少，我们希望模型在不同步骤能给出不同的处理，所以还需要把timestep来作为输入。![[image-55.png]]
内部如何去噪的？
会有一个noise predicter来预测噪声。既然是预测噪声，noise的gt是人为创造出来的。
![[image-56.png]]
- Forward Process
每一步都随机sample加入一个高斯噪声。![[image-58.png]]
现在加入文字提示：
![[image-59.png]]最终的完整流程：
![[image-60.png]]
 推理过程的框架：
 ![[image-61.png]]
 
 
# Evaluation
评价生成图片好坏  
1. FID:![[image-62.png]]
2. CLIP:![[image-63.png]]

Decoder的训练不需要文字图像pair，如果都只是图片就可以做：
比如中间产物【latent representation】是小图最终目的是生成大图，那么训练数据就要把大图变成小图。要得到中间的latent（相当于是小图但是人类无法看懂）还需要训一个autoencoder最后把decoder拿出来用。![[image-64.png]]

# 具体原理
## 训练过程：
【是latent加上同样维度的noise】
![[image-65.png]]

算法流程：
![[image-67.png| 噪声和原图混合程度是由t的采样决定 t越大 原图混合比例越小]]
![[image-68.png]]

 实际上一步加入噪声一步的noise

## 推理过程：
![[image-66.png]]
![[image-70.png]]



maximize likelihood=minimize KL divergence
做生成的时候每次选几率最大的输出不一定是最好的结果，所以需要sampling。
![[image-71.png]]这里每一步去噪都加入了noise