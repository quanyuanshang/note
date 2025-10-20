Kmeans选择k：聚类之间的距离更大越好。

animated transition betweeen graph。展现对应关系。
需要人来注意到变化。
Make changes visible in visualizations to reduce the cognitive load

Relativity and Absouluteness:

数据撒谎：
![[Pasted image 20250926085316.png]]


地图可视化
Line Data
捆绑线否则很乱 visual cluster。
bundling lines：捆绑弯曲，到目的地了再分开。
Choropleth Map：地图划分成行政单元按照区域上色。但没有考虑到区域背后存在人口差距的事实。每个州根据人口来进行缩放，变成cartogram map。

Bubble Set:
**OpenStreetMap**


Lecture 7
# 高维数据可视化
1-D：直方图 pie chart
2-D：散点图
3-D：不建议将抽象数据做3-d除了地图
更高维度：
1. visual channels![[Pasted image 20251013082136.png]]
> 在原来散点图上增加形状，但是也比较难以区分（5-6d）

2. mutiple coordinate多视图![[Pasted image 20251013082313.png]]
3. 降低维度：（信息量损失不会影响太大）降维之后得到的是抽象出来的维度和原来的语义就没有关系了。聚类。
## PCA：数据从高维降到2-d。主要原则是尽可能保证大方差。![[Pasted image 20251013083241.png]]
选择能保留最大方差的那个方向，再选能让方向第二大的那个方向。
但有可能两者之间存在线性相关性，两个方向差别不大。
这个时候考虑协方差矩阵：协方差为0就是完全独立的。![[Pasted image 20251013083535.png]]也就是取正交的方向。![[Pasted image 20251013083751.png]]

## t-SNE：
excellent for clusters
![[Pasted image 20251013084208.png]]
- MDS
使投影之后pairwise的distance和原始距离尽量相似。
- Scatter-plot Matrix
散点图矩阵
- Parallel Coordinates![[Pasted image 20251013085656.png]]每个轴是一个维度。但是该怎么order轴的顺序？重排之后可能会更有相关性。
解决视觉重叠：![[Pasted image 20251013085852.png]]
![[Pasted image 20251013085900.png]]
密集的地方用热力展示

circular Parallel coordinates
![[Pasted image 20251013090758.png]]
图源：Glyph-based visualiazation
![[Pasted image 20251013090848.png]]
人脸上encoding数据，得到商品画像![[Pasted image 20251013090952.png]]
> 不同城市犯罪脸
为什么选择A而不选择B

Small multiples：

![[Pasted image 20251013091331.png]]不同动画是因为人对动画的记忆比较短暂。


Dust & Magnet：
灰尘是数据点。磁铁是过滤器。越靠近磁铁，越能表示数据点和磁铁的相关性。

# 文本可视化
• Lexical Level: basic unit of text.
• Syntactic Level: structure information. 
• Semantic Level: meaning of text contents.

- 分析手段：
1. Tokenization：Remove stop words: a, the, that, etc+Plural form to singular form: men->man, truths->truth
	1. Vector-space model:![[image.png]]
	
2.  Topic Retrieving:![[image-1.png]]

可视化手段：
1. Word cloud：encoding词的频率等![[image-2.png]]![[image-3.png]]![[image-6.png]]
2. 
> 形状、排布、颜色。
> 布局：
> 1. Spiral layout：中心向外
> 2. 不同年份下单词的联系：![[image-4.png]]
3. Theme River：![[image-8.png]]![[image-7.png]]![[image-11.png]]![[image-10.png|话题转变方向、轴点击显示文字云、sparkline显示随着时间词汇的频率]]
# Hierarchical Data
orthogonal layout
tree
radial layout![[image-12.png]]
treemap：填充空间的算法![[image-15.png]]
icicle plots:![[image-13.png]]
voroni Map：两点之间取垂直平分线。![[image-14.png]]