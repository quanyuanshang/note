## Working with Text Data
### Embedding
pretrained model: Word2Vec
LLMs commonly produce their own embeddings that are part of the input layer and are updated during training.
### Tokenizing text
- preprocess text
- construct vocabulary
![[image-44.png]]
- use an <|unk|> token if it encounters a word that is not part of the vocabulary
- we add a token between unrelated texts.![[image-45.png]]

### Byte pair encoding(BPE)
the BPE tokenizer above encodes and decodes unknown words（break down into smaller subwords)![[image-46.png]]
### Data sampling with a sliding window
![[image-47.png]]
We are interested in returning two tensors: an input tensor containing the text that the LLM sees and a target tensor that includes the targets for the LLM to predict![[image-48.png]]

### Creating token embeddings
convert the token IDs into embedding vectors
we initialize these embedding weights with random values as a preliminary step.（optimization later）
the embedding layer is essentially a look-up operation that retrieves rows from the embedding layer's weight matrix via a token ID.
parameter size: # input token x # embedding dim
```
Parameter containing: tensor([[ 0.3374, -0.1778, -0.1690], [ 0.9178, 1.5810, 1.3010], [ 1.2753, -0.2010, -0.1606], [-0.4015, 0.9666, -1.1481], [-1.1589, 0.3255, -0.6315], [-2.8400, -0.7849, -1.4096]], requires_grad=True)
print(embedding_layer(torch.tensor([3])))
输出是
tensor([[-0.4015, 0.9666, -1.1481]], grad_fn=)
相当是输出第4行
```

![[image-49.png]]


### Encoding word positions
self-attention mechanism doesn't have a notion of position or order for the tokens within a sequence.
- it is helpful to inject additional position information into the LLM
- ![[image-51.png]]
- relative positional embeddings：the model learns the relationships in terms of "how far apart" rather than "at which exact position." --> generalize better to sequences of varying lengths

![[image-52.png]]
## self-attention mechanism
we want to compute the context vector:an embedding that contains information about x(2) and all other input elements x(1) to x(T)
- Their purpose is to create enriched representations of each element in an input sequence (like a sentence) by incorporating information from all other elements in the sequence
![[image.png]]
1. compute **attention score**:![[image-1.png| query is x2 and compute the dot product with other input vector]]
	a higher dot product indicates a greater degree of alignment or similarity between the vectors.
2. normalize the attention score to get **attention weight**
	1. three trainable weight W_k W_q W_v:These three matrices are used to project the embedded input tokens, x(i), into query, key, and value vectors![[image-2.png]]
	2. ![[image-4.png]]
	3. now compute the context vector as a weighted sum over the **value vectors.**![[image-5.png]]
	use the softmax function for normalization. This approach is better at managing extreme values and offers more favorable gradient properties during training.
> the softmax function ensures that the attention weights are always positive. This makes the output interpretable as probabilities or relative importance, where higher weights indicate greater importance.

3. calculating the **context vector** z(2) by multiplying the embedded input tokens![[image-3.png]]
![[image-6.png| overall pipeline]]

### Hiding future words with causal attention
![[image-7.png]]
![[image-8.png]]
- Masking additional attention weights with dropout
This method helps prevent overfitting by ensuring that a model does not become **overly reliant on any specific set of hidden layer units**.
dropout rate of 50%, half of the elements in the matrix are randomly set to zero. the values of the remaining elements in the matrix are scaled up by a factor of1/0.5 =2. 

### Extending single-head attention to multi-head attention
multi-head attention: creating multiple instances of the self-attention mechanism
crucial for the kind ofcomplex pattern recognition
![[image-9.png]]
splits the input intomultiple heads by reshaping the projected query, key, and value tensors and then combinesthe results from these heads after computing attention.

![[image-10.png]]
把(b, num_tokens, d_out)先转换为(b,num_tokens, num_heads, head_dim)进行多头同步运算最后再reshape回去。Additionally, we added a so-called output projection layer (self.out_proj) to MultiHeadAttention after combining the heads。
Why efficient?
we only need one matrix multiplication to compute the keys

## Implementing a GPT model
### layer normalization
- layer normalization: mean of 0 and a variance of 1 
	- speeds up the convergence to effective weights and ensures consistent, reliable training
	- typically applied before and after the multi-head attention module and before the final output layer
	- mean = out.mean(dim=-1, keepdim=True)dim=-1是确保在最后一个维度上进行均值计算。[batch_size, num_tokens, embedding_size]就是这个embedding_size keep dim是保证 the returned mean tensor would be a 2-dimensional vector [0.1324, 0.2170] instead of a 2×1-dimensional matrix [[0.1324], [0.2170]]
### GELU activations
GELU (Gaussian Error Linear Unit)
SwiGLU(Swish-Gated Linear Unit).
![[image-11.png]]![[image-12.png]]GELU allows for more nuanced adjustments to the model's parameters. GELU allows for a small, non-zero output for negative values. neurons that receive negative input can still contribute to the learning process, albeit to a lesser extent than positive inputs.
### Shortcut connections
purpose: mitigate the challenge of vanishing gradients
![[image-13.png]]

### overall gpt
**weight tying** that is used in the original GPT-2 architecture, which means that the original GPT-2 architecture is reusing the weights from the token embedding layer in its output layer.


### generating text
![[image-14.png]]
## pretrain
整体流程：
![[image-15.png]]

### Decoding strategies to control randomness
#### Temperature scaling
temperature scaling is just a fancy description for dividing the logits by a number greater than 0
A temperature of 1 divides the logits by 1 before passing them to the softmax function to compute the probability scores.![[image-16.png]]
that higher temperature values result in more uniformly distributed next-token probabilities, which result in more diverse outputs as it reduces the likelihood of the model repeatedly selecting the most probable token.

#### Top-k sampling
restrict the sampled tokens to the top-k most likely tokens and exclude all other tokens from the selection process by masking their probability scores, as illustrated in Figure 5.15.

### Saving model
save a model's so-called state_dict, a dictionary mapping each layer to its parameters, using the torch.save function as follows:
`torch.save(model.state_dict(), "model.pth")`
```
model = GPTModel(GPT_CONFIG_124M) model.load_state_dict(torch.load("model.pth")) model.eval()
```

Using torch.save, we can save both the model and optimizer state_dict contents as follows:
```
torch.save({ "model_state_dict": model.state_dict(), "optimizer_state_dict": optimizer.state_dict(), }, "model_and_optimizer.pth" )
```


```
checkpoint = torch.load("model_and_optimizer.pth") model = GPTModel(GPT_CONFIG_124M) model.load_state_dict(checkpoint["model_state_dict"]) optimizer = torch.optim.AdamW(model.parameters(), lr=5e-4, weight_decay=0.1) optimizer.load_state_dict(checkpoint["optimizer_state_dict"]) model.train();
```

## Finetuning
### instruction-finetuning
training a language model on a setof tasks using specific instructions to improve its ability to understand and execute tasksdescribed in natural language prompt
 undertake a **broader range of task**
![[image-18.png]]
格式调整+token padding（每个batch有自己的最长长度）+只保留一个end-of-text token（也就是50256）让模型学会在合适的时候生成 end-of-text，作为响应结束的标志，而不是去学习padding。其他的原本的50265就会变成

评估chatbot表现的方法
- ![[image-19.png]]
### classification-finetuning
deal for projects requiring precise categorization of data into predefined classes
如何处理不同长度的输入？
1. Truncate all messages to the length of the shortest message in thedataset or batch.
2. Pad all messages to the length of the longest message in the dataset orbatch.![[image-17.png]]

根据想要做的分类调整最后的输出维度，然后冻结前面的参数，只更新最后的参数。
比如输入由4个token但是为什么最终只会关注last token：the last token in a sequenceaccumulates the most information since it is the only token with access to data from all theprevious tokens.

