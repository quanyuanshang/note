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
- 单位复数在单位圆上，单位四元数是4d上的ring。![[image-92.png |轴角表示可以用四元数表示]]
![[image-93.png|向量表示成四元数标量=0]]
- 四元数的相乘可以表示组合旋转（轴角表示需要转换为矩阵）![[image-94.png]]
- 旋转插值：
	- ![[image-95.png | 线性插值+映射、这种问题是速度不恒定]]
	- Spherical Linear Interpolation（SLERP）：![[image-96.png]]