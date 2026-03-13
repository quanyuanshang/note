![[Pasted image 20251013151033.png]]
# Camera
## Pinhole camera
![[image-100.png]]
- Projection
1. Points are projected to points 
2. Lines are projected to lines 
3. Length is not preserved  • The closer, the larger 
4. Angle is not preserved 
	• Parallel lines “intersect” 
	• Vanishing points
linear albebra：$P^2=P$
![[Pasted image 20250917160816.png]]

- vanishing point
1.  All parallel lines converge to a vanishing point
2. Each direction in space is associated with its own vanishing point 
3. Exception: directions parallel to the image plane


![[Pasted image 20250922152803.png]]
## Depth of Field
为什么光圈（Aperture）越大景深越小？
![[image-99.png]]
> 小光圈阻挡了会引起circle of confusion的光。
> - **光圈大（f 值小）**：
    
    - 光线束更粗，离焦时弥散圆直径更大。
        
    - 导致浅景深，背景容易虚化。
        
- **光圈小（f 值大）**：
    
    - 光线束更细，离焦时弥散圆直径更小。
        
    - 增加景深，更多前后景物看起来清晰。
> ![[Pasted image 20250922152744.png]]
> - 当光线通过镜头后，如果没有在焦平面上完全聚焦，点光源会在成像平面上形成一个圆形斑点，这个斑点的直径就是弥散圆（circle of confusion）。
    
- **作用**：弥散圆大小决定了画面是否被认为“清晰”。如果圆的直径小于人眼分辨能力，就仍然被视为清晰；超过这个范围就会显得模糊。
    
- **临界点**：弥散圆就是“清晰”和“模糊”之间的分界线。
![[image-101.png]]
> 但同时光圈变小减少了进光量可能就需要更长的曝光时间

##  Field of View
FOV（Field of View，视场角）是指光学系统或成像设备能够捕捉到的场景范围，用角度来表示。它决定了镜头或相机能看到多少画面：**FOV越大，视野越宽；FOV越小，视野越窄但放大效果更强**
**镜头焦距**：焦距越短 → FOV越大（广角镜头）；焦距越长 → FOV越小（长焦镜头）
![[Pasted image 20250922153834.png]]

## real camera
按快门（Shutter）的时间->曝光时间
lens flaw:
- vignetting: 多个透镜导致边缘的光打不进去![[Pasted image 20250922154325.png]]
- radial distortion
- Chromatic Aberration：透镜对不同波长的颜色投射不同，形成重影效果

# Light and Shading
## Light Transport
斜着打过去，到平面上的间距会变大，导致单位面积上的光子变少。
![[Pasted image 20250922161228.png]]
### Fundamental Radiometric Relation
一个球面部分向外发射光源。
L：从某一点散发出来的能量（单位：瓦特球面单位方向）
E：接收到的能量。（因为是平面所依单位没有球面定义）
![[Pasted image 20250922161711.png]]
关系公式：![[Pasted image 20250922162305.png]]
- 越接近边缘的地方能量越少
- 和镜片面积成正比
### From Light Rays to Pixel Values：
![[Pasted image 20250922162926.png]]
Image Sensing Pipeline：
![[Pasted image 20250922163342.png]]
模数信号转换之后得到的是raw


## Reflectance and Shading
## Common effects
![[Pasted image 20250922164029.png]]
## Photometric Stereo：2d->3d
> 假设：
> 1. “A Lambertian object” : 一种表面具有**理想漫反射特性**的物体，它在任何观察角度下都以**相同的亮度**反射光线。这种物体遵循**朗伯定律（Lambert’s Law）**
> 2. A local shading model : Each point on a surface receives light only from sources visible at that point. eg: **ignore shadow\indirect illumination\reflection\interreflection（互反射）**
> 3. 假设每个像素都没有被遮挡
> 4. A set of known light source directions 
> 5. A set of pictures of an object, obtained in exactly the same camera/object configuration but using different sources
> 6. Orthographic camera model（理想化的成像模型）：所有光线都是**平行地投射到图像平面上**。- 不考虑透视变形（即远处物体不会变小）。相机没有焦距的概念，图像中的位置直接对应物体在空间中的位置。



**Goal**: reconstruct object shape and albedo
- albedo反照率：表示光线漫反射的比率。这是一个常数。Albedo = 反射光的强度 ÷ 入射光的强度
![[Pasted image 20250924164607.png]]
最终效果：
![[Pasted image 20250924174445.png]]
- S:光源向量
- I：对应光源下的像素
![[Pasted image 20250924162921.png]]
原理是LAmbert：
![[Pasted image 20250924162155.png]]
> 点积 S⋅N(x,y)$$ \mathbf{S} \cdot \mathbf{N}(x, y) $$ 表示光线与表面法线之间的夹角的余弦值（即光照的“有效性”）。

1. **Least squares problem:**
![[Pasted image 20250924184249.png]]

$$
Ax=b\ \ \ x=min(Ax-b) 这里的x就是g(x,y)
$$
$$g(x,y)=\rho(x,y).N(x,y) \ \ \ N(x,y)是g(x,y)的倍数$$2. Recovering a Surface from Normals
设置每个像素的深度为f(x,y)
tangent plane:切平面
切平面的法向量是有上面两个向量做叉乘
法向量做一下归一化确保模长是1
![[Pasted image 20250924185322.png]]
![[Pasted image 20250924185532.png]]
## Finding the Direction of the Light Source
![[Pasted image 20250924190350.png]]
平行于照相平面那一圈的法向量都是0
化简：![[Pasted image 20250924190441.png]]


# Color
##  Spectrum
可见光波段：400-700nm

波长发生改变：荧光（fluorescence）
光被反射的时间不同：磷光
Spectroradiometer:分光辐射计
![[Pasted image 20250929151948.png]]
> 不同波长光折射不同，之后再用凸透镜进行聚焦，移动小孔得到不同波长的光。这种技术可以用于测试溶液对不用波长光的反应。

接收到的光是由直射光和反射光等的共同作用：
![[Pasted image 20250929152954.png]]


## Metamer
相似的颜色可由不同波长的颜色拼成。但是这取决于在某种光源下有这种效果。![[Pasted image 20250929160140.png]]

## Color Space
- XYZ  Color Space

![[Pasted image 20250929160937.png]]
> 每一行和都是1，确保Y只和RGB有关，当三者相等那么就是一样的。将Y轴映射到明暗度。线性映射。

contour是可见光波长变化。



- HSV Color Space![[Pasted image 20250929161203.png]]
> 要想更改颜色直接改Hue就可以，相比RGB需要同时调节三原色更方便。

## Color Sensing

间隔得填充R、G、B sensor.
因为人对绿光最敏感，所以绿色传感器会更多。
对每个位置还要根据相邻传感器的值进行估计（图中是小写）![[Pasted image 20250929162515.png]]


# Image Filtering
- Types of images:
1. binary{0,1}
2. gray[0,255] 8个bit 1个byte
3. collor[0,255]\*[0,255]\*[0,255]3个byte uint8


Average：Let’s replace each pixel with a weighted average of its neighborhood using **filter kernel**.
> Convolution：卷积核上下左右翻转之后![[Pasted image 20251011153054.png]]
> - 性质：
> 	1. Shift invariance：filter(shift(f)) = shift(filter(f))
> 	2. Lineartity：$filter(f_1 + f_2) = filter(f_1) + filter(f_2)$ 
- dealing with edges:
1. zero pad边缘变黑
2. wrap around最合理的，因为和离散上的傅里叶变换相似
3. copy edge
4. refelct across edge

### Filtering
1. Sharpening ：![[Pasted image 20251011160910.png]]detail保留了边缘（颜色变化剧烈的区域）
2. Smoothing with Box Filter会有缺点：留下了矩形的artifacts
- 解决方法：weight contribution of neighborhood pixels according to their closeness to the center。
- **Gaussian Kernel:距离越近的权重越大**![[Pasted image 20251011161429.png]]
- $\sigma$ 是一个可以调节的参数，越小，这个时候就近似于中心是1的那个kernel。越大就接近于平均的kernel。
- 还有可以调节核的size，![[Pasted image 20251011161900.png]]size越大，越接近平均。一般选择 $3\sigma$ 为kernel的width。框如尽可能多靠近中心的。反之，确定了width，确定$\sigma$ 就width乘上3就可以。
- 低通滤波器 low-pass
- Convolution with self is another Gaussian： Convolving two times with Gaussian kernel with std. dev. 𝜎 is same as convolving once with kernel with std. dev. 2$\sigma$
- Separable kernel: 降低计算量，2d卷积直接做计算复杂度$O(n^2m^2)$=1d卷积做两次$O(2.n^2m)$![[Pasted image 20251011162726.png]]

### Image Denoising
![[Pasted image 20251011163318.png]]
**Gaussian noise:** 噪声值在统计上呈现出一种特定的分布模式——**正态分布**，也叫**高斯分布**
**Solution：** 高斯核，但也同时模糊了原图

**Saltpepper:** 极端的噪声值没有被影响很多反而影响到了周围的正常值。
**Solution：** **median filtering** 取周围的中位值，这样就能去掉极端值（outlier）。更加鲁棒的结果。


# Edge
## Edge detection
 - 定义edge：An edge is a place of rapid change in the image intensity function。![[Pasted image 20251013152019.png]]
> 这里值得注意的就是这个一阶导数的变化。一阶导数到极值点（不是0）的时候二阶导数是0。一个比较好理解的方法是确认周围的点，自己局部最优。

- 用卷积计算一阶导数：
- ![[Pasted image 20251013153039.png]]

0 -1 1
对x求导

---
 1 
 -1 
对y求导

![[Pasted image 20251013153249.png]]
> ![[Pasted image 20251013155441.png]]对竖直方向和垂直方向求导后的点这样做

- Effects of Noise:![[Pasted image 20251013153413.png]]
	- 这里就是找不到噪声。先去噪。
	- 先用filtering kernel去噪声，再求导：![[Pasted image 20251013153635.png]]这里把高斯去噪和求导合起来。
	- 和smoothing filter的区别： smoothing没有负数，kernel的值加起来是1。但是derivative filter（Derivatives of Gaussian）是有负数的，和加起来是0.
	- Sobel Operator：![[Pasted image 20251013160142.png]]这里前面的那个求导矩阵式$\frac{f(x+a)-f(x-a)}{2a}$
	- Non-Maximum Suppression:![[Pasted image 20251013160728.png]]沿着梯度的方向不断选取较大值直到最后。![[Pasted image 20251013160844.png]]最后得到的边缘会变细但是会比较清晰。


## Texture
Describe what kind of pattern should be detected。
![[Pasted image 20251013161442.png|412x203]]找水平、斜方向、竖直方向、、、的线条
包括了一些语义信息，线条模式，每个核在告诉我们图像像什么东西。相应比较高的，那么就会是白色。
> 通过调节kernel的方向，尺寸，产生不同通道来描述图片。图例中有八个


# Recognition

challenge:
- background
- illumination
- occlusion 遮挡

## Tradition method
![[image-3.png]]
- Bags of words
特征提取
使用kmeans选取最有代表性的图像块
## K-means
损失函数：![[image.png]]
这个损失函数优化过程中，损失一直会下降，每次都会把点分配给最近的邻居

再做统计。![[image-1.png]]
问题：特征是没有顺序的。图片各个地方的尺寸是存在位置信息的。
解决：Spatial Pyramids![[image-2.png]]空间上不断细分下去，逐一比较是否相似。
## K-Nearest Neighbor Classifier
no-training+Distance function
时间复杂度 O(n)。inference：O(n)必须把测试集都过一遍。
**缺点**：slow at test time
如何选择超参数（k和distance
 metric）：
用训练集来选择超参数会存在一个数据泄露。
最合理的办法是：
训练数据划出来一部分作为validation set，用validation set来调整超参数，再在test上检测。
#crossvalidation
![[image-4.png]]坏处就是慢，训练时间直接多了四倍。深度学习就会用的比较少了。

issue with pixel distance：维度灾难。

# Linear Classifer
$$
f(x,X)=Wx+b
$$
![[image-5.png]]
> 从卷积的角度理解：要学的W相当于一个卷积模版，这是在计算那个模版和测试图片最像。

核心：如何求解W？
目标：定义一个loss function，找到W能够最小化loss function。
### SVM
最近距离最大化
![[image-6.png]]
目标：![[image-7.png]]
对于不可线性划分的数据点，如果能正确分类max取到的就是0，
Soft margin:![[image-8.png]]![[image-9.png|式子转换一下，与y（wx-b）>=1相比条件宽松]]
最后用拉格朗日得到权重就是支撑向量的线性组合：![[image-10.png]]
提升到高维空间，定义好这种点乘，变成一个可分的空间。
![[image-11.png]]
- Polynomial Kernel：![[image-12.png]]
- Gaussian Kernel：![[image-13.png]]


# CNN
问题：线性分类器没有考虑到图像的空间信息。
## Conv Layer
![[image-14.png|N是batch大小，一个batch有多少张图片]]
一直叠加卷积提升到高维无意义（线性）![[image-15.png]]
加入非线性激活函数。
关于padding：![[image-16.png]]![[image-118.png]]
peceptive field：每次往前扩充都会扩充k-1。再加上自己原本的就是再加1
![[image-18.png| 最后output一个点包含了全图信息]]
计算：![[image-19.png]]
这里能学到的参数个数：10\*3\*5\*5+10
1*1 conv可以用于降维，从而降低计算量和参数量。


##  Pooling
![[image-20.png]]
- shift invariance的矛盾：![[image-21.png| 因为边缘上的1padding影响了神经网络的判断]]
## batch normalization：
变成0均值1方差的目的：在经过Wx+b之后能确保输出的结果也能符合0均值...，否则均值可能会越来越大。归一化能控制住数值范围。![[image-22.png| uj是j通道的均值，最后epsilon是防止分母变成0（每个样本都是一样的）]]
- 测试时候：![[image-27.png]]
![[image-23.png]]
通常bn层是在卷积之后，激活层之前做的。加入之后学习速度也会更快，收敛更快，在比较深的网络里是必备的。
对于bn来说batch size的影响：小的N会让均值差别较大。
对于layer normalization来说就不存在这个问题。问题是容易受个别特征影响。
instance normalization：分成最细![[image-24.png]]
![[image-26.png]]
案例：
### VGG：
使用3次3\*3，等价于一个7\*7的perceptive field,但由于多次叠加能引入更多非线性。同时参数量也可以变小。![[image-28.png]]
### GoogleNet：
inception module：
平行做多个层，再concatenate。问题是计算量太大了![[image-30.png]]
Solution：加入1*1卷积![[image-31.png]]
总体图片：![[image-32.png| stem是在做初识的位置编码卷积Pooling之后在接上inception module]]
最后在输入FClayer之前，做一个average pooling（H * W）， H * W * C最后变成1*1*C，相当于每个通道都取一个平均。同样这种操作被应用在中间，监督中间的特征。
网络过于深，后向传播有点优化不动了。
作用一：可以把他看做inception网络中的一个小细节，它确保了即便是隐藏单元和中间层也参与了特征计算，他们也能预测图片的类别，他在inception网络中起到一种调整的效果，并且能防止网络发生过拟合。
作用二：给定深度相对较大的网络，有效传播梯度反向通过所有层的能力是一个问题。通过将辅助分类器添加到这些中间层，可以期望较低阶段分类器的判别力。在训练期间，它们的损失以折扣权重（辅助分类器损失的权重是0.3）加到网络的整个损失上。
————————————————
测试不需要，train需要。

![[image-33.png]]
### ResNet
想法来源：为什么更深的网络效果会不如较浅？
![[image-34.png]]
# RNN
![[image-35.png|环状的就是hidden]]
![[image-37.png]]
是否忘却，中间有个sigma来取0或者1，ht-1是上一世参数![[image-36.png]]
![[image-38.png|有个激活函数，但还是要控制是否写入]]
C相当于来自过去的记忆，通过hidden state和现在的记忆来控制是否写入，从而达成长期记忆。
![[image-39.png | ]]![[image-40.png|输出]]


# Image Segmentation
bottom up：把东西分的很细，然后再拼成想要的力度。从下往上做。
top down：大到细
下面两种方法都是bottom up
- Superpixel：超像素，组合了一些特征上比较接近的，把图像分解成小块组成的。
	- 相比于pixel的优势：
	1. carry more information
	2. align better with image edges
	3. speed up subsequent processing
	4. reduce redundancy and simplify recognition tasks
	- 如何生成superpixel？
	- ## Felzenszwalb’s Algorithm
		- 把图变成N4Graph![[image-41.png | 边的值是像素差。]]
		- 希望找出一个图上面的分割使得分割到的两个superpixel差距最大。
		- 希望满足：组内两点距离最大值要比两个组间距离最小值来的小。![[image-42.png |Int（C）是类内最大距离， |C|是类内点个数]]![[image-43.png]]
		- D是false就进行合并，一直合并直到没办法合并![[image-44.png|k越小，分的越细致]]
	- kmeans：因为目标函数是单调递减的，所以最终一定会收敛。
	- SLIC：这种方法不会出现隔空跳成一类。![[image-45.png | 同时考虑颜色距离和空间上的间距，S是撒点的间距，m来控制更侧重于颜色还是空间信息。]]
- ![[image-91.png]]![[image-46.png]]


## Mean Shift Algorithm
![[image-92.png]]
中心一直往密度高的地方挪动。
希望找到图片中的mode（local maximum）![[image-47.png|m里面可以把K（）看成是权重，用uniform1 m就是直接算均值]]
![[image-48.png|直到最后的mean点和样本点重合]]


当然可以！我们用一个简单的二维例子来说明 Mean Shift 聚类是如何使用高斯核工作的。

---

### 🧠 Mean Shift 聚类 + 高斯核示例

假设我们有一组二维点如下：

```
[(1, 2), (2, 2), (2, 3), (8, 8), (9, 8), (9, 9)]
```

这些点明显分成两个区域：一个在左下角，一个在右上角。

---

### 🌀 步骤解释（使用高斯核）

1. **选择核函数和带宽**
    
    - 使用 **高斯核**：权重由距离决定，距离越近，权重越高。
    - 设定带宽 ( h = 2 )：控制搜索窗口的大小。
2. **对每个点执行以下操作：**
    
    - 以该点为中心，找到在半径 ( h ) 范围内的邻居点。
    - 使用高斯核计算这些邻居点的加权平均（即“均值”）。
        - 高斯权重公式：  
            $$ w(x) = \exp\left(-\frac{|x - x_i|^2}{2h^2}\right) $$
    - 将窗口中心移动到这个加权平均位置。
    - 重复直到窗口位置不再显著变化（即收敛）。
3. **聚类**
    
    - 所有收敛到同一个“模式”（密度峰值）的点被分为同一类。

---

### 📌 举个具体例子

以点 (2, 2) 为例：

- 邻居点：[(1, 2), (2, 2), (2, 3)]
- 计算这些点到 (2, 2) 的距离并用高斯核加权：
    - (1,2) 距离为 1 → 权重约为 0.88
    - (2,2) 距离为 0 → 权重为 1
    - (2,3) 距离为 1 → 权重约为 0.88
- 加权平均位置约为： [ \frac{0.88 \cdot (1,2) + 1 \cdot (2,2) + 0.88 \cdot (2,3)}{0.88 + 1 + 0.88} \approx (1.65, 2.35) ]
- 将窗口中心移到 (1.65, 2.35)，继续迭代直到收敛。

最终，(1,2), (2,2), (2,3) 会收敛到一个密度峰值，形成一个簇；而 (8,8), (9,8), (9,9) 会收敛到另一个峰值，形成另一个簇。


优缺点：![[image-49.png]]
> 为什么robust？因为每次只看半径内的点。不像kmeans一样需要每一个点。        


分割问题什么是最重要的？
- feature space：把什么定义成distance。

## Fully convolutional network (FCN)
神经网络分割的输出尺寸：
1. 类别不互斥：h*w*c（c个类别通道，是就是1）![[image-51.png]]
2. 互斥：![[image-50.png]]


![[image-52.png]]将fc替换成卷积改变维度。
恢复维度的时候，贴上前面的特征，再卷积，再上采样。最后输出对每个像素做1*1卷积输出分数![[image-53.png]]

# Image Retrieval 图像检索
- Description-based image retrieval (DBIR) : Image meta search: search of images based on associated metadata such as keywords, text, etc用关键词检索。
> 对图片做文字层面的标注。
- Content-based image retrieval (CBIR): the application of computer vision to the image retrieval.根据图片内容（纹理颜色形状）
	- 传统方法：
		1. color histograms：![[image-54.png]]
		2. Texture：![[image-55.png]]
		3. Region-Based Image Retrieval：聚焦于关注的区域
	- Deep-Feature-Based retrieval：
		- 核心是学到一个good embedding space，让instance map到这个space之后能有明显距离区分。
		- Triplet loss：目标是让negative到样本的距离更大。并且减去正样本距离大于一个阈值。之后取max（x，0）这样再大也不会进行优化，不够大就会优化。![[image-56.png]]
		- constrastive：![[image-57.png|pull是希望距离靠近，push是希望两个的距离尽可能大]]
案例：SimCLR：这里的encoder用的是resnet50![[image-58.png]]
![[image-93.png]]![[image-59.png]]图片不需要标签直接放进encoder，学到特征。这些特征可以做一些下游任务比如分类、分割。
![[image-60.png]]
最后做Softmax：![[image-61.png]]
如何对齐文字和图像？
文字也提取特征。
这张图展现的就是将问题和图片特征显示在坐标里面，距离最近的就是最有可能的分类。
![[image-63.png|这样的话可以不需要标签进行分类]]
现有工作：CLIP![[image-62.png | 训练目标是拉进图片和目标距离，但实际上可以直接做分类问题。]]


# fitting
## Least squares line fitting

![[image-64.png]]

![[image-94.png]]
## Total least squares
先求d，带入求导的d之后相当于归一化把直线移动到原点了。
再对ab对应的矩阵N求导。
$U^TU$是实对称矩阵一定能做特征值分解。这里就是把N看成是对应特征值为0的那个解。（为什么特征值为0是最小的，因为这是个半正定矩阵对应的特征值一定大于等于0）
![[image-65.png]]
![[image-95.png]]

特征值求解+回代入解方程![[image-96.png]]
![[image-98.png]]
从几何角度理解就是在N这个方向上方差最小（也就是特征值最小）![[image-66.png]]


防止outlier：
## Robust fitting：
当outlier数量不是那么多
通过设置上线：损失函数最多为1，从内向外，sigma越来越大。
![[image-67.png]]


sigma太小：相当于没有优化因为损失函数基本上都是1![[image-68.png]]
sigma太大![[image-69.png]]




## RANSAC：
能处理好大量噪声。
Random sample consensus (RANSAC): Very general framework for model fitting in the presence of outliers
先随机采样几个点来拟合一条直线，再看剩下的点是否同意这条直线？
![[image-71.png]]
红框公式：N次采样，每次至少有一个outlier的概率。
![[image-72.png]]

一开始不知道outlier比例，e。之后调整。一旦inlier有最大数更新，就调节e，再重新计算N。![[image-73.png]]


## Hough Transform
voting：
### Linear Hough Transform

![[image-74.png]]
线点对应。![[image-75.png]]

用mb来表示直线的局限性：
1. 参数没有范围
2. 对于竖直线m是无穷大。

用极坐标表示。
$$\rho = x \cos \theta + y \sin \theta$$

- ρ\rho：直线到原点的 **距离**
    
- θ\theta：直线法线与 x 轴的 **夹角**
目标是由离散的点去拟合直线。

![[image-76.png | ]]
步骤：
![[image-77.png]]

映射到houghspace就是正弦函数，选取上面的交点来拟合。

如何处理噪声：
Choose a good grid / discretization 
• Too coarse: large votes obtained when too many different lines correspond to a single bucket 
• Too fine: miss lines because some points that are not exactly collinear cast votes for different buckets
• Increment neighboring bins (smoothing in accumulator array）比如用高斯噪声去噪


### Circle Hough Transform

![[image-78.png]]
圆一共有三个参数，固定R，一个点在hough空间上对应的也是圆.$ (a-x)^2+(b-y)^2=R^2 $

### Generalized Hough Transform

Template representation: for each type of landmark point, store all possible displacement vectors towards the center![[image-79.png]]
![[image-80.png]]
通过数据单词（visioncode）的位移向量来确定物体具体位置。
Implicit shape model具体train步骤：
1. ：Build codebook of patches around extracted interest points using clustering (more on this later in the course)在图片中寻找小的模块，做聚类。![[image-83.png]]
2. Map the patch around each interest point to closest codebook entry![[image-82.png]]
3. For each codebook entry, store all positions it was found, relative to object center![[image-81.png|找displacement vector]]
测试：
 4. Given test image, extract patches, match to codebook entry 
 5. Cast votes for possible positions of object center 
 6. Search for maxima in voting space 
 7. Extract weighted segmentation mask based on stored masks for the codebook occurrences![[image-84.png]]





# Camera Calibration
必须确定其中两个参数才能推出第三个
 确定相机参数
![[image-85.png|算出相机应该在哪里]]
二维重建三维![[image-86.png]]
ill-posed欠表达的推测。![[image-87.png]]![[image-88.png]]

## 如何标定相机
相机参数：
1. 焦距：焦距比较小，能看到比较广的东西。
知道三维点坐标和相机焦距就能知道二维成像上的坐标。![[image-89.png]]
### 齐次坐标
齐次坐标是不受放缩影响的。
第一个方程比较垂直关系
第二个方程的叉乘能表示同时与这两个向量垂直。
![[image-90.png | 也可以理解成：如果把abc看成三维坐标的点，知道经过它的两条射线，如何计算三维上的点是啥]]
用齐次坐标表达无穷远的点和直线：![[image-103.png|两条线的参数做叉乘算出交点。]]


Projection matrix：
注意这是在相机坐标系下：三维空间映射到二维成像平面：![[image-104.png]]
x和y方向上单位像素对应的毫米数可能不一样。如果物理长度一样但是x方向上像素单位长度较大，此时fx就会偏小。s会让y方向对x投影产生影响，skew代表一种扭曲程度。x0，y0代表中心从0,0偏移![[image-105.png]]![[image-106.png|内参共有5自由度]]
将世界坐标系坐标表达为相机坐标系的坐标：![[image-107.png]]
先平移后旋转。
旋转矩阵：![[image-108.png|欧拉角的表示 三维旋转，三个自由度]]
旋转矩阵正交，逆就是转置，行列式还要是1【右手系】（否则还可能是-1【左手系】）![[image-109.png]]![[image-110.png]]

camera pose和extrinsic matrix是反过来的![[image-111.png]]
如何计算逆？
![[image-112.png]]
## 相机标定总体公式：
在已知2D-3D对的情况下进行相机标定：![[image-113.png]]
总共有11自由度5 3 3
一个pair能提供两个等式，一共11个自由度，至少需要6对。虽然有12个参数但是因为P是齐次的所以scale不影响可以直接除去一个m，所以实际上自由度是11.
![[image-114.png]]
![[image-115.png]]
让上述左边的矩阵尽可能小。![[image-119.png | ]]
所以取最小值的时候ATA就是最小的特征值。x就是对应最小特征值对应的特征向量。**第二部分：**−λ(xTx−1)-\lambda (x^T x - 1)

- 这是一个 **约束项**。
    
- xTx=∥x∥2x^T x = \|x\|^2，即向量的平方长度。
    
- 减去 1 表示我们希望约束条件是 ∥x∥2=1\|x\|^2 = 1，即 xx 是单位向量。
    
- λ\lambda 是拉格朗日乘子，用来强制这个约束。

![[image-120.png]]
## 问题：
最小化的是algebra error，但不是最小化投影误差
拿到的是直接的矩阵。DLT 的结果是一个整体的投影矩阵，但没有直接分解成相机的内参（焦距、主点、畸变等）和外参（旋转、平移）。后续还需要额外步骤（如 QR 分解）。比如我们已知焦距或主点位置，DLT 不能直接把这些先验条件加入求解。它只能给出一个通用解。
## QR Decomposition
![[image-121.png]]   
投影过程存在非线性的变换：![[image-125.png]]
LM方法：
![[image-126.png]]![[image-127.png]]
![[image-128.png]]
![[image-122.png]]![[image-123.png]]中间那个sigma在调整样本权重。
![[image-124.png]]
lamba加10：现在参数在前面的local minimum，想要脱离这里就迈一大步往后走。反之精细化学习率
# Single-view
在没有2D-3D对情况下，使用消失点进行相机标定
  ![[image-129.png]]
  ![[image-143.png]]
  ![[image-130.png]]
按照上述公式，X轴方向上消失点坐标是（1,0,0,0）。由于齐次坐标的性质，图像点只确定了方向，**无法确定绝对比例**。也就是说：![[image-144.png]]为了唯一确定投影矩阵 P，我们需要：

- 更多几何约束（比如已知的角度、长度、比例）
- 相机内参（焦距、主点位置）
- 或者使用已知的 2D–3D 对应点进行标定单一视角下，找到三个正交的消失方向，用vanishing point做标定。
具体方法：
![[image-131.png]]
选取正交的vanishing direction，先求内参K，再求旋转矩阵。
![[image-132.png]]
![[image-133.png]]
没办法求解和能求解的情况。

![[image-134.png]]

平行于同一个平面的直线的所有vanish point都会在同一直线上。
## Measuring Height without a Ruler
- Projective Invariant![[image-145.png| a quantity that does not change under projective transformations (including perspective projection)]]
- 第四个点我们选择vanishing point：![[image-146.png]]
- 2D面上的直线表示：![[image-147.png]]
- 先通过两直线交点求v，再求t![[image-148.png]]
# Multi-view
Epipolar：
xe上的点一定会出现在x'e'直线上
![[image-135.png]]

![[image-136.png]]![[image-137.png]]
xhat'是另一个相机坐标系下表达的三维点（虽然物理意义上是同一个点，但由于坐标系不同所以数值不同）利用垂直关系得到$$ x^' . [t x (Rx^)]=0$$![[image-138.png]]
![[image-139.png]]
叉乘转为矩阵运算![[image-140.png]]
定义essential matrix：本质矩阵，刻画了同一个三维空间上的点投影到不同成像平面需要满足的关系。
![[image-141.png]]
因为$$ l^T*x=0 $$![[image-142.png]]
没有对齐的情况：E是外参相关，K是内参矩阵。
![[image-149.png]]
F可以随意放缩+rank=2（detF=0）![[image-150.png]] 
现在给到两找图片对应同一点的坐标：![[image-151.png]]虽然给到了八组，但是最小二乘法之后不能保证秩为2.
解决方法：
1. 奇异值分解：去掉最小的奇异值![[image-152.png]]
2. 7-point algorithm：![[image-153.png]]

8方程组的计算问题：![[image-154.png]]
因为存在坐标数值相乘，所以数值相差会比较大（坐标相乘的和没有坐标相乘的）会导致一些问题。所以要对数字做归一化。
添加normalization：
![[image-155.png | 归一化，中心到原点并且平均下来偏离原点距离是根号二]]
	之后带入归一化求到的是一个F‘,再根据公式求F。![[image-156.png]]


# Image Stitching
- Homography:![[image-157.png]]
> 因为没有平移，所以可以直接求解出x’与x的关系，而一般情况是在对极线上
![[image-158.png]]
这个方程因为A的秩不满，一定会有一个为0的特征值，只需要求出对应的特征向量就是对应的h节。![[image-159.png]]


归一化：![[image-160.png]]
总体的pipeline：因为提取对应点的时候可能会发生错误，所以哪怕只需要四个点就能求解，还是可能会出错，要结合ransac判断inliers。
![[image-161.png]]


# Stereo

# SfM
同时估计3D和相机在哪里。现在只知道多张图片的点的匹配。
每个三维点都可以投影到m张照片上![[image-17.png]]
问题是只能得到相对物体大小关系，不能知道实际上的scale。![[image-25.png]]
列方程：条件总数要大于自由度
![[image-29.png]]
11是P的自由度（12个元素但因为up to scale，11个自由度）
3是3维点。
15：因为可以有一个Q up to，相当于加大了自由度，去掉变化的自由度15（-1的scale）



![[image-70.png]]