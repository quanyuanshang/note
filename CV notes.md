# Camera
- Projection
1. Points are projected to points 
2. Lines are projected to lines 
3. Length is not preserved • The closer, the larger 
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
> 小光圈阻挡了会引起circle of confusion的光。
> ![[Pasted image 20250922152744.png]]
> 但同时光圈变小减少了进光量可能就需要更长的曝光时间

##  Field of View
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

### Image Denoising
![[Pasted image 20251011151524.png]]
	1. Average：Let’s replace each pixel with a weighted average of its neighborhood using **filter kernel**.
> Convolution：卷积核上下左右翻转之后![[Pasted image 20251011153054.png]]
> - 性质：
> 	1. Shift invariance：filter(shift(f)) = shift(filter(f))
> 	2. Lineartity：$filter(f_1 + f_2) = filter(f_1) + filter(f_2)$
> 	










