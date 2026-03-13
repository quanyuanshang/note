Physiss-based/Dynamic Approaches物理仿真
设定一个姿势希望计算出来的力能差不多完成姿势。
Proportional

# Math
## 叉乘
叉乘是不满足结合律的：
![[image-71.png]]
叉乘的矩阵表示：![[image-72.png]]
如何计算两向量之间最小旋转角：![[image-68.png]]
![[image-69.png|如何按照某个轴转动向量、ab是长度相等的，投影到与u垂直的平面如右下角，u是单位向量]]
![[image-70.png]]
![[image-73.png|Rodrigue rotation formula]]

## 行列式：
![[image-74.png]]
![[image-76.png]]![[image-75.png | 正交矩阵 逆=自转置 至少有一个特征值=+-1]]
## Transformation
[[GAMES101note]]
旋转矩阵式正交矩阵
旋转矩阵的一种理解：![[image-77.png|转换坐标系，向量的坐标是不变化的]]
三维中的旋转如何用插值表示？
### Conventions of Euler Angles：
- 旋转划分到三个轴分别进行。![[image-78.png]]
- ![[image-79.png]]
- 万向锁：就是当第二个旋转轴旋转90或-90时，第三个轴的转动效果可以被第一个旋转轴替代了：![[image-80.png]]

### Axis Angles
![[image-81.png]]
## Quaternions四元数
二维的旋转可以用复数来表示：
![[image-82.png]]
四元数：![[image-83.png]]
- 性质：![[image-84.png]]![[image-86.png]]![[image-88.png|]]单位四元数的逆等于自身共轭：![[image-90.png]]
- 单位复数在单位圆上，单位四元数是4d上的ring。
- 用四元数来表示旋转![[image-92.png |轴角表示可以用四元数表示]]
![[image-93.png|向量表示成四元数标量=0]]
- 四元数的相乘可以表示组合旋转（轴角表示需要转换为矩阵）![[image-94.png]]
- 旋转插值：
	- ![[image-95.png | 线性插值+映射、这种问题是速度不恒定]]
	- Spherical Linear Interpolation（SLERP）：![[image-96.png]]




# Kinematics
## Character Kinematics
每个关节的局部坐标系（朝向）与父关节*当前关节旋转有关![[image-99.png]]![[image-98.png]]还有一种表达方式是某个物体朝向是另一个物体朝向*朝向差：![[image-126.png]]
![[image-100.png|计算相对旋转]]
局部坐标与全局坐标的转换
![[image-101.png|l0是局部坐标位置，计算全局坐标位置]]![[image-104.png]]
![[image-102.png|由全局计算局部坐标，先把 xx 相对于 Q_3 原点的位移取出，再投影到 Q3Q_3 的轴上，这里 Q_3^T 是把全局向量表示换到Q_3 坐标系下的旋转（因为 Q_3 正交，Q_3^{-1}=Q_3^T]]
![[image-103.png]]


关节类型：
- hinge:自由度为1
- ball-and-socket ：自由度为3![[image-105.png]]



motion data in a file: BVH:![[image-106.png]]


##  Inverse Kinematics
2-joint IK problem：![[image-107.png |已知边长求角度转，再整体转到x]]![[image-108.png]]
目标函数：![[image-110.png]]
1. CCD：![[image-109.png|交替在每个角度对应的轴上更新，直到找到能让目标函数最小的最优解或者到了一定迭代次数]]
> ![[image-111.png| 对每一个关节转动都是转到能最靠近到目标位置的角度（前面学到的按照某个轴转到一个位置），之后再重复上述操作]]同时由于一般第一个关节移动的距离会更大，所以可以有策略的调节移动顺序

2. 梯度下降：![[image-112.png]]![[image-113.png]]
> 求解梯度
> 1. hinge
>![[image-114.png|assume all joints are hinge joint]]2. ball![[image-115.png|ball joint]]![[image-116.png|注意关注坐标轴a的变化]]


FUll body IK：如果root是根节点，从脚到根节点求IK要revert一下确保根节点是父！![[image-128.png]]
使用多条链条![[image-129.png]]

Gauss-Newton Method:![[image-117.png]]
![[image-121.png|左右两边乘上J，如果JtJ可逆就可以约掉]]![[image-122.png]]![[image-123.png]]
![[image-118.png|矮胖矩阵对应的是单点，高瘦的对应的是约束点较多，比如规定x_1,x2x3都要到某个位置]]
因为无法确保矩阵可逆：![[image-119.png | 加入阻尼项]]
其实就相当于加入正则化：![[image-120.png|比如w0大一点就可以让节点0少运动]]
## 重定向
![[image-124.png]]![[image-127.png]]



# Keyframe Animation and Interpolation
Interpolation：内插（插入点在周围点之内）
## linear Interpolation：![[image-130.png | 前面x那一块也可以直接看成是t，大小范围0-1之间]]![[image-131.png]]
## polynomial  Interpolation：
通过n个已知的点求解系数，如果有100个样本点，就需要n=99
![[image-132.png|要确保可逆首先要是一个方阵]]
但是在边缘的地方会出现剧烈震荡。考虑一次拟合一段。也就是用一系列低阶去拟合。
## Spline Interpolation：
还可以添加额外的条件让曲线更平滑，比如一阶导数，二阶导数相等![[image-135.png|]]
问题:
1. 调整一个点可能导致曲线发生变化影响其他的插值点。
2. 计算起来很麻烦（要对一个4N * 4N的矩阵进行运算。）
## Cubic Hermite Splines
只用到两个端点，假设提供给更多信息，这里是两个端点的速度（导数）：![[image-136.png]]![[image-137.png]]
hermite Basis Function:![[image-138.png]]
导数怎么获得？![[image-140.png]]
还有贝赛尔曲线[[GAMES101note#Curves：]]

# Data-driven Character Animation
## Motion capture
- Exoskeleton：外骨骼，手部捕捉用的比较多。
- IMU：Inertial Meaturement Unit惯性传感器单元，得到的是加速度和速度，基于关键传感器的技术。问题是容易产生漂移。速度加分的累加会产生误差。
- Optical Mocap：基于光学的，红外线达到标记点会进行反she，相机就会看到二维平面上有点，从而可以重建。![[image-141.png|通过相机接收到的点，连成的方向的交点来确定具体位置 相机越多越准确]]
	- 问题：
		1. 做复杂动作的时候，有些标记点会被遮挡或者歪斜。所以需要补点。
- 深度摄像机:问题是出现遮挡会麻烦
- motion estimation动作估计：

## Motion Synthesis
![[image-142.png]]
- motion retargeting：![[image-143.png]]
	- 还有方法就是只记录一些关键点的位置，不记录关节旋转，重定向位置，再对新的角色做IK。
- Motion Transition：采样是一段一段采的。![[image-144.png]]
	- facing frame朝向坐标系：假设处于y up坐标系![[image-145.png]]这里相对于全局坐标轴的转向是R可以分解为在y轴的旋转和在xz平面上的向量的旋转。如果能知道从当前facing坐标轴的y轴转回到全局坐标系的y轴的R‘，就能把Ry表示出来。![[image-146.png]]
	- 让某一帧跑步的facing frame和走路的对齐，对齐之后再做插值![[image-147.png]]
		你先把目标坐标系从它自己的参考系转换到世界坐标系（用 $R_1^T R_1(t)）$，再从世界坐标系转换到原始坐标系（用 R_0）。最终得到的是目标坐标系在原始坐标系下的旋转矩阵。 ![[image-148.png]]
- Motion Composition:
	- 如何对长动作进行分割：定义距离，举例可以是关节旋转的角度差![[image-149.png]]- 横轴是帧编号（frame i）
    
	- 纵轴也是帧编号（frame j）
	    
	- 每个像素值 D(i,j)D(i, j) 表示第 i 帧和第 j 帧之间的姿态差异（比如关节角度、位置等）
	- 颜色越深（或数值越小）表示两个帧越相似，颜色越浅（或数值越大）表示差异越大。
> - 如果某个点 D(i,j)D(i, j) 是局部最小值，说明第 i 帧和第 j 帧非常相似。这些点可能是动作的**重复、循环或过渡点**，非常适合用来做**动作分段**或**拼接**。
    例如：在走路动作中，左脚迈出和右脚迈出可能在不同时间点重复出现，对应的帧之间就会有低距离值。所以这些点可以作为“动作片段的边界”或“拼接点”。
	- 动作图：![[image-150.png]]
	- pipeline：![[image-151.png]]
    


