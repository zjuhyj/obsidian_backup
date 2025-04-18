- OLS-MOSUM：The ordinary least squares (OLS) residuals-based MOving SUM
- 目的
	- 用于检测时间序列中是否存在结构断点（如均值或回归系数的突变），基于普通最小二乘（OLS）残差的移动累积和。

- 具体步骤*
	1. 拟合初始模型：
	    - 假设原假设为“无结构变化”，使用**OLS**对整个时间序列拟合一个全局线性模型：
	        - $$Y_t=X_t\beta+\epsilon_t$$
		        - $X_t$：设计矩阵，用于表示自变量的数据
			        - 每一列对应一个自变量，每一行对应一个观测值
				- $\beta$：系数
				- $\epsilon_t$：残差
	2. 计算残差：
	    - 得到残差序列 $$\widehat{\epsilon}_t=Y_t-X_t\widehat{\beta}$$

	3. 构建MOSUM统计量：
	    - 定义窗口宽度 $h$（通常取序列长度的10%-20%）
	    - 计算每个时间点 $t$ 的滑动窗口残差累积和，即在一定窗口宽度内，从某个起始点到当前点的残差平方和：
	        - $$MOSUM_t=\frac{1}{n\sqrt{\sigma}}\sum_{k = t−h+1}^{t}\widehat{\epsilon}_k​$$
	        - $n$ ：序列长度
			- $\widehat{\epsilon}$ ：残差标准差
	        
	4. 判断是否拒绝原假设：只需以下一种即可
		1. **临界值**
			- 临界值：根据显著性水平（通常为0.05）和概率分布表确定的（如正态分布、t分布等）
			    - 对于OLS-MOSUM检验，通常使用**正态分布**或**t分布**来确定临界值。
			1. 计算临界值：用统计表或软件工具来查找
				- 将计算出的检验统计量与临界值进行比较
			2. 如果检验统计量超过临界值，则拒绝原假设。否则，不拒绝原假设。
	    2. **P值**
		    - P值：在<u>原假设（无结构断点）成立</u>的前提下，观测到的**MOSUM统计量**或更极端值出现的**概率**。
			1. 计算P值：用统计表或软件工具来查找
				- 具体来说，P值是根据MOSUM统计量在原假设下的分布计算的
				- 示例：如果计算出的MOSUM统计量为**2.5**，对于标准正态分布，P值可以计算为：
				    - $$P-value=2×P(Z>2.5)=2×(1−\phi(2.5))$$
						- $\phi$是标准正态分布的累积分布函数
			2. 将计算出的P值与显著性水平（如0.05）进行比较。如果P值小于显著性水平，则拒绝原假设。否则，不拒绝原假设
