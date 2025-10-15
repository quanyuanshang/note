Rasterization：光栅化，三维空间内的形体显示在屏幕上。

# Transformation

## 2D transformation
1. scale：![[Pasted image 20251007013018.png]]
2. Reflection：![[Pasted image 20251007013107.png]]
3. Shear：![[Pasted image 20251007013415.png]]
> 相似三角形
4. Rotate：![[Pasted image 20251007013829.png]]![[Pasted image 20251007135000.png]]
5. 
6. 线性变换=Matrice

## Homogeneous coordinates
translation 平移不是线性操作
解决方式：![[Pasted image 20251007014417.png]]
> 为什么向量的最后一个位是0？![[Pasted image 20251007014625.png]]
> ![[Pasted image 20251007014708.png]]
> 点加点是连个点的中点

- Affine Transformation 仿射变换![[Pasted image 20251007014821.png]]
- ![[Pasted image 20251007014913.png]]
-  Composing Transforms![[Pasted image 20251007015400.png]]

- Decomposing Complex Transform 沿着任意点旋转：![[Pasted image 20251007015656.png]]


- Inverse Transformation

## 3D Transformation
![[Pasted image 20251007015843.png]]![[Pasted image 20251007135546.png]]
Rodriguez Rotation Formula
![[Pasted image 20251007135921.png]]


## Viewing Transformation
### View、Camera Transformation
- Camera: pos\up direction\viewing direction
- ![[Pasted image 20251007141128.png]]
- 移动相机的操作、先平移pos到原点然后旋转轴（从g，t转回到xyz不好求，所以先求逆矩阵，再根据旋转矩阵是正交矩阵，逆矩阵就是转置就得）
- 一般来说相机会在原点上，往-z方向上看

### Projection Transformation
#### Orthographic
![[Pasted image 20251007141625.png]]
![[Pasted image 20251007142354.png]]
#### Perspective 
- Squish：
1. 远平面上的图案挤压，中心是不会变化的（0,0，z,1）
2. 近平面上的任一点的z坐标总是n
- 再做正交投影
![[Pasted image 20251007143556.png]]
利用性质1、2解：
![[Pasted image 20251007143705.png]]![[Pasted image 20251007143647.png]]最终解出来：![[Pasted image 20251007143811.png]]


# Rasterization
vertical field of view && aspect ratio:![[Pasted image 20251007181346.png]]
![[Pasted image 20251007181546.png]]
Rasterize:把东西画在屏幕上
Canonical Cube to Screen
视口变换：原本坐标原点在左下角现在移动到中心，再让它适应屏幕大小，这里和z周没有关系因为看到的是2D
![[Pasted image 20251007182224.png]]
接着上像素
显示的图像其实就是内存中的一块区域
光栅化：
采样，判断每个像素的中心点**是否在三角形内部**，是的话是1不是就是0
如何判断是否在三角形内部：叉乘（必须选择收尾连接的向量）
可以通过限定范围减少计算量

问题：产生锯齿Jaggies
## Sampling Artifacts
1. Jaggies-sampling in space![[Pasted image 20251007213000.png]]
2. Moire undersampling
3. Wagon wheel effect - sampling in time
总的来说就是采样跟不上频率
**Solution**:Antialiased Sampling
采样前先对信号进行模糊

Flourie Transform：![[Pasted image 20251010072808.png]]
## aliases：走样，
![[Pasted image 20251010073228.png]]
filtering:
- high-pass filter:图像线条（颜色变化剧烈）
- low-pass filter:模糊

convolution therom：时域的卷积=频域的乘积
box filter：![[Pasted image 20251010074524.png]]为什么盒子变大频域上反而变小？用越大的box，图像会更模糊。

**Sampling = Repeating Frequency Contents**
![[Pasted image 20251010190936.png]]
- 冲击函数在频域上也是冲击函数
**Alias = mixed Frequency Contents**
采样不够快、冲击函数在谱域上的频率低，导致信号重叠![[Pasted image 20251010191256.png]]
**Solution**：
1. 增加采样率
2. Antialiasing: 先把一个信号的高频信息拿走模糊一下：先做卷积做平均，然后对每个像素的中心值做采样。![[Pasted image 20251010191606.png]]

## MSAA
近似模糊这个过程
### Supersampling
![[Pasted image 20251010192136.png]]
![[Pasted image 20251010192315.png]]
左下角，四个区域有一块在三角形内部，覆盖率是25%，模糊再采样。**这种方式并没有增加分辨率**![[Pasted image 20251010192348.png]]
代价：计算量增大

# Visibility
画家算法：先画最远的面，再往前。一般要先根据深度排序，但可能有互相遮挡的问题，无解。
实际采用的方法：Z-buffer。
## Z-buffer
在生成最终图像frame buffer的同时生成像素对应的最浅深度 depth buffer。
深度：里摄像机越近越小
![[Pasted image 20251011185654.png]]
如果要画上去的深度小于先前像素对应的深度，就更新这个像素对应的深度。![[Pasted image 20251011185849.png]]
和画的顺序无关。
同时如果采用MSAA，对于每个采样点都要有一个zbuffer来进行记录。

# Shading着色
Shading!=shadow着色不考虑其他物体的存在只考虑自己所有是没有阴影的
The process of applying a **material** to an object
## Blin-phone Reflection model:
![[Pasted image 20251011192453.png]]
	- Diffuse Reflection：
	- r通常指的是**光源到当前片元（像素）位置的距离**，即：
		![[Pasted image 20251011193351.png]]到达的能量与接收到的能量的乘积。两个单位向量的点乘为余弦，和0取max，防止负数情况（光从下面打上来）。
		如果不同那个的点有不同吸收率，颜色就会不同。漫反射稀疏，d=0，代表吸收所有光，1表示不吸收。可以把这个数据表示成三通道0-1.
		- 对于观察某个特定的点，漫反射和观测角度无关，因为他往四周反射方向是一样的。
		- ![[Pasted image 20251011193901.png]]kd从小到大
	- Blinn-Phong：当观察方向和镜面反射方向足够接近就能够观察到高光 == 发现向量与半程向量![[Pasted image 20251012163957.png]]通常认为ks是白的。佳教育险增加指数次方加开角度增大的衰减速率![[Pasted image 20251012164347.png]]
	- Ambient Term:环境光![[Pasted image 20251012164627.png]]是一个常数，假设各个视角都是一样的
- ![[Pasted image 20251012164714.png]]
## Shading Frequencies
着色应用在多少点上
- Flat shading：每一个三角形面求一个法线（做叉积），三角形内部就是一样的着色
- Gouraud shading：每一个三角线顶点求出法线着色再用插值方法填充三角形内部颜色。
- Phong shading: 每个像素单独着色
![[Pasted image 20251012165513.png]]
当几何形体面本身足够多，使用简单的Flat就可以效果很好。

Defining Per-Vetex Normal Vector：
顶点关联的面的法线做加权平均：![[Pasted image 20251012165707.png]]
逐像素法线：![[Pasted image 20251012165823.png]]
左边顶点法线和右边法线知道，中间发现就是过度过去。

## Graphics Pipeline
![[Pasted image 20251012170321.png]]


## Texture Mapping

定义任何一个点的不同属性
三角形映射到纹理上。
规定u,v:(0-1)![[Pasted image 20251012171801.png]]
### Interpolation Across Triangles
- Barycentric Coordinates：三角形内部插值。过渡自然。
>$\alpha \beta \gamma$ 非负的话就一定是在三角形内部
>
>特殊点：三角形重心（1/3,1/3,1/3）
>![[Pasted image 20251012193658.png]]

属性也可以用重心坐标表示，但是在投影下，重心坐标是会变化的。对于三维空间中的属性，建议在三维空间尚座插值，在投影到二维。**透视矫正**- 投影过程把顶点从相机/世界空间变换到裁剪空间，然后做齐次除法（x/=w, y/=w, z/=w）得到屏幕坐标。屏幕空间对属性（颜色、纹理坐标、法线、深度等）做线性插值会被投影变换（1/w）扭曲，直接用 α·A0+β·A1+γ·A2 会不正确。
- 解决：先插值 A/w（或 z/w），再除以插值后的 1/w。这就是透视正确插值（perspective-correct interpolation）。
> - 在图形管线中，顶点经过投影变换后是齐次坐标 (x, y, z, w)。v.w() 就是这个 w。

### Texure Magnification

如果纹理的像素（texel）远远小于屏幕的像素（pixel），会出现屏幕上多个像素对应了一个texel。
Solution：平滑过渡
1. BIlinear interpolation双线性插值：取临近的四个![[Pasted image 20251012194647.png]]
2. Bicubic：取邻近的16个
- 那如果纹理大了怎么办？
	- 远处的一个像素会对应比较大的纹理区域，这时候只用一个采样点去采样肯定不对![[Pasted image 20251012195113.png]]
	- 那就不采样，要知道这个区域的平均值。Range Query
		- mipmap：fast 近似 只能做正方形的范围查询。
			先对纹理进行缩小分辨率。分成不同层![[Pasted image 20251012200341.png]]近似正方形区域![[Pasted image 20251012200117.png]]如果边长是1* 1，在原本的纹理图上映射就可以对应一个像素，如果是44，那么就到第二层对应一个像素（第一层是22）。D就是在第几层去查询。如果是查1.8层，就用插值。![[Pasted image 20251012200537.png]]每一层之间取色是做bilinear插值，不同层之间再做一次插值![[Pasted image 20251012200738.png]]出现这种情况是因为只能做正方形，但实际上压缩是水平数值都存在的。
			![[Pasted image 20251012201056.png]]
	
			
		- Anisotropic Filtering：对应矩形区域。但不太好处理斜的区域![[Pasted image 20251012201203.png]]类比mipmap，总开销额外增加原本的三倍
		- EWA filtering：![[Pasted image 20251012201137.png]]


### Applications of Textures
可以用纹理来表示环境光。
凹凸贴图：纹理可以定义表面上任一点沿着法线方向的高度。这些点的项堆高度变化止呕就近似了不同的小三角形，这些小三角形的发现就会和原来不一样，法线变化，着色也就变化了。
也就意味着伪造假的法线=>伪造假的着色结果制造凹凸表面即使没有改变几何。
#### Bump mapping
凹凸贴图假设集合表面每店沿着法线到的高度变化![[image-2.png]]
如何计算normal：
![[image-3.png]]
先根据离散导数定义计算出切线，再根据法线正交于切线求出法线。
由于纹理贴图有两个方向u，v![[image-4.png]]
> 但是这个情况下，也就是局部坐标系，发现坐标是001


这种方法没有边缘处理，同样也没有处理阴影。![[image-5.png]]
#### Displacement mapping
真的做位置的移动。也就需要几何三角形足够细能跟得上纹理的变化
# Shadow Mapping

key idea：the points not in shadow must be seen both by the light and by the camera.
![[image-28.png]]
1. render from light: 记录距离光源的深度。记录信息的分辨率如果比较低就会走样。![[image-29.png|示例]]
2. render from eye：从眼睛出发看物体，看到的点投影回光源，比较这样的深度和原本从光源出发记录的深度，如果一致，就说明这个点不在shadow里面![[image-30.png]]
	- 为什么看起来这么脏？因为数据精度 float判断相等不容易
- 硬阴影：点光源
- 软阴影：考虑光源大小![[image-31.png]]
# Geometry
- Implicit：只能得知坐标关系。$f(x,y,z)=0$ 虽然不能直接判断形状但能很快知道某个点是否在物体内或者外。
	- Algebraic：不直观![[image-7.png]]
	- CSG：结合基本运算得到另一个几何。![[image-8.png]]
	- Distance Functions：描述任意一个点到表面的最近的距离。![[image-9.png]]最后可以根据blend的距离函数来恢复中间状态。找出对应0的位置，就是表面
		- 水平集2d:![[image-10.png]]
	- fractals:自相似![[image-11.png]]
- explicit：判断变难了![[image-6.png]]参数映射
	- point cloud
	- polygon mesh![[image-12.png]]如何定义三角形?![[image-13.png]]> v是顶点 vn是法线 vt是纹理坐标 f是第几个顶点 第几个纹理第几个法线 每三个点(一排)就是一个三角形
## Curves：

Bezier Curve：
确定起始点和终止点以及切线方向![[image-14.png]]规定是三倍
- 如何确定中间的点：![[image-15.png]]每一段线段上找时间t上的点，直到最终只剩下一个点![[image-20.png]]![[image-16.png]]**n 是控制点的数量减一**：如果你有 n+1n+1 个控制点（比如 b0,b1,...,bnb_0, b_1, ..., b_n），那么 Bézier 曲线就是 n 阶的。**在参数区间** t∈[0,1]t \in [0, 1] 内的任何时间点都可以用 Bézier 曲线的公式来表示![[image-17.png]]这样各个点不在同一平面上也可以了![[1111.png]]
	1. 起点终点分别是t=0 1
	2. 三次贝赛尔曲线：![[image-21.png]]切线方向
	3. 仿射不变性：放射后画曲线=画曲线后仿射
	4. convex hull property：convex hull 是能够包围制定几何形体的最小凸多边形![[image-22.png]]贝赛尔曲线一定是在控制点形成的凸包的内部
Piecewise bezier curves：
每次用较少的（每4个）控制点来逐段定义曲线![[image-24.png]]
如何确保光滑：第二段曲线的七点切线与上一段的切线一样（导数连续）
C0连续：只要第一段终点=第二段的起点![[image-25.png]]
C1连续：切线共线并且方向相反

## surface
Bezier Surface：
![[image-26.png]]
- mesh operation：
1. Mesh Subdivision：引入更多三角形+调整位置细化模型
	- 顶点区分为新的顶点和旧的位置。
	- loop subdivision：
	- update：
		- new:![[image-1.png]]
		- old:![[image-18.png]]老的顶点的更新方式主要依靠自身和其他旧的顶点。如果入度n比较大，那么其他点的作用会比较大。
	- Catmull-Clark Subdivision：
		- extraordinary vertices：奇异点，只要入度不是4。在非四边形面中点上的点就是奇异点。
		- non-quad faces：非四边形面
		- non-quad faces引入奇异点之后非四边形面就会消失。并且第一次细分之后不能再增加奇异点![[取中点和三角形内部中点相连接.png]]
		- ![[image-19.png]]
		- 点的更新：![[image-23.png]]
2. Mesh  Simplification：模型简化![[image.png]]
		理念：不同情况下选择不同那个复杂度（如果比较远的话）
	- 边坍缩。![[捏起来.png]]怎么判断哪些边比较重要？
	- Quadric Error Metrics：![[image-27.png|坍缩任意一条边之后找到点的位置是变化最小]]
	- 使用优先级队列，每次取二次误差最小的边，再更新，再找。
	- 
3. Mesh regularization:不至于存在过于窄或者过于宽的三角形。




