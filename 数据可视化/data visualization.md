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
# Network data
Sugiyama Representation：
数据本身有一种原生的顺序
force-based algorithm:
适用于没有原生数据，每个节点之间会有引力和斥力。
arc diagram：
多个实体之间的关系![[image-5.png]]
Adjacency matrix summary：
比较抽象但对于密集连接的方式很友好。可以结合一下![[image-9.png]]
Gmap
Topology Simplification
数据简化：
1. 边捆绑![[image-16.png]]
2. 最小生成树、点聚类


prefuse工具包
gephi图可视化


# 时序信息
## Temporal Data
linear time
radial time:时间具有周期性

grid time：![[image-18.png|日历]]
spiral time：螺旋上升：![[image-17.png]]
arbitrary time：折叠，按照时间顺序排列，按相似度折叠。![[image-19.png]]
chronological：
![[image-20.png]]
## Streaming data
无法预测数据量。
Dynamic Time Warping (DTW)
SAX![[image-21.png]]
设计原则:
• Follow Shneiderman’s mantra 
	• Overview first, zoom and filter, detail on demand
 • Avoid abrupt visual change 
 • User actions should receive immediate visual feedback 
 • Assertion: Showing several levels of detail simultaneously provides useful high information density in context


## 伪基站项目：
小组分工
后端数据挖掘（反应课程关联）
前端视觉系统设计
视频制作

第一步不应该是想用什么方法来可视化
第一步是应该想清楚项目的痛点。
practical。
DBscan根据密度寻找核心。
什么样的短信能被归为一类？
不确定性：采样频率不一样，点的稀疏不一样就需要更改参数。（比如在警察局附近就稀疏。）参数调整。
如何确定不同短信是来自同一基站？
- 文本相似度
- 时空距离 
插值还原。
轨迹重构，要把拟合出来的不确定轨迹拟合到道路上。





11.24 mid pre
12.12 final pre
# project 选题
## 🎵 Topic One：音乐数据可视化与趋势分析

**目标**：分析音乐发布数据，探索行业趋势与潜力。

**任务**：

1. **音乐人表现评估**：选择一位音乐人（如 Sailor Shift），通过数据分析她的行业表现。
    
2. **音乐流派发展分析**：研究某一流派（如 Oceanus Folk）的演变过程，提取关键趋势。
    
3. **未来明星预测**：预测未来五年该流派可能出现的明星，并提供数据支持。
    

**建议方法**：

- 使用时间序列图、地理热图、发布频率图等。
    
- 可结合社交媒体热度或流媒体播放量。
    

## 🏛️ Topic Two：公共机构监督行为分析

**目标**：分析经济监督委员会成员在传统渔业（FILAH）与现代旅游（TROUT）中的行为偏向。

**任务**：

1. **单独数据偏向分析**：分别分析 TROUT 与 FILAH 的数据，判断是否存在成员偏向。
    
2. **整合知识图谱分析**：设计可视化方案，分析成员在整合数据中的行为分布，判断是否存在整体偏向。
    
3. **深入案例调查**：
    
    - 选取 TROUT 指控的成员，分析其行为在整合数据下的变化。
        
    - 分析 FILAH 数据中哪些成员受采样偏差影响最大，并用可视化方式呈现。
        

**建议方法**：

- 使用网络图、时间轴、雷达图等。
    
- 强调数据整合前后的差异性。
    

## 🌊 Topic Three：海岸区许可与利益关系分析

**目标**：揭示海岸区开发中的权力关系与潜在违规行为。

**任务**：

1. **通信模式分析**：分析拦截的无线电通信，提取周期性互动模式。
    
2. **船只与人员关系分析**：构建船只与人员之间的互动网络。
    
3. **非法行为调查**：
    
    - 分析 Nadia Conti 是否仍涉及非法行为。
        
    - 可视化其行为轨迹，判断是否存在可疑依据。
        

**建议方法**：

- 构建知识图谱、行为时间线、互动热力图。
    
- 可结合 NLP 技术分析通信内容。
    

## 🤖 Topic Four：BERT 情感嵌入结构分析

**目标**：探索 BERT 模型在情感表达上的结构演化与偏差。

**任务**：

0. 选择合适的 BERT 模型与数据集。
    
1. **宏观评估**：测试 BERT 在不同领域（如电影 vs 产品评论）中情感表达的一致性与泛化能力。
    
2. **层级演化分析**：研究情感信息在 BERT 各层之间的传递与抽象过程。
    
3. **微观贡献分析**：识别句子中对最终情感嵌入影响最大的词汇，并分析其变化影响。
    

**建议方法**：

- 使用嵌入可视化（如 t-SNE、UMAP）、词向量热图、层级流图。
    
- 可结合 Transformer 可视化工具如 BertViz。

设计过程，自己的方案好在哪里


```
```
(base) PS D:\cs\数据可视化\Topic1> python scripts\analyze_genre_uniformity.py --min-genres 4 --max-spread 0.1 --max-dominant 0.45 --top 15 --export-json output\uniform_persons.json
[INFO] total persons: 11361
[INFO] persons with >= 4 genres: 147
[INFO] matching uniform persons: 29

Top 15 examples (spread ≤ 0.1, max share ≤ 0.45):
- #4465 Qiang Song: genres=5, spread=0.000, std=0.000, entropy=2.322 | Darkwave:0.200, Dream Pop:0.200, Indie Folk:0.200, Post-Apocalyptic Folk:0.200, Speed Metal:0.200
- #1942 Yan Han: genres=5, spread=0.000, std=0.000, entropy=2.322 | Dream Pop:0.200, Post-Apocalyptic Folk:0.200, Space Rock:0.200, Symphonic Metal:0.200, Synthwave:0.200
- #4185 Na Ren: genres=5, spread=0.000, std=0.000, entropy=2.322 | Americana:0.200, Desert Rock:0.200, Doom Metal:0.200, Indie Folk:0.200, Space Rock:0.200
- #7398 Charles Vazquez: genres=5, spread=0.000, std=0.000, entropy=2.322 | Indie Rock:0.200, Lo-Fi Electronica:0.200, Oceanus Folk:0.200, Post-Apocalyptic Folk:0.200, Synthwave:0.200
- #6424 Cheryl Carter: genres=5, spread=0.000, std=0.000, entropy=2.322 | Americana:0.200, Doom Metal:0.200, Indie Folk:0.200, Indie Rock:0.200, Synthpop:0.200
- #4259 Jie Tao: genres=5, spread=0.000, std=0.000, entropy=2.322 | Alternative Rock:0.200, Indie Folk:0.200, Jazz Surf Rock:0.200, Lo-Fi Electronica:0.200, Psychedelic Rock:0.200
- #6039 Fang Yin: genres=5, spread=0.000, std=0.000, entropy=2.322 | Alternative Rock:0.200, Blues Rock:0.200, Emo/Pop Punk:0.200, Indie Pop:0.200, Southern Gothic Rock:0.200
- #6779 Chao Zhong: genres=5, spread=0.000, std=0.000, entropy=2.322 | Alternative Rock:0.200, Blues Rock:0.200, Dream Pop:0.200, Indie Rock:0.200, Southern Gothic Rock:0.200
- #3990 Ming Huang: genres=5, spread=0.000, std=0.000, entropy=2.322 | Americana:0.200, Dream Pop:0.200, Indie Folk:0.200, Synthpop:0.200, Synthwave:0.200
- #9231 Brett Brown: genres=4, spread=0.000, std=0.000, entropy=2.000 | Doom Metal:0.250, Dream Pop:0.250, Post-Apocalyptic Folk:0.250, Symphonic Metal:0.250
- #1021 Przemysław Grupa: genres=4, spread=0.000, std=0.000, entropy=2.000 | Alternative Rock:0.250, Blues Rock:0.250, Indie Pop:0.250, Southern Gothic Rock:0.250
- #3157 Jun Chang: genres=4, spread=0.000, std=0.000, entropy=2.000 | Americana:0.250, Doom Metal:0.250, Symphonic Metal:0.250, Synthwave:0.250
- #678 Nathan Bryant: genres=4, spread=0.000, std=0.000, entropy=2.000 | Doom Metal:0.250, Dream Pop:0.250, Indie Rock:0.250, Symphonic Metal:0.250
- #676 Brandon Bartlett: genres=4, spread=0.000, std=0.000, entropy=2.000 | Doom Metal:0.250, Dream Pop:0.250, Post-Apocalyptic Folk:0.250, Symphonic Metal:0.250
- #2613 Yong Wen: genres=4, spread=0.000, std=0.000, entropy=2.000 | Doom Metal:0.250, Dream Pop:0.250, Indie Folk:0.250, Oceanus Folk:0.250
[INFO] exported 29 records to output\uniform_persons.json
```
```


