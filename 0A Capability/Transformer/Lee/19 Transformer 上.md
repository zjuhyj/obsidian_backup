
- transformer：变形金刚
- BERT：transformer与其关系很大

---
## 1 Seq2seq

- transformer：一个**Seq2seq**的model
- input：seq，output：3种情况（见[[16&17 自注意力机制Self-attention]]
- ![[Pasted image 20250416095109.png]]
	- 为什么不是1+2替代3：世界上很多语言没有文字
- eg. 台语翻译
	- ![[Pasted image 20250416095358.png|400]]
- eg. 语音合成：文字到语音
- eg. Chatbot 聊天机器人
	- ![[Pasted image 20250416095800.png|400]]

### 1.1 Application

- ![[Pasted image 20250416101656.png|400]]
	- Seq2seq在NLP上应用<u>很广泛</u>
	- 很多NLP任务都可以想成是QA任务
- NLP各种任务，往往**特制化模型**能得到更好的结果
	- NLP各种任务相关模型学习链接，基于语音的某些特性

---
- ![[Pasted image 20250416101945.png|450]]
	- 输出似乎是树状结构，可以转成seq
- ![[Pasted image 20250416102018.png|450]]
	- ![[Pasted image 20250416102129.png|400]]

---

- Multi-class：属于哪一个class
- Multi-label：可以属于多个class
- Multi-label：使用Multi-class的方法输出分数最高的前三名，可能行不通。
	- 每篇文章对应的class的数目不一样
- ![[Pasted image 20250416102504.png|450]]

---

- ![[Pasted image 20250416102523.png]]

---

➡<mark class="highlight">Seq2seq model is greatly powerful !!</mark>

### 1.2 Seq2seq 原理

- ![[Pasted image 20250416102829.png]]

#### 1.2.1 Encoder

- ![[Pasted image 20250416102951.png]]
- ![[Pasted image 20250416103053.png]]
	- every block有好几个layer

---
- Transformer
	- ![[Pasted image 20250416105328.png]]
		- b：原input
		- norm中，式子右边的分子$x^{\prime}_i$应为$x_i$
		- 紫色箭头：自己加的，表明右边接着左边
		- 最后的红框，才是一个block的输出
		- Layer Norm：不考虑batch的资讯
			- Batch Norm：<u>同一feature、dimension</u>  不同example计算均值和残差
			- Layer Norm：<u>同一example</u> 不同feature、dimension计算均值和残差
			- ![[Pasted image 20250416104417.png]]
	- ![[Pasted image 20250416105533.png]]
		- block会重复N次
		- **BERT**：Transformer的Encoder
	- 以上Transformer的Encoder按照原始论文讲解，并非最佳
		- 该Encoder设计可以改进
		- ![[Pasted image 20250416105654.png]]
			- （a）：原始Transformer
			- （b）：更换Layer Norm位置后的Transformer，效果更好
			- Transformer中，为什么Batch Norm不如Layer Norm，提出Power  Norm

#### 1.2.2 Decoder

- 见[[20 Transformer 下]]