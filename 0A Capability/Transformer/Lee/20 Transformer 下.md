
## 1 Decoder

- ![[Pasted image 20250416152927.png|450]]
- Dedoder有两种，<u>常用Autoregressive</u>

### 1.1 Autoregressive（AT

- ![[Pasted image 20250416153633.png]]
- ![[Pasted image 20250416153800.png]]
	- Decoder如果产生错误的输入，会被自己吃进去
	- 会“一步错，步步错”吗？有可能
- ![[Pasted image 20250416153934.png]]
- ![[Pasted image 20250416154100.png]]
	- 将灰色部分遮住，Encoder与Decoder相似
	- **Masked Multi-Head Attention**
		- ![[Pasted image 20250416154208.png]]
			- 产生各个$b^i$，能看到所有的$a^i$
			- ![[Pasted image 20250416154407.png]]
		- ![[Pasted image 20250416154335.png]]
			- 右边被遮住
			- 产生$b^1$时，只能看到$a^1$
			- ![[Pasted image 20250416154655.png]]
			- Why Masked? <span style="color:red;">Decoder输出是是一个一个输出</span>，先有$a^1$再有$a^2$再有$a^3$、$a^4$
			- Self-Attention：$a^1$到$a^4$是一次性都有的，一次性都输入的

---

- ![[Pasted image 20250416154950.png]]
	- 机器不知道output什么时候停下
- ![[Pasted image 20250416155145.png]]
	- BEGIN 只在 input 出现，END 只在 output 出现
	- BEGIN 和 END 用的符号可以相同
	- ![[Pasted image 20250416155437.png]]
		- END几率最大时，seq输出结束

### 1.2 Non-autoregressive（NAT

- ![[Pasted image 20250416155840.png]]
	- NAT：输入几个BEGIN，输出几个字
	- NAT：并行化，一个步骤就能输出所有句子。Self-Attention出现后才有NAT。
- ![[Pasted image 20250416160324.png]]
	- NAT：研究热门，具有并行优势，但表现不如AT
- NAT学习视频链接，也是大坑

## 2 Encoder与Decoder传递（cross attention

- ![[Pasted image 20250416160611.png]]
	- Encoder与Decoder间的资讯传递：即红框部分
- ![[Pasted image 20250416160814.png]]
	- ![[Pasted image 20250416161443.png]]
		- 上图：Encoder，横轴时间，向量图
		- 下图：Decoder，值是分数
		- 从左上到右下，考虑的声音信号也是由左向右
	- Transformer之前，已有Cross Attention机制
	- 先有Cross Attention，再有Self-Attention
-  ![[Pasted image 20250416161635.png]] 
	- Decoder不一定拿Encoder最后一层的输出作为输入
	- <u>链接方式可以各式各样</u>

## 3 Training

- ![[Pasted image 20250416162626.png]]
	- 与分类类似
- ![[Pasted image 20250416162851.png]]
	- 注意训练label 还要有END
	- 训练时，**Decoder输入是正确答案**

## 4 Training Tips

- 对seq2seq模型也适用

### 4.1 Copy Mechanism

- ![[Pasted image 20250416163306.png]]
	- 很多任务，不需要创造输出，而是从输入中复制一些东西出来
	- ![[Pasted image 20250416163327.png]]
		- 训练此类模型至少需要百万篇文章
	- Copy Mechanism相关学习视频链接

### 4.2 Guided Attention


- Guided Attention领导机器attention，<u>强迫attention有一个固定的样貌</u>
	- Guided Attention在很多任务都用的上
		- 尤其是在语音合成、语音辨识中，在此类任务中，机器没有读到认到某一部分会不太能被接受
- ![[Pasted image 20250416164029.png]]
	- 图中为语音合成示例
		- 上图是正常顺序，应该是从左到右
		- 下图顺序颠三倒四，错了
	- 如何guide？也是一个大坑要细讲，ppt右上角有一些关键词可供展开

### 4.3 Beam Search

- ![[Pasted image 20250416164851.png]]
	- 假设Decoder只能有2种输出
	- 绿色路径：虽然一开始的选择比较差，但后面的结果是比较好的。较之红色路径，应该选绿色路径
	- 无法暴搜所有路径
		- 以中文为例，每一个转折点都有4k个路径，无法暴搜

- Beam Search：用比较有效的方法找一个估测、不是很精准的solution
	- 大家自行学习google

- <u>Beam Search并非一定有用</u>
	- ![[Pasted image 20250416165628.png]]
	- 需要创造性的任务，Beam Search效果不太好。
		- 如语音合成，**Decoder需要加入一些noise**。
			- 在ML中，训练中会加入noise，但在测试时不会。
			- <u>TTS测试时需要加入一些noise效果才会好</u>，很神奇
	- <span style="color:red;">有时候，Decoder需要一些随机性，效果才会好</span>

### 4.4 Optimizing Evauation Metric

- ![[Pasted image 20250416170711.png]]
	- minimize cross entropy 不一定可以maximize  BLEU score
		- 两者只有一点点的关联，是两个不同的数值
		- 右图。测试时，将Decoder输出的**句子**与正确的句子作比较，计算BLEU score
		- 左图。训练时，每一个词汇是分开考虑的，minimize cross entropy
	- 实际，**选择最好的model时**，并非按照 cross entropy，而是<mark class="highlight">挑BLEU score最高的</mark>。
	- 能否在training用minimize （-1）× BLEU score，即maximize  BLEU score?
		- BLEU score 很复杂，用作Loss无法微分
		- **当optimization Loss Function无法解决时**，可以尝试<span style="color:red;"><strong>RL</strong></span>
			- 将BLEU score当作reward，Decoder当作agent
			- 比较难的做法，并没有特别推荐
- ![[Pasted image 20250416171458.png]]
	- 测试时，Decoder是自己的输出，可能看到错误的东西；训练时，Decoder看到的是完全正确的。**即exposure bias**。
	- 如果Decoder在**训练**时只看到正确的，在测试时看到错误的会”一步错，步步错“。
	- ➡解决：<u>给Decoder的输入加一些错误的东西</u>
		- ![[Pasted image 20250416171515.png]]
		- 会伤害到transformer并行化能力

## 5 Transformer 架构图

- ![[Pasted image 20250416171634.png]]