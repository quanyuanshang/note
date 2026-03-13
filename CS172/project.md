这个 Project 的目标是：**在不依赖棋盘格（checkerboard）的前提下，实现机器人手眼标定（Hand–Eye Calibration），并评估和可视化结果**。整体任务从经典算法到现代数据驱动方法，包含实现、评估和拓展。以下是简洁清晰的总结：

---

## ✅ 这个 Project 主要要做什么？

### **🎯 核心目标**

实现 **AX = XB 的手眼标定问题**，在真实数据或由基础模型估计的相机运动（没有棋盘格）条件下求解手和眼之间的变换矩阵 X。

---

### **📌 基础任务（Basic）**

1. **理解和实现经典算法**
    
    - 学习 Tsai–Lenz (1989) 和 Park–Martin (dual quaternion, 1994) 等经典 AX = XB 求解方法。
    - 编写程序，从多组机器人与相机运动（Aᵢ, Bᵢ）计算变换 X。
    - 注意：Bᵢ 运动可能是无尺度（scale ambiguous），因为来自 COLMAP 或 3D Foundation Models（如 DUST3R、VGGT）。
        
2. **同时估计相机运动和手眼变换**
    
    - 将相机位姿估计 + 手眼标定联合起来处理。
    - 使用提供的 synthetic 数据进行实验。
    - 使用指标：
        - 旋转误差（角度，°）
        - 平移误差（厘米，cm）
            
3. **3D 可视化结果**
    
    - 展示标定得到的相机坐标系相对于机械臂坐标系的位置关系。
    - 可使用 Open3D，pytransform3d 或 Matplotlib 3D。
        

---

### **📌 高级任务（Advanced Options, 可选）**
1. **鲁棒估计**
    - 给机器人和相机的输入数据添加噪声。
    - 使用 RANSAC、M-Estimator 等方法提高抗噪性能和去除异常配对。    
2. **深度学习方法**
    - 使用或训练一个神经网络，直接从轨迹数据或 RGB-D 图像预测手眼变换 X。
        

---

## 🧠 数学表达：

手眼标定常被建模为求解以下矩阵方程：

**AX = XB**

其中：

| 符号  | 含义                                  |
| --- | ----------------------------------- |
| A   | 机器人末端执行器在两个不同姿态之间的变换（Hand movement） |
| B   | 摄像头在两个不同画面之间的变换（Eye movement）       |
| X   | 机器人末端执行器与摄像头之间的固定变换（要标定的量）          |
|     |                                     |

### **(1) What is feed-forward 3D reconstruction?**

理解 DUSt3R → VGGT 的演化：

- DUSt3R：点匹配 + point map
- MASt3R：多视图扩展
- VGGT：transformer-based, joint pose + depth estimation
    

你需要学的是：  
**它们怎么从两张图估计相机 pose（B_i）**

## Framework of Feed-forward Reconstruction
#### 1. 学习鲁棒的密集对应关系

- **目标**：在不同图像之间找到像素级的匹配关系（即哪个像素对应同一个场景点）。
    
- **方法**：
    - 使用 **编码器** 提取图像的深层特征。
    - 借助 **Transformer 的全局匹配模块**，通过自注意力和交叉注意力机制，在图像之间建立密集的像素对应关系。The self-attention layers enhance the contextual awareness of features within each image, allowing every pixel's feature to "see" the entire image.The cross-attention layers, in contrast, facilitate information exchange between tokens from different images, enabling the computation of pixel-wise matching relationships within a global receptive field.

> [!NOTE]
>     - 在传统的 SfM/MVS 流程里，匹配阶段通常输出的是**离散的匹配点对**，并且需要通过 RANSAC 等方法区分 **inlier（内点）/outlier（外点）**。这种方式虽然有效，但信息量有限，容易在复杂场景下失效。
>     - MASt3R [10] 与 VGGT [11]**目标**：提升在 **大基线差异**（wide-baseline）和 **分辨率变化**场景下的匹配精度。**方法**：采用更复杂的 **多尺度特征融合策略**，在不同分辨率层级上进行特征匹配。 **效果**：增强了跨尺度、跨视角的鲁棒性。    **意义**：相比传统的稀疏特征点（如 SIFT），这种方法能在纹理较少或视角差异大的场景中保持稳定。
    




#### 2. 联合推理几何与相机位姿

- **目标**：同时估计
    - **相机的相对位姿**（旋转与平移）。
    - **场景的三维几何结构**（深度图或点云）。
- **方法**:
    - 解码器将匹配结果输入，直接回归出相机位姿和场景几何。
    - 有些模型通过预测点云，再用可微分的对齐算法（如 Kabsch/Umeyama）计算相机位姿。The implementation in DUSt3R
    - - **预测点云**
	    - 模型输入第一张图像，输出每个像素在一个 **归一化相机坐标系** 下的三维坐标。
		- 这相当于直接生成一个稠密的点云。
        
	- **建立对应关系**
	    - 根据匹配阶段得到的像素对应关系，把第二张图像的像素投影到同一个坐标系。
        
	- **对齐点云（Pose-from-Alignment）**
	    - 使用 **可微分的 Kabsch 或 Umeyama 算法层**，计算两个点云之间的最佳刚性变换（旋转 + 平移）。
        - 这个变换就是相机的相对位姿 (R,t)(R, t)。
        
	- **互相监督**
	    - 预测的三维坐标直接提供场景几何信息。
	    - 点云对齐过程同时得到相机位姿。
	    - 几何和位姿互相约束、互相监督，提高整体精度和鲁棒性。
        
- **意义**：避免了传统流程中“先算位姿再算几何”的误差累积问题，实现端到端的一致性。
    

#### 3. 从双视图扩展到多视图

- **挑战**：两张图像的推理相对简单，但真实场景往往需要几十甚至上百张图像。
- **方法**：
    - 先在两视图之间建立对应关系，再通过图优化或点云融合扩展到多视图。
    - 新的研究尝试直接设计能处理 **N>2 张图像**的 Transformer 架构，实现真正的多视图端到端推理。
        
- **意义**：提升大规模场景的重建能力，减少逐对匹配带来的误差累积。

![[image-102.png]]

### **(2) Camera pose estimation部分**

重点是：

- feed-forward 模型如何匹配点对
- 如何通过 point map 得到 R,t
- 为什么不能得到 scale（非常关键）
    

这些知识刚好对应你项目的 Bᵢ。


# T算法
#### 2. 核心解法：旋转与平移分离 (Decoupling)

这是这篇论文最精彩的地方，也是你必须掌握的技巧。作者没有试图一下子解出 6 个自由度，而是分两步走。

**第一步：求解旋转 $R_{cg}$**

- **原理**：利用旋转轴的几何关系。
    
- **关键引理 (Lemma III)**：手眼变换的旋转轴 $P_{cg}$ 垂直于“机器人旋转轴 $P_{gij}$ 与相机旋转轴 $P_{cij}$ 的差向量” 10。
    
    - 公式：$P_{cg} \perp (P_{gij} - P_{cij})$ 11。
        
- **计算方法**：通过至少两组运动，构建线性方程组 $Skew(P_{gij} + P_{cij})P'_{cg} = P_{cij} - P_{gij}$ 来求解 12。
    
- **对你的启发**：这意味着你可以仅通过旋转信息就先确定相机的姿态，这在计算上非常稳定。
    

**第二步：求解平移 $T_{cg}$ (这是你项目中需要修改的地方！)**

- 公式：一旦求出了旋转 $R_{cg}$，就可以利用方程 (15) 求解平移 13：$$(R_{gij} - I)T_{cg} = R_{cg}T_{cij} - T_{gij}$$
- **深度解读**：
    
    - 左边：$(R_{gij} - I)$ 是系数矩阵。
    - $T_{cg}$ 是未知数（手眼平移向量）。
    - 右边：包含了相机的平移 $T_{cij}$ 和机器人的平移 $T_{gij}$。

> ⚠️ 重点预警（针对你的 Final Project）：
> 
> 在你的项目中，你使用的是无标定板方法。3D Foundation Models (如 DUST3R) 能够很好地估计相机的旋转 $R_{cij}$ 和平移方向，但无法确定真实的物理尺度。
> 
> 这意味着，方程右边的 $T_{cij}$ 是带有未知尺度因子 $s$ 的。
> 
> 经典 Tsai-Lenz 假设 $T_{cij}$ 是完美的真值（米/毫米）。而你将面对的是 $s \cdot T_{cij}^{model}$。你需要在这一步引入一个新的未知数 $s$，将方程稍作变形来同时求解 $T_{cg}$ 和 $s$。




这是一个非常核心且极其重要的问题！理解了 **"Up to Scale"**（尺度不确定性），你就理解了为什么你的 Final Project 会比传统的棋盘格标定更难，以及为什么你需要修改算法。

简单来说，**"Up to Scale" 的相机运动意味着：相机的旋转（方向）是完全正确的，但平移（距离）的单位是未知的。**
### 2. 数学解释

在你的 $AX=XB$ 方程中，相机运动 $B$ (从 $i$ 帧到 $j$ 帧) 由旋转矩阵 $R_{cij}$ 和平移向量 $T_{cij}$ 组成。
- **旋转 ($R_{cij}$)**：**是准确的。** 不管你是巨人还是蚂蚁，转头 90 度就是 90 度。旋转与尺度无关。
- **平移 ($T_{cij}$)**：**是“缩放”过的。**
    - 真实的物理平移是 $T_{real}$（单位：米）。
    - 算法（COLMAP/DUST3R）算出的平移是 $T_{est}$（单位：未知）。
    - 它们的关系是：
        
        $$T_{real} = s \cdot T_{est}$$
    - 这里的 **$s$** 就是那个未知的 **尺度因子 (Scale Factor)**。
        

### 3. 对你项目 ($AX=XB$) 的致命影响

在你的项目文档中明确提到：_"$B_i$ is ambiguous at scale (estimated from foundation models or COLMAP)"_ 1。

传统的 Tsai-Lenz 算法步骤是：

1. 利用旋转求解 $R_{cg}$（这一步没问题，因为旋转不受尺度影响）。
    
2. 利用方程 $(R_{gij} - I)T_{cg} = R_{cg}T_{cij} - T_{gij}$ 求解平移 $T_{cg}$。
    

问题来了：

在这个方程右边，$T_{gij}$ 是机器人告诉你的，单位是米（真实世界尺度）。而 $T_{cij}$ 是算法（Dust）算出来的，单位是未知单位（虚拟世界尺度）。

你不能把“米”和“未知单位”直接相减！这就像用“3米”减去“2个苹果”，没有数学意义。





# 代码实操
1. 相机的位置是通过把图片给到Dust3r这个模型吐出的。
2. 目前有gemini给到的手写T算法实现和OpenCV上包装好的算法实现，但是不知道为什么OPenCV的这种方法更差了。

- [x] 去看github上对应的论文。
- [ ] 了解项目代码结构

看到比较重要的文件：
`fix_scale_calib.py`
`cam_calibration.py is a tool to caliberate camera to get focal lengths, cx, and cy.带标定格子
目前项目主要靠 utils/scale_calib.py（由 DUSt3R + 机械臂观测驱动）得到 R 和 t，JCR_run.py 把该解结果用于生成最终的相机/点云对齐输出。
scale_calib_helper：calibrate 在解经典的 AX = XB 问题。

  
compute_arm 里直接对尺度做暴力搜索：

1. 复制一份相机位姿 `w2c_poses_selected_scale_cpy`，根据是否为 COLMAP 模式，在 `[0.01, 5]` 或 `[0.01, 10]` 区间均匀取 200000 个尺度候选。@utils/scale_calib.py#14-21
2. 对每个尺度，调用 compute_A_B_Rx 生成 AX=XB 的系数，再把缩放后的位姿送进 compute_cost 计算总代价 `J_res` 与平移 `tx_res`，把所有代价保存。@utils/scale_calib.py#22-31
3. 选出代价最小的尺度 `scale = vals_sc[argmin(J_res)]`，之后用该尺度重算 A、B，并继续解旋转/平移。@utils/scale_calib.py#32-41


指令：python visualize_scene.py --exp-name 7obj_4cluster --num-imgs 10 --run-calibration --interactive
python visualize_scene.py --exp-nam4obj_measureer --num-img8 --show-raw-poses

遇到了代码报错说没有computevisual那个函数



- AB，一组对应的噪点。看下误差大不大。



`poses_trans = torch.matmul(T.float().unsqueeze(0), poses)` 将标定得到的刚体变换 `T` 左乘到每个 Dust3R 输出的相机位姿上，从而把所有相机位姿从 Dust3R 坐标系一次性变换到末端执行器/世界坐标系。


- 经过 Dust3R 获得的 `poses` 原本描述的是“相机在 Dust3R 坐标系中的位姿”。
- 手眼标定得到的 `T` 描述 Dust3R 坐标系相对于末端执行器坐标系的刚体变换（R|t）。
- 左乘 `T` 后得到的 `poses_trans[i] = T · poses[i]`，可解释为“先把点从相机坐标系转换到 Dust3R，再通过 `T` 转到末端执行器坐标系”。
- 因为齐次矩阵乘法遵循坐标系变换的链式法则，`T` 把整个 Dust3R 坐标系搬到末端执行器坐标系，所以相机位姿也随之表达为“末端执行器坐标系中的相机位姿”。

因此转换后的矩阵具有“相机位姿对齐到末端执行器坐标系”的含义，原因在于该乘法应用了手眼标定求得的坐标系对齐变换。@visualize_scene.py#80-106


python visualize_scene_noise.py --exp-name extra_eg --num-imgs 10 --run-calibration
python visualize_scene_noise.py --exp-name extra_eg --num-imgs 10 --run-calibration --compare-methods