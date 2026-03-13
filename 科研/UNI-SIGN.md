a unified pre-training framework that **eliminates the gap between pre-training and downstream SLU tasks** through a large-scale generative pre-training strategy and a novel fine-tuning paradigm.

- SLU：sign language understanding
	- isolated sign language recognition (ISLR)
		- concentrates on classifying individual sign language movements,
		- 指标：
			- ==Phoenix2014-T(Camgozetal.,2018)CSL-Daily==
			- 
	- continuous sign language recognition (CSLR)
		- learn the alignment of sequences between sign language and their correspond ing glosses学习手语与其对应注释之间序列的对齐
	- sign language translation (SLT).
		- SLT requires the model to generate textual descriptions corresponding to sign language sequences
		- gloss-based 基于注释的:the model acquires intermediate representation of glosses, leading to improved text generation capabilities.
		- SLRT：transformer encoder-decoder framework in SLT and incorporates gloss-level supervision into the transformer encoder through ==CTC loss==.
		- STMC-T：tackles the SLT through multi cue modeling.
		- SLTUNET(Zhangetal., 2023a) and MMTLB：employing three sub-tasks (sign-to-gloss, gloss-to-text, and sign-to-text) attempt to transfer knowledge from large-scale external text corpus and pre-trained language models into SLT

> [!问题]
> 		costly gloss labeling limits dataset and model scalability 不得不尝试gloss free
> 


- GFSLT-VLP：proposed **text-video contrastive loss** to pre-train translation models,文本视频对比损失，提升了 glossfree的表现
	- MSLU (Zhou et al., 2024) and C2RL (Chen et al., 2024a) further introduce the pretext tasks of **keypoint reconstruction** and **language modeling**, respectively.但是受限于the scale of the video-gloss/video-text paired data or the transferring capability of downstream tasks.
- Sign2GPT&Sign LLM：take advantage of the **linguistic knowledge inherent in large language models (LLMs)** to enhance gloss-free SLT.
- YouTube-ASL：directly employs language modeling task for large-scale pre-training, demonstrating the potential of generative pre-training and empha sizing the importance of scaling datasets.
	- proposeda1,447hoursBritishSign Language(BSL)dataset and a 984 hours American SignLanguage(ASL)dataset,

> [!和seq2seq的相关] seq2seq in vision
>1. LLaVA
>2. VisionLLM-v2



Trend：utilize large-scale **self-supervised learning** to unleash the statistics in unlabeled data
- SignBERT+
	- employs a masking-and-reconstructing strategy to mine contextual information of sign language,
	- Problem：neglecting the acquisition of textual knowledge, causing the gap with downstream task like SLT.
- the incorporation of gloss/text data has been proven effective
	- they are generally limited by **the scale** of the video-gloss/video-text paired data or the transferring capability of downstream tasks.


> [!数据集] 数据集
>Previous works primarily focused on collecting ==BSL and ASL== datasets,（美国手语和英国手语）



Uni-Sign：Uni-Sign utilizes generative pre-training on large-scale datasets, capture the semantics embedded in sign language.
- innovation
	1. introduce CSL-News
	2. treat downstream tasks as a single SLT task
	3. incorporate a prior guided fusion (PGF) module and a score-aware sampling strategy
- method：总体而言是三步骤Stage 1: pose-only pre-training, Stage 2: RGB-pose interaction continue pre-training（compensates for the visual cues lost due to inaccurate keypoints,）, and Stage 3downstream task fine-tunning
	1. LARGE-SCALE DATA CURATION: CSL-NEWS
		1. ==FunASR== extract textual annotations from the video
		2. Then 根据分隔符号逗号叹号等生成 video-text pairs
		3. using predefined relative coordinates to eliminate background interference.使用预定义的相对坐标裁剪手语视频，以消除背景干扰。![[image.png]]
	2. UNIFIED PRE-TRAINING AND FINE-TUNING：把keypoints划分成左右手 头和脸
		- pose-encoder：先拿到比较全局的特征Fp，再去拿手部特征Fr：videos cropped using keypoint coordinates and resized to 112 × 112 pixels+==three-layer spatial GCN.== [[GNN]]![[image-1.png|learning representa tions from both hands rather than the entire image.]]最后两者融合一下作为F~p，再把Fp和~p都放进==ST-GCN== 通过均值池化层在组内聚合，并在所有组之间进行连接，以生成最终特征 Fsign ∈ RT× 4C，随后被输入到语言模型中。
		- ![[image-2.png]]
		- pre-training uni-sign:employ **the generative pre-training paradigm** to utilize the knowledge embedded within the pre-trained large language model.我们将特征 Fsign 投影以匹配语言模型的维度，然后将其输入语言模型。![[image-3.png]]
		- Fine-tuning Uni-Sign:treat ISLR, CSLR, and SLT as a single SLT task. To construct supervision targets, ISLR uses **action description**, CSLR employs **sequences of glosses** separated by spaces, and SLT utilizes the **translation text**, denoted as **yword, ygloss, ysentence,** respectively.![[image-4.png|最终的目标函数]]
	3. MULTI-MODAL FUSION：
		- ==Multimodal networks==：without considering **fine-grained spatial relationships** 就是对场景中**不同部分之间非常精确的空间关系**进行建模或分析，which are crucial for narrowing the representational gap between modalities。
		- **prior-guided fusion (PGF) module** that leverages keypoint coordinates as priors to model fine-grained spatial consistency between modalities,
		1. Given Fp i,t and Fr i,t, where i = {lh,rh}, we first employ **a multi-head attention module** to incorporate the **global RGB information** into Fp i,t.
		2. utilizing the keypoint coordinates Js i,t as priors to initialize the reference points in deformable attention- 模型中有一个 **Deformable Attention 模块** 这个模块需要知道**从哪里**去采样特征；作者把人体/手部的 **关键点位置（keypoints）** 提供进去，作为这些采样点（reference points）的**初始位置**。The fused features are denotedas ˆFp i,t. Finally,Fp i,t and ˆFp i,t are fed into a gater to expedite convergence during Stage2 training. ![[image-5.png]]
		3. Score-aware sampling strategy：包含 RGB 姿态会对计算资源提出显著挑战。 RGB 与高置信度关键点之间的信息冗余 有选择地选择对应低置信度关键点的 RGB 帧。使用手部关键点的平均置信度作为可靠性评分，然后将采样得分计算为 1−rs。接下来，我们根据这些采样得随机抽取 Psamp% 的 RGB 帧。相当于，手部置信度越高，采样的RGB帧就不需要很多了