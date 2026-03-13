# 基础知识
## test

- seq labelig: eg：词性分析。如何联系上下文？![[image.png|给一个window、但是windows再大也不能根据输入seq来盖住，因为seq长度会变化]]
	- self-attention：每个输入向量经过selfattention 考虑整个seq，之后再进fc![[image-1.png]]
	- attention怎么计算两个向量的相关度？![[image-2.png|seq乘上矩阵之后做点乘]]![[image-4.png]]![[image-5.png|b1-4是同时计算出来的]]![[image-6.png|矩阵并行计算]]![[image-7.png]]![[image-9.png]]
	- multihead self-attention：不同的head找不同的相关性。![[image-10.png| 不同head体现在kq求出来之后再成两个不同参数的W变成q1q2]]
	- 目前为止位置信息都没有考虑进来，接下来就就是encode position![[image-11.png|如何encode是自己定的或者从机器学出来的]]
	- truncated self-attention：![[image-12.png|对于像语音这样很长的序列，可能不需要关注整个序列]]
	- self-attention for image:将每个pixel看成三维向量。
	- 和CNN的区别：CNN只会考虑receptive field里的秩序而self attention考虑整张图的。self-attention弹性比较大，相当于自动学习receptive-field，需要更多的数据量来训练。CNN是selfattention的个例，更为简单，训练资料更小表现更好。
	- 和RNN区别：![[image-13.png|RNN相距太远的序列影响就会变小，没办法平行处理所有Vector]]
	- 在graph上的应用：![[image-14.png]]
	
- seq2seq:输入是N个向量，输出是N‘个label(N'是自己决定的)。用于语音识别、语音翻译等，让模型自己决定输出多少长度的序列。文字到语音。chatbot。multi-label classification机器自己决定每个东西属于多少class
	- encoder：给一排向量输出一排向量。![[image-15.png]]![[image-16.png|每个block拆解，self+res+layer=norm（维度上）+FC+res+norm]]![[image-22.png|总览]] 
	- decoder：加入stop token（相当于产生一个句号）让它自己决定产生多少![[image-20.png|先根据token，过Softmax求vocabulary里概率率最大的，再作为onehot加入decoder以此类推、但会看到错误的输出导致之后的错误|557x396]]
		- mask self attention：![[image-19.png|由于decoder是按照前面产生的产生后面，所以要有mask]]
		- AT &NAT：![[image-21.png]]
		- cross-attention：![[image-23.png|decoder产生q，与encoder产生的k形成v进入FC]]

## train
- Teacher Forcing
训练时提供正确历史，往右移动一位，防止信息泄露，预测位置t只能看到t-1和之前。
希望每一次做完Softmax分类，这样得到的cross entropy最小。所以在模型训练阶段，给到decoder的是正确答案。
- 为什么需要交叉注意力机制？让解码器知道：

- 源语言中哪些部分与当前目标token相关
- 如何将源语言信息转化为目标语言![[image-26.png]]![[image-24.png]]
- Copy Mechanism：有的时候机器需要复述用户内容
- Guided Attention：机器自主学习的时候有时候会漏掉信息。在语音辨识、语音合成很重要。比如限制机器Attention从左到右（设定一个score）
- Beam Search：![[image-25.png|对于答案明确的任务（语音辨识）但对发挥创造力的就不太好]]
- OPtimization：训练的时候用cross entropy是因为好算微分，但是bleu score不行。
- scheduled sampling：训练的时候给一些错误的信息，从而避免test时候一步错步步错。

# BERT
你好！非常棒的问题。这说明你不仅学会了怎么跑代码，而且开始思考模型**内部的运作机理**了。这正是从“调包侠”进阶到“算法工程师”的关键一步。

我们逐一拆解你的问题：向量是怎么来的？有什么意义？为什么它这么强？

---

### 一、 向量是怎么获取的？有什么意义？

我们可以把 BERT 模型想象成一个**“超级加工厂”**。
输入是一串原始的字（Token），经过 12 层（BERT-Base）的 Transformer 编码器层层加工，最后输出了两类产品：`last_hidden_state` 和 `pooler_output`。

#### 1. `last_hidden_state` (每个字的向量)

*   **形状 (Shape):** `(batch_size, sequence_length, hidden_size)`
    *   例如：你输入“我爱你”，加上首尾特殊标记，长度是 5。BERT 维度是 768。形状就是 `(1, 5, 768)`。
*   **它是怎么来的？**
    *   它是 BERT 模型的**最后一层 Transformer 的直接输出**。
    *   在这个过程中，每一个字都经过了“自注意力机制（Self-Attention）”的洗礼。也就是说，“爱”这个字的向量里，已经融合了“我”和“你”的信息。
*   **有什么意义？**
    *   这是**上下文相关的词向量 (Contextualized Word Embeddings)**。
    *   **举个例子：** “苹果”这个词。
        *   在句子“我爱吃苹果”中，`last_hidden_state` 里“苹果”的向量代表**水果**。
        *   在句子“苹果发布了新手机”中，`last_hidden_state` 里“苹果”的向量代表**科技公司**。
        *   *以前的模型（如 Word2Vec）做不到这一点，BERT 做到了。*
*   **适用场景：** 需要对每个字进行操作的任务，比如**命名实体识别**（判断哪个词是人名）、**问答系统**（找出答案的起止位置）。

#### 2. `pooler_output` (整句话的向量)

*   **形状 (Shape):** `(batch_size, hidden_size)`
    *   例如：`(1, 768)`。不管句子多长，最后都浓缩成这一个向量。
*   **它是怎么来的？**
    *   BERT 在处理句子时，会强制在句首加一个特殊标记 **`[CLS]`** (Classification Token)。
    *   `pooler_output` 的计算过程是：
        1.  取出 `last_hidden_state` 中的第一个向量（也就是 `[CLS]` 对应的向量）。
        2.  让它通过一个全连接层（Linear Layer）。
        3.  再经过一个 Tanh 激活函数。
    *   *简单理解：`[CLS]` 就像是全班的班长，他站在第一个位置，不干别的，专门负责汇总全班同学（整句话）的信息。*
*   **有什么意义？**
    *   它代表了**整个句子的语义信息**。
*   **适用场景：** **文本分类**（情感分析）、**句子回归**（比如你之前的**预测手语帧数**任务）。

> **老师的特别提示（进阶技巧）：**
> 虽然 `pooler_output` 设计初衷是代表整句，但在实际应用中，很多人发现直接把 `last_hidden_state` 里所有字的向量取**平均值 (Mean Pooling)**，往往比直接用 `pooler_output` 效果更好。你以后优化模型时可以试试这个技巧。

---

### 二、 我们什么时候考虑使用 BERT？

只要你的任务满足以下 **3 个条件**，BERT 通常是首选：

1.  **任务类型是“理解”而非“生成”：**
    *   ✅ **适合：** 文本分类、情感分析、实体抽取、句子相似度匹配、回归预测（你的任务）。
    *   ❌ **不适合：** 纯文本生成（如写小说、翻译）。虽然 BERT 能做，但 GPT 或 Encoder-Decoder 模型（如 T5, Bart）更擅长生成。
2.  **数据量小：**
    *   你手头只有几千条标注数据？没问题。BERT 已经在大规模语料上“读过万卷书”了，你只需要给它几千条数据做“岗前培训”（Fine-tuning），它就能工作得很好。
3.  **对精度要求高，对速度要求不苛刻：**
    *   BERT 模型比较大，推理速度比传统的 LSTM 或 CNN 慢。如果你需要每秒处理几万条数据，可能需要用轻量版的 BERT（如 DistilBERT）或者传统模型。

**回到你的场景（手语帧数预测）：**
你**非常适合**使用 BERT。因为你的输入是文本，目的是“理解”文本的长度和语义复杂度，输出是一个数值。这是典型的 NLU（自然语言理解）回归任务。

---

### 三、 为什么这种模型表现得这么好？

BERT 之所以能横扫 NLP 榜单，主要归功于三个核心原因：

#### 1. 上下文感知 (Contextualization) —— 不再断章取义
以前的模型（如 Word2Vec）是静态的，一个词对应一个固定的向量。
BERT 是动态的。正如前面“苹果”的例子，BERT 能根据上下文精准地理解同一个词在不同语境下的含义。这对于理解人类语言的歧义性至关重要。

#### 2. 双向注意力 (Bidirectionality) —— 真正的“上帝视角”
*   **LSTM/RNN:** 像读书一样，只能从左往右读。读到后面时，前面的记忆可能模糊了；而且读前面的词时，完全不知道后面会发生什么。
*   **BERT:** 它是**Transformer Encoder**，它使用了**Self-Attention**机制。它在看句子中的任何一个字时，都**同时**看到了这句话里所有的其他字。
    *   比如理解“银行”时，它能同时看到前面的“去”和后面的“存钱”，从而瞬间确定这是金融机构。这种“上帝视角”让它捕捉特征的能力极强。

#### 3. 预训练+微调 (Pre-training + Fine-tuning) —— 站在巨人的肩膀上
这是 BERT 最伟大的贡献。
*   它在维基百科等海量数据上通过“完形填空”学会了语言的语法、句法和常识（这就是**预训练**）。
*   当你拿到模型时，它已经是一个“语言学博士”了。你只需要教它一点点你的专业知识（比如手语数据的规律），它就能迅速掌握。这比你从零开始训练一个模型要快得多，效果也好得多。

### 总结

*   **`last_hidden_state`** 是**每个字的深度特征**，适合细粒度任务。
*   **`pooler_output`** 是**整句话的浓缩特征**，适合分类和回归任务（你现在用的就是这个）。
*   BERT 之所以强，是因为它像人一样，能结合上下文**双向**地理解文字，并且拥有海量的**通识知识**储备。

希望这次的讲解能帮你彻底“通关” BERT 的理论基础！如果想深入代码实现细节，随时告诉我。