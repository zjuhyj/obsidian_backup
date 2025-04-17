
## Input

- 输入非一个向量，而是<span style="color:red;">一组长度会改变的向量</span>
	- ![[Pasted image 20250415155612.png|450]]

	- ![[Pasted image 20250415160138.png|450]]
		- One-hot Encoding
			- 缺陷：此表示假设所有词汇向量间均无关系，即所有向量互相正交
		- **Word Embedding**
			- 词汇向量之间有语义联系
	
	- ![[Pasted image 20250415161213.png|450]]
	- ![[Pasted image 20250415193019.png|450]]

## 2 Output

- ![[Pasted image 20250415193736.png|450]]
	- seq2seq：如翻译
	- 第一种：Sequence Labeling

## 3 Sequence Labeling

- ![[Pasted image 20250415194852.png|450]]
	- 两个saw词性不同，但对于FCN来说输入相同输出相同
		- ➡能否考虑上下文关系
			- 局部窗口
				- 将前后的几个向量，一起作为输入
				- 有些任务需要考虑整个seq
			- 全局窗口
				- 需要提前统计最长的序列长度（输入有长有短）
				- FCN参数量会很大，且容易过拟合
		- 更好的方法：self-attention


## 4 Self-attention
 
- ![[Pasted image 20250415195324.png|450]]
	- 黑色框框：**考虑了整个seq的资讯**的向量
	- self-attention可以<u>叠加多次</u>
		- self-attention 可以和FCN交互使用
			- self-attention：处理整个seq资讯
			- FCN：专注处理某一位置资讯
		- ![[Pasted image 20250415195534.png|400]]
			- 之前有论文提到过类似架构，但在《Attention is all you need》中把self-attention发扬光大
	- ![[Pasted image 20250415195950.png|450]]
		- a：向量
		- b：均考虑了所有a输出

---

- ![[Pasted image 20250415200336.png|450]]
	- 根据$a^1$，找出seq中与$a^1$相关的其他向量，哪些部分是重要的，对于判断$a^1$属于哪个label有关
	-  self-attention如何决定两个向量间的相关性？
		- ![[Pasted image 20250415200728.png|450]]
		- 常用左边的方法
- ![[Pasted image 20250415200957.png]]
- ![[Pasted image 20250415201116.png]]
	- 也会自己和自己计算关联性，且很重要（可以自己尝试一下）
	- Soft-max可以换成其他function，结果可能会更好
	- 到此步：已知哪些向量与$a^1$相关，下一步根据关联性抽取重要资讯
- ![[Pasted image 20250415201357.png]]
	- 谁的attention score越大，$b^1$与谁的$v$越接近
- ![[Pasted image 20250415202153.png]]
	- $b^1$到$b^4$ <span style="color:red;">不需要依序产生</span>，是**同时被计算**出来的

---
- 矩阵乘法角度
	- ![[Pasted image 20250415203326.png]]
	- ![[Pasted image 20250415203848.png]]
	- ![[Pasted image 20250415215057.png]]
		- 图中$\hat{a}$打错了，应该为$a^{\prime}$

- 矩阵过程总结
	- ![[Pasted image 20250415215223.png]]
	- 只有$W^q,W^k,W^v$是需要学习的参数

---

## 5 Multi-head Self-attention

- head数目：超参
- 一些场景中，较多的head数目可能会取得更好的结果
- ![[Pasted image 20250415215935.png]]
	- 假设<u>有两种不同的相关性</u>，所以设置了两个head
	- 上标的第二个位置表示：第几个head
- ![[Pasted image 20250415220123.png]]

## 6 Positional Encoding

- self-attention对于每个位置的操作相同，没有位置的资讯
- 进行self-attention时，若位置的资讯是需要的，可以将其加入
- ![[Pasted image 20250415221200.png]]
	1. 人为设定$e^i$
		- 问题：实际长度可能大于设定长度
		- 《Attention...you need》中没有这个问题，其通过一个神奇的规则，一个sin、cos的function产生$e$
		- <mark class="highlight">尚待研究的问题</mark>，可以创造新的方法，设定自己的$e^i$
			- 相关研究论文：
				- ![[Pasted image 20250415221341.png]]
				- 图横着看，每一个row代表一个position
	2. 从数据学习$e^i$

## 7 Application
- ![[Pasted image 20250415221729.png|450]]
- 语音识别
	- ![[Pasted image 20250416090823.png|450]]
		- 1s的语音有100个向量，长度很可观，计算量和存储量会很大
		- **Truncated Self-attention**：只看一个小的范围，范围由人设定
		- 一些场景并不需要整个seq的资讯，只需要一定范围内的即可
- 影像处理
	- ![[Pasted image 20250416091052.png|450]]
		- 每一个pixel看作一个3-d的向量
	- ![[Pasted image 20250416091127.png|450]]
		- <u>Self-attention用在影像处理上很常见</u>

### 影像处理：Self-attention v.s. CNN

- ![[Pasted image 20250416091446.png|450]]
	- Self-attention自己找到相关的pixel，即learnable receptive field
- ![[Pasted image 20250416091523.png|450]]
	- 该paper从数学上严谨说明，**CNN是Self-attention的特例**

---
- Self-attention：felxible model
- flexible model需要更多的data，否则容易overfitting
- small model，比较 limited model 在data较小时，不容易overfitting。限制设置的好也会有不错的结果

- ![[Pasted image 20250416092332.png|450]]
	- 将影像裁剪成16×16个patch，称作word
	- M：million张图像
	- Self-attention：弹性更大，在训练资料少时容易overfitting，训练资料大时效果好
	- CNN：弹性小，在训练资料少时效果较好，训练资料大时无法更好
	- <u>Self-attention、CNN可以一起用</u>

### Self-attention v.s. RNN

- RNN：很大角色已被Self-attention取代
	- input：seq，与Self-attention相同
- ![[Pasted image 20250416093304.png]]
	- FC：FCN
	- RNN：也可以是双向的，即可以考虑整个seq
	- Self-attention加上什么东西后可以变成RNN
	- RNN学习视频链接

### Self-attention for Graph

- ![[Pasted image 20250416093613.png]]
	- 关联性不需要机器找出，可以根据edge计算，只计算有edge相连的
	- Self-attention用在Graph上是某类型的GNN
	- GNN学习视频链接，水也是很深

## To Learn More（缺陷

- ![[Pasted image 20250416094039.png]]
	- Self-attention<mark class="highlight">最大问题</mark>：运算量很大。
		- 未来重点 #缺口 
	- 有时候讲transformer，广义上就是Self-attention
	- 各种变形都是：xxformer
		- 横轴：速度，纵轴：performance