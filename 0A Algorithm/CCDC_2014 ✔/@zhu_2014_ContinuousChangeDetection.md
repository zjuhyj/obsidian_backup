---
NO: M008
title: "Continuous change detection and classification of land cover using all available Landsat data"
authors: Zhe Zhu, Curtis E. Woodcock
year: 2014
journal: 
DOI: 10.1016/j.rse.2014.01.011
---


<font size="5"><strong>标题</strong>：<strong>Continuous change detection and classification of land cover using all available Landsat data</strong></font>
<font size="5">使用<span style="color:red;"><strong>所有可用的 Landsat 数据</strong></span>对土地覆被进行<span style="color:red;"><strong>持续变化检测和分类</strong></span></font>

- 关键词：
	- CCDC 
	- Classification Change detection 
	- Time series Land cover 
	- Continuous Landsat 
	- Random Forest
- 主要内容：



![[Pasted image 20250312211006.png]]


## 1 方法概述/1.3. CCDC算法介绍

- **本文研究目标**
	- 寻求通过开发一种使用<span style="color:red;"><strong>所有可用 Landsat 数据</strong></span>的方法，来改进对新英格兰沿海地区土地覆被和土地覆被变化的监测
		- 所有可用Landsat数据：<span style="color:red;"><strong>有云影像中有许多清晰的观测值</strong></span>➡<span style="color:blue;"><strong>潜力</strong></span>：如Landsat尺度的物候学研究or高频次和高空间分辨率下的森林干扰持续监测 #潜力
			- ![[Pasted image 20250313104901.png|300]]
			- ➡由图可知：如果只使用云覆盖率小于 10% 的 Landsat 影像（过去常见），我们将忽略超过 50% 的净观测值。即使是云覆盖率超过 40% 的 Landsat 影像，也包含了近 20% 的晴朗观测值。
- **主要内容**
	- 利用所有可获得的Landsat4,5,7的TM/ETM+影像，开发<span style="color:red;"><strong>Continuous Change Detection and Classification (CCDC) 算法</strong></span>
		- continuous：”连续"
			- 能够<span style="color:red;">每次获取新的影像时检测到新的变化</span>
				- 如果随着新影像的获取而不断更新，那么这种方法就<span style="color:red;"><strong>接近于实时变化检测</strong></span>
			- 在覆盖影像时间范围内的任意给定时间生成土地覆盖图
		- 能够检测**多种类型**的土地覆盖变
---
## 局限

1. 算法的计算成本很高，并且需要大量的数据存储
	- 该算法在每次有新的观测值可用时都会更新时间序列模型，这样做的好处是模型估计更准确，但也大大增加了计算负载
	- 仅表面反射率和亮度温度数据就需要 500 GB 以上，总共需要 519 个 Landsat 图像
2. 需要高时间频率的清晰观察
	- 对于极端情况，如果清除观测值少于 15 个，则 CCDC 算法将无法初始化时间序列模型
3. 假设简单正弦模型能够估计各种土地覆被类型，这对于年内变化较大的土地覆被类型（例如农业）可能无效
4. 算法中使用的数据驱动阈值能够处理波士顿场景的多种土地覆被变化，但对于年际变化较大的地点，它可能会有问题
	- 在物候过程的时间上表现出高度可变性的半干旱地区可能特别具有挑战性，因为数据在一年中的某些时候会波动更大，此时需要更高的阈值。<u>能够在时间上更改的阈值可能会提供更好的准确性</u>  #缺口 
---
## 2 算法步骤/3. Methods

- CCDC算法分为三步：**数据预处理、连续变化检测、连续土地覆盖分类**

### 3.1. Image preprocessing

**3.1.1. Atmosphere correction** 
几何配准&大气校正

---

**3.1.2. Screening of cloud, cloud shadow, and snow** 
云、云阴影、雪的筛查

- <span style="color:red;">“clear”</span>：没有云、云阴影和雪（以下简称“晴朗”）的Landsat影像。
	- 本文档中翻译成**清晰**

（1）云、云阴影、雪的筛查：**两步法**

1. 通过<span style="color:red;"><strong>Fmask算法</strong></span>进行云、云阴影、雪的筛查
	- 基于对象的云、云阴影、雪筛查
	- 缺点：无法筛查<mark class="highlight">短暂的变化</mark>，如浓气溶胶、烟雾或<mark class="highlight">洪水</mark>，<u>可能与土地覆被变化相混淆</u>
		- ➡第二步：多时相分析补充筛选
	- ▲ 是<span style="color:red;"><strong>单时相</strong></span>的筛查

2. <span style="color:red;"><strong>多时相分析</strong></span>补充筛选
	- ▲ 利用上下文信息，解决Fmask漏检的部分
	- 基于Fmask算法筛选出的清晰观测值，构建<span style="color:red;"><strong>时间序列模型</strong></span>，比较时序模型的估计值和Landsat观测值，检测出异常值
	- 时序模型建模：
		- 时序模型建模：
			- ![[Pasted image 20250313160344.png|450]]
		- **参数估计**：
			- **鲁棒迭代重加权最小二乘法 Robust Iteratively Reweighted Least Squares** (RIRLS)
				- **优点**：能减少短暂变化，以及Fmask无法检测出来的云、云阴影、雪的影响
				- ▲ 核心思想：通过迭代过程为每个样本分配不同的权重，对异常值和噪声具有较高的**鲁棒性**
					- 每次迭代中，根据当前模型的残差（即预测值与实际值的差异）来调整样本的权重。
					- 残差较大的样本（可能是异常值）会被赋予较小的权重，而残差较小的样本会被赋予较大的权重。
				- 权重：权重函数+权重迭代
	- 异常值检测：<u>满足下图任一条件则被认为是异常值</u>，删除
		- ![[Pasted image 20250313161253.png|450]]

- 估计了 **Band 2** 和 **Band 5** 的时序模型
	- 云和雪使Band 2 更亮
	- 云阴影和雪使Band 5 更暗

---

- <span style="color:red;"><strong>CCDC模型启动条件</strong></span>
	- 当Fmask确定的**清晰观测值**总数达到**15个**
	- <u>前12个清晰观测值</u>：<span style="color:red;"><strong>Fmask+多时相分析</strong></span>【用于模型初始化】
		- Fmask+多时相分析
			- 目的：用于比较观测值和模型预测值，**判断是否存在异常值**
		- 数量选择原因：
			- 时间序列模型有4个系数，12个观测值（3倍于系数数量）能保证模型估计的稳健性和准确性
	- <u>后3个清晰观测值</u> ：<span style="color:red;"><strong>Fmask</strong></span> #困惑 【连续3个观测值确定变化】
		- **不进行多时相分析**
			- 原因：CCDC算法需要在时间序列的末尾保留额外的观测值，**以便模型能够响应地表变化**，从而<span style="color:red;"><strong>避免CCDC初始模型将末尾发生的变化误判为异常值</strong></span>
		- 数量选择原因：
			- CCDC算法使用<u>连续3个观测值</u>来确定像素是否发生变化，3个清晰观测值足以让时间序列模型响应地表变化。
	- 异常值剔除：
		- 前12个像素中的任何一个被识别为可能的云、云影、雪或其他短暂变化，则从清晰像素列表中剔除
- 由于计算限制和持续监控的要求：**仅在CCDC模型初始化期间使用多时相筛选算法**，对于初始化后的数据仅使用Fmask算法
	- ▲ 一旦时间序列模型完成初始化，CCDC进入持续监测模式。此时**仅使用Fmask算法**快速筛选新获取的观测值，以满足实时性需求并降低计算成本。

![[Pasted image 20250313215656.png|500]]

---

（2）**CCDC时序模型**

- 土地覆盖类型变化分为三类： #⭐
	1. **年度变化**（intra-annual change）
		- 由温度和降水等环境因素的**季节性模式**驱动的**植被物候**引起
	2. 渐变的**年内变化**（gradual inter-annual change）
		- 由气候变异性、植被生长或土地管理的**逐渐变化**或土地退化引起
	3. 突然变化（abrupt change）
		- 由森林砍伐、洪水、火灾、**昆虫**、城市化等引起

- <span style="color:red;"><strong>CCDC时序模型</strong></span>
	- 具有<u>季节性、趋势和断点</u>三个成分，用于捕捉上述的三类变化
	- 时序模型建模：
		- ![[Pasted image 20250314124235.png|450]]
		- <u>参数越多模型建模越准确，但也可能模型会去拟合噪声</u>
		- **参数估计**：基于剩余清晰的观测值进行**最小二乘法（OLS）**
			- 不适用RIRLS是因为当前已排除所有异常值，OLS更快更准确
		- ![[Pasted image 20250314124750.png|450]]
		- ![[Pasted image 20250314124807.png|450]]

---

（3）**连续变化检测**

- **基础**：将模型预测与清晰的卫星观测进行比较，以发现变化
- 数据点
	- 噪声因子在本质上往往是短暂的，而土地覆被的变化随着时间的推移更加持久
	- ➡CCDC基于连续图像（即，一组数据）识别土地变化
	- ➡基于以往研究，<u>由三个连续的时间点识别的土地变化是最佳的</u>

- 阈值
	- CCDC 使用<u>所有 Landsat 光谱波段</u>和<u><strong>数据驱动阈值</strong></u>（针对每个单独的像素进行调整）来检测多种类型的土地覆被变化
	- 每个 Landsat 波段的**观测值**和模型**预测值**之间的差异<span style="color:red;">按三倍 RMSE</span> 进行归一化
		- 选择3倍的原因：土地覆盖发生变化时，光谱信号通常与模型预测的偏差是 RMSE 的三倍以上
			 - Fig.6.  使用三倍 RMSE 的阈值进行连续变化检测的原因。 Fig.6 A：模型预测和变化发生前。Fig.6 B：模型预测和变化发生时。Fig.6 C：模型预测和变化发生后。
			- ![[Pasted image 20250317162057.png|800]]
			- Fig.6 A&C：没有土地覆被变化时，接下来的三个明确观测值始终在模型预测范围内（± 3 × RMSE）
- ➡变化识别公式：
	- ![[Pasted image 20250317165200.png|500]]
	- ![[Pasted image 20250317165207.png|500]]
	- <u>当有可用的新的清晰观测值时</u>，CCDC 算法会<span style="color:red;"><strong>更新时间序列模型</strong></span>（Eq. 4），向过程添加动态特征，使时间序列能够随时间进行调整 #困惑 
	- **不足**：CCDC算法<u>假设稳定土地覆盖的物候是一致的</u>，但由于年际差异的变化，这对于某些半干旱环境可能并非如此

---

- <span style="color:red;"><strong>CCDC初始化</strong></span>
	- CCDC初始化（模型估计开始）期间发生的土地覆被变化可能会使时间序列模型预测产生偏差，因此如果存在土地覆盖变化则删去，直至初始化阶段无可以检测到的变化
	- ➡<u>每个像素都有自己的模型初始化开始日期</u>
	- **用于初始化**的<span style="color:red;">前12个观测值</span>中检测变化的3种方法：
		- 对于稳定土地覆盖的像素，时间序列模型的斜率量级相对较小
		1. 异常斜率（发生在初始化期间）
			- 模型初始化时观测值偏离 RMSE 的三倍以上➡时间序列模型的斜率大于  $3 × RMSE_i/t_{model}$➡$ith$波段的斜率由$3 × RMSE_i/t_{model}$ 归一化，如果＞1，则判定为异常斜率（见Eq. 5）
			- ![[Pasted image 20250317165926.png|500]]
		2. 首次观测值的异常 & 3. 最后一次观测值的异常（发生在初始化始末）
			- 地物变化发生在初始化期**始末**，<u>可能不会导致斜率上异常明显（如突变的数据点较少时）</u>，但依旧会影响模型估计➡比较首尾观测值与模型预测值的差异：模型首尾的较少观测值的变化不会影响CCDC模型预测的效果➡步骤：
				1. 分别计算收尾观测值与预测值的**差**
				2. 将各个波段的**差**进行$RMSE_i$归一化（见Eq. 5）
				3. 如果Landsat所有波段的归一化**差**值的平均值＞1，则被认为是异常观测
	- 模型初始化完成后，它将用作<u>连续变化检测</u>和<u>分类</u>的基础

---

（4）**连续土地覆盖类型分类**

- 土地覆盖分类输入：<span style="color:red;">时序模型的系数</span>
	- 各像素在变化检测前后都有自己时序模型
	- ➡为各时序模型各时间段提供土地覆盖类型
- **不同土地覆盖类型的时序模型的形状不同**
	- Fig. 8A：森林类型➡已开发类别。
		- 两模型间隔被归类为**“受干扰”**，<u>过渡期间数据的巨大变化会阻止模型初始化</u>
		- <u>森林类型变为开发，时序模型形状差异明显</u>，尤其是波段4和波段6
			- 波段4减小；波段6增加：森林被开发&城市热岛效应
	- Fig. 8B：森林类型➡裸地。波段 5 & 波段 7 变化最显著
	- Fig. 8C：森林类型➡草地。可见光波段时序模型差异不大，但短波红外和热波段差异显著
	- Fig. 8D：森林类型➡农业。近红外和短波红外波段时序模型差异最大
	![[Pasted image 20250318151507.png]]
	![[Pasted image 20250318151657.png]]
	
- <span style="color:red;">时序模型的系数</span>
	- 时间序列模型获取的5个变量（$\overline{\rho}，a_1,b_1,c_1,RMSE$）作为土地覆盖类型分类的输入
		- ➡7个波段 × 5个变量 = <span style="color:red;">35个变量用于分类输入</span>
		- $\overline{\rho}$：各Landsat波段的每个时序模型中心的总体光谱值
			- $a_0$：常数；$c_1$：斜率
			- ![[Pasted image 20250319083831.png|450]]
			- ![[Pasted image 20250319090509.png|450]]
		- $a_1,b_1$：提供了年度（or 季节）模式的时间信息
		- $c_1$：衡量年内差异or趋势
		- $RMSE$：通过对Eq. 3进行OLS拟合得出
	- 不同土地覆盖类别的<u>参考站点</u>以上 5 个不同变量的的平均值
		- 可以看出：不同的土地覆盖类型的参数出现不同的形状
		- ![[Pasted image 20250319084353.png]]
		- ![[Pasted image 20250319084437.png]]
- <u>未检测到土地覆盖变化</u>的**像素的系数**用于训练Random Forest Classifier (RFC)随机森林分类器

## 3 示意图

![[Pasted image 20250319090541.png]]
![[Pasted image 20250319090555.png|450]]

## 4 重要摘录

### 1. Introduction

#### 1.1. Monitoring land cover change with remote sensing 土地变化检测

- Landsat：高空间分辨率，相对较低的时间频率
	- 使用Landsat的**缺陷**：相对较低的时间频率
		- 过境美国区域传感器会打开，其他区域不一定，数据更少
	- ➡<span style="color:red;"><strong>双时相检测</strong></span>：
		- 大多数使用Landsat进行变化检测的算法通常使用双时相</strong></span>
		- **缺点**：对双时相的数据要求高，<u>需要长时间才能采集到一年同一时间的理想图像</u>
	- ➡为了在较短时间间隔内进行变化检测：<span style="color:red;"><strong>时序检测（通常用年度影像）</strong></span>
		- **缺点**：理想图像应在同一季节且无云，通常<u>提供的最佳结果是年度或两年一次的变化结果</u>。即，<mark class="highlight">只能检测到年度变化</mark>。
- MODIS：中等空间分辨率，**更高的时间频率** 
	- 粗分辨率导致检测的能力受限

- ➡<span style="color:blue;"><strong>需要一种变换检测算法</strong></span>：使用<u>精细的空间分辨率数据</u>（如 Landsat）& <u>尽可能多的观测值</u>来准确快速地检测土地覆被变化。 #缺口 

#### 1.2. Land cover classification  土地覆盖类型分类

- <span style="color:red;"><strong>多时相图像</strong></span>有助于提高分类准确性
	- 缺点：
		1. 需要都是无云图像
			- 对于多云区域的某个位置需要等待好几年
			- ➡ 大多数基于Landsat的土地覆盖图都是每5/10年一次绘制 #缺口 
		2. 通常**假设**不同影像之间的时间间隔内<span style="color:red;"><strong>没有土地覆被变化</strong></span> #缺口 
			- <u>对于<strong>较长时间间隔</strong>or<strong>变化频繁</strong>的区域并不有效</u> 
			- 传统方法生成的土地覆盖图由于误差比土地覆盖变化大，无法用于变化检测 ➡ <span style="color:red;"><strong>分类过程产生的误差对于土地变化检测带来严重影响</strong></span>

- ➡<span style="color:blue;"><strong>需要一种分类算法</strong></span>： #缺口 
	1. 增加土地覆盖类型的<u>时效性</u>
		- *新算法应在更短的时间间隔内生成土地覆盖图，从而使其保持更高的时效性*
	2. 适用于<u>多种覆盖类型常见</u>的区域
		- *新算法需要处理这些频繁的变化，避免因变化而导致的分类错误*
	3. <u>不同土地覆盖图之间具有可比性</u>，以便识别变化
		- *新算法需要确保不同时期的土地覆盖图分类误差较小，不影响进一步的变化检测*

#### 1.3. Introduction to the CCDC algorithm CCDC算法介绍

- 2008 年Landsat开放

### 2. Data and study area

- 研究区域：
	1. 包括能获得野外实测数据的区域
	2. 多覆盖变换类型
	3. 很难找到无云的影像

- 数据：分布均匀，生长季节（7 月、8 月和 9 月）略多

- <span style="color:red;">训练数据</span>：Landsat影像所覆盖的时间范围内的任何给定时间收集的<span style="color:red;">土地覆盖类型参考数据</span>
	- 土地覆盖参考数据：曾用于校准HERO马萨诸塞森林监测计划（MaFoMP）2000年土地覆盖产品，基于2005-2007年航空照片和多次实地考察创建。
	- 参考点尺寸：60✖60m
	- 共有16个类别，总计有8220个参考点
		- 原始数据中水被分为两个土地覆盖类别（浅水和深水），简化合并一个类别：水。
	- ![[Pasted image 20250313135134.png]]


### 3. Methods
/ 见算法步骤

