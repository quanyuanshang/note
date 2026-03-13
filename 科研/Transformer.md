## test

- seq labelig: eg：词性分析。如何联系上下文？![[png/image.png|给一个window、但是windows再大也不能根据输入seq来盖住，因为seq长度会变化]]
	- self-attention：每个输入向量经过selfattention 考虑整个seq，之后再进fc![[png/image-1.png]]
	- attention怎么计算两个向量的相关度？![[png/image-2.png|seq乘上矩阵之后做点乘]]![[png/image-4.png]]![[png/image-5.png|b1-4是同时计算出来的]]![[image-6.png|矩阵并行计算]]![[image-7.png]]![[image-9.png]]
	- multihead self-attention：不同的head找不同的相关性。![[image-10.png| 不同head体现在kq求出来之后再成两个不同参数的W变成q1q2]]
	- 目前为止位置信息都没有考虑进来，接下来就就是encode position![[image-11.png|如何encode是自己定的或者从机器学出来的]]
	- truncated self-attention：![[image-12.png|对于像语音这样很长的序列，可能不需要关注整个序列]]
	- self-attention for image:将每个pixel看成三维向量。
	- 和CNN的区别：CNN只会考虑receptive field里的秩序而self attention考虑整张图的。self-attention弹性比较大，相当于自动学习receptive-field，需要更多的数据量来训练。CNN是selfattention的个例，更为简单，训练资料更小表现更好。
	- 和RNN区别：![[image-13.png|RNN相距太远的序列影响就会变小，没办法平行处理所有Vector]]
	- 在graph上的应用：![[image-14.png]]
	
- seq2seq:输入是N个向量，输出是N‘个label(N'是自己决定的)。用于语音识别、语音翻译等，让模型自己决定输出多少长度的序列。文字到语音。chatbot。multi-label classification机器自己决定每个东西属于多少class
	- encoder：给一排向量输出一排向量。![[image-15.png]]![[image-16.png|每个block拆解，self+res+layer=norm（维度上）+FC+res+norm]]![[image-22.png|总览]] 
	- decoder：加入stop token让它自己决定产生多少![[image-20.png|先根据token，过Softmax求vocabulary里概率率最大的，再作为onehot加入decoder以此类推、但会看到错误的输出导致之后的错误|557x396]]
		- mask self attention：![[image-19.png|由于decoder是按照前面产生的产生后面，所以要有mask]]
		- AT &NAT：![[image-21.png]]
		- cross-attention：![[image-23.png|decoder产生q，与encoder产生的k形成v进入FC]]

# train
- Teacher Forcing
希望每一次做完Softmax分类，这样得到的cross entropy最小。所以在模型训练阶段，给到decoder的是正确答案。![[image-24.png]]
- Copy Mechanism：有的时候机器需要复述用户内容
- Guided Attention：机器自主学习的时候有时候会漏掉信息。在语音辨识、语音合成很重要。比如限制机器Attention从左到右（设定一个score）
- Beam Search：![[image-25.png|对于答案明确的任务（语音辨识）但对发挥创造力的就不太好]]
- OPtimization：训练的时候用cross entropy是因为好算微分，但是bleu score不行。
- scheduled sampling：训练的时候给一些错误的信息，从而避免test时候一步错步步错。

# 自回归模型：
像 GPT-2、TransformerXL、XLNet 这类模型，每次只预测下一个词（token），并且生成的词会作为输入继续影响后续预测。