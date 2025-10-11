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
## aliases：走样，![[Pasted image 20251010073228.png]]
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
