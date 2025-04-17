- 开始介绍神经网络架构 
- Convolutional Neural Network（CNN）：处理图像

---

 ## Image Classification
 
- 假设<u>输入的图像大小固定</u>
	- ![[Pasted image 20250415094016.png|450]]
	- tensor：超过2维的矩阵
		- ![[Pasted image 20250415094313.png|400]]
- ![[Pasted image 20250415094420.png|450]]

## CNN

### 1 Story v1

#### 1.1 Observation 1：接受域（感受野
- ![[Pasted image 20250415094625.png|450]]
- ![[Pasted image 20250415094730.png|450]]
- ![[Pasted image 20250415094921.png|450]]
	- **Receptive field**：感受野、接受域
- ![[Pasted image 20250415095104.png|450]]
	- 问题回答：都可以，Receptive field完全由你设计
---
- ![[Pasted image 20250415095625.png|450]]
	- kernerl size：一般取3×3
	- stride：一般取1、2，希望有高度重叠以侦测特征
	- padding：补值

#### 1.2 Observation 2：共享参数

- ![[Pasted image 20250415095909.png|450]]
- ![[Pasted image 20250415100400.png|450]]
	- 如何共享由自己决定

---
- ![[Pasted image 20250415100644.png|450]]

#### 1.3 总结

- ![[Pasted image 20250415102924.png|450]]
	 - model bias 更大不一定是一件坏事，如果model bias过小，在xx（se pa ri ty，台湾口音）很高的时候，很容易过拟合
	 - FCN可以做各种事情，但在图像上可能做的不是很好
	 - CNN专门对影像设计，在影像上用的较好，其model bias对影像来说不是问题，但在影像之外则需要小心


### 2 Story v2

#### 2.1 过滤器（卷积核

- ![[Pasted image 20250415103929.png|450]]
	- ![[Pasted image 20250415104125.png|400]]
	- ![[Pasted image 20250415104255.png|400]]
	- ![[Pasted image 20250415104335.png|400]]
	- ![[Pasted image 20250415104929.png|400]]
	- ![[Pasted image 20250415104805.png|400]]
		- <mark class="highlight"> filter 的高度 = 影像的 channel </mark>
		- 第一层：输入影像若彩色（RGB）则 filter 高度为3，若黑白则为1
		- 第二层：如果第一层有64个filter，则第一层输出的feature map有64个channel，那么第二层的filter高度应为64
	- - 叠得越深，对应的接受域越大

### 3  v1 v2 比较

- ![[Pasted image 20250415110103.png|450]]
- ![[Pasted image 20250415110112.png|450]]
- ![[Pasted image 20250415110215.png|450]]

### 4 Observation 3：池化

- ![[Pasted image 20250415110443.png|450]]
- ![[Pasted image 20250415110559.png|450]]
	- Max Pooling：选择最大的那一个
	- Pooling：把图片变小，**减少运算量**
	- ![[Pasted image 20250415152922.png|450]]
- Pooling会对结果造成一定影响，**需要考虑是否要使用**
- 现在计算资源的升级，使得<u>很多模型不再使用Pooling</u>

### 5 The whole CNN

- ![[Pasted image 20250415153244.png]]

## Application: Playing Go

- ![[Pasted image 20250415153822.png|450]]
- ![[Pasted image 20250415154012.png|450]]
- ![[Pasted image 20250415154438.png|450]]

---

- CNN用于语音、文字，与用于图像不同
	- **要根据具体场景设计**
	- ![[Pasted image 20250415154541.png|400]]



## To learn more...

- **CNN无法处理影像放大、缩小、旋转的问题**
	- 图片放大后，CNN不一定能判别
	- 其拉长成向量后，输入不同
	- ➡训练前，需要做<mark class="highlight">data augmentation </mark>
		- 对影像进行放大、裁剪、旋转，输入到CNN中，使其学习不同大小、不同角度的pattern
	- ![[Pasted image 20250415154720.png|450]]


