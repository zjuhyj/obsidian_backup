
- 前置知识：[[16&17 自注意力机制Self-attention]]
- ![[Pasted image 20250417100603.png|450]]
	- Self-Attention有各式各样的变化

---

## 1 Self-Attention回顾

- ![[Pasted image 20250417100817.png]]
	- <span style="color:red;">痛点</span>：N×N计算量非常大
- ![[Pasted image 20250417101146.png]]
	- Self-Attention是一个**module**
	- 种种Self-Attention变形往往为图像服务

## 2 Self-Attention变形

### 2.1 Skip some calculations with human knowledge

- ![[Pasted image 20250417101534.png]]
	- 一些任务只需要看左右邻居而非整个seq
	- 灰色部分：0
	- 缺陷：similar with CNN，直接用CNN即可。
		- 能加快Self-Attention的计算，但不一定给你非常好的结果

- ![[Pasted image 20250417101633.png]]
	- 看比较远的邻居

- 前面两者attention  都是以某一点为中心

---

- 两种做法：上图，下图