---
NO: R002
title: "Forest disturbance characterization in the era of earth observation big data: A mapping review"
authors: Enmanuel Rodríguez Paulino, Martin Schlerf, Achim Röder, Johannes Stoffels, Thomas Udelhoven
year: 2024
journal: International Journal of Applied Earth Observation and Geoinformation
DOI: 10.1016/j.jag.2024.103755
---

#NO：R002

<font size="5"><strong>标题</strong>：<strong>Forest disturbance characterization in the era of earth observation big data: A mapping review</strong></font>

<font size="5">对地观测大数据时代的<span style="color:red;"><strong>森林干扰表征</strong></span>：制图综述</font>

- 关键词： 
	- Forest disturbance characterization  森林干扰特征
	- Spectral-spatial-temporal patterns  光谱-空间-时间格局
	- Earth observation big data  对地观测大数据
	- Systematic mapping review  系统制图综述
- 研究目标：
	- 本研究进行了制图综述，以阐明利用EO大数据进行森林干扰特征研究的最新进展
- 研究框架：
	- 按“**时空模式-预测因子-数据融合-分类模型-数据可访问性**”五维度分析

![[Pasted image 20250218144353.png]]

---
# 文献卡片

## 1 核心问题

- 主要内容：**回答&讨论5个问题**
	1. RQ1：<span style="color:red;"><strong>变化检测方法</strong></span>如何表征森林干扰的<strong>时空格局</strong>，即<span style="color:red;"><strong>空间&光谱-时间模式的类型</strong></span>
	2. RQ2：哪些<strong>空间-光谱-时间<span style="color:red;">预测因子</span></strong>对表征森林干扰很重要
	3. RQ3：多源EO数据的<span style="color:red;"><strong>数据融合</strong></span>方法
	4. RQ4：用于森林干扰表征的<span style="color:red;"><strong>分类算法</strong></span>
	5. RQ5：训练的数据和模型输出的可访问性

## 2 关键结论
1. 时空模式：
	- **光谱-时间模式**的使用率是空间模式的三倍
	- 基于轨迹分割的研究占主导
2. 预测因子：
	- **短波红外(SWIR)区域**的光谱变化幅度和干扰发生时间是最常用的预测因子，还包括**受干扰斑块尺寸**和**高程**的测量
3. 分类算法：
	- **随机森林**是最常用的分类器
	- 从基于专家的规则集到**深度学习**，显示出中等到先进的性能
4. 数据可访问性
	- 少于10%的研究提供了用于分析的数据集，阻碍了研究发展

## 3 未来工作

- RQ1：时空模式
	- 需进一步开发从**空间对象**中高校提取光谱-时间模式的算法
	- 使用先进的ML技术，例如<mark class="highlight">深度学习</mark>，在<span style="color:red;"><strong>端到端</strong></span>的学习框架中进一步探索扰动的**光谱-时间-空间关系**
- RQ2：预测因子
	- 考虑其他<span style="color:red;">局地气候和环境信息</span>可能有助于森林干扰特征化，其可谓理解和区分干扰驱动因素提供有价值的见解
- RQ3：数据融合
	- 在**特征级和决策级**上进行数据融合以提高森林干扰检测和表征的是一种有前途的策略，因其可以<u>集成多种遥感数据产品</u>
		- 数据融合还没得到广泛探索，建议**集成多模态EO数据**，以互补增强光谱、空间、时间和结构信息，增强干扰特征的可靠性和鲁棒性
	- 通过<mark class="highlight">深度学习</mark>方法，对遥感图像提取的**特征进行融合**
- RQ4：分类方法
	- 基于<mark class="highlight">人工智能</mark>的分类方法已开始使用，并预计在未来几年将进一步<span style="color:red;"><strong>急剧增加</strong></span> 
	- 不仅要优先考虑模型和特征的可解释性，还要强调可解释性的重要性
- RQ5：数据可获得
	- 注重开放的数据获取和可重复性

## 4 创新点
/

## 5 个人思考

- 有助于构建森林干扰特征方面的框架
- 图表设计值得学习
- 注意领域研究的缺口，从中寻找可能的课题，获取创新性的灵感

## 6 研究方法

- 数据来源：数据库：Scopus，Web of Science，MDPI，Science Direct和IEEE Xplore
- 文献筛选：
	- 年份：1995-2023
	- 筛选后数量：104
	- 限制条件：mapped or identified the disturbance agent 映射或识别干扰因子、使用earth observation (EO)数据、采用时间序列（非单、双时相）、大区域

## 7 局限性

- 现有文献的评估不完整：不包括有关森林干扰研究的所有文献
- 未纳入文献质量评估
- 存在地域偏差：优先考虑了北方国家，南方地区相关研究代表性不足
- 研究者偏差：只考虑第一作者

---
# 重点摘录

## 1 Introduction

- 森林扰动的定义：
	- Forest disturbance are discrete events that result in either gradual or abrupt changes to forest composition, structure, and function
	- 森林干扰是导致森林组成、结构和功能发生渐变或突变的离散事件
	- that can **potentially** be detected through earth observation sensors
- 特点：frequency and intensity 频率&强度
- **分类**：
	- 自然干扰事件：如昆虫暴发、火灾、干旱和大风
	- 人为干扰事件（human-induced disturbances）：如 harvest and silviculture practices 收获和造林经营措施

<br>

- 开源的遥感数据：Landsat family、Sentinel sensors
- 地图综述（Mapping Review，MP）的定义：
	- systematic search across a wide domain to identify knowledge gaps and determine future research needs
	- 在广泛的领域中进行系统搜索，以确定知识差距，并确定未来的研究需求

<br>

- 综述：
Frolking等( 2009 )对遥感技术在探测森林干扰及其对森林结构和地上生物量的影响方面进行了全面的研究。
Lausch et al ( 2016 )对与森林健康衰退相关的光谱植物性状进行了深入分析，并提供了如何利用遥感技术量化这些性状的例子
Senf等( 2017 )集中研究了与应用遥感评估昆虫干扰有关的文献
**Torres等( 2021 )系统总结了2015 - 2020年间发表的用于绘制森林健康的方法。** 这篇必须读


## 2 Materials and methods
/

## 3 Results

### 3.1  变化检测方法表征森林干扰的时空格局

- 光谱-时间模式的使用率是空间模式的三倍
- 变化检测方法占比81.7%

（1）时间模式
- 定义：光谱响应随时间的动态变化，通常称为光谱轨迹
- 依变化检测算法，分类：
	1. **假设**轨迹：根据事先定义的与物候特征和结构发展相似的曲线描述扰动的行为
	2. 轨迹**分割**：通过分段线性回归将谱-时信号分割成具有或不具有趋势的较小片段
	3. 轨迹**阈值化**：通过使用用户自定义的值将时间序列划分为扰动前和扰动后两个阶段来描述时间模式
- 前二者集成了阈值的使用，将阈值应用在时序分析后的扰动识别，以过滤掉噪声观测并评估森林恢复情况；轨迹阈值化直接将阈值应用于光谱波段or时序

- <mark class="highlight">经典算法</mark>
	- Landsat-based detection of Trends in Disturbance and Recovery（**LandTrendr**）
	- Vegetation Change Tracker （**VCT**）
	- Continuous Change Detection and Classification （**CCDC**）
	- 共同特征：均完全自动化，且大多生成频谱变化幅度和干扰年份作为标准输出

（2）空间模式
- 定义：通过将具有相似<span style="color:red;">光谱-时间模式</span>的**单个像素**组合成<span style="color:red;">patches</span>斑块来提取的
- 区域合并：具有一定相似度的相邻像素根据一定的规则集组合在一起以创建空间对象
- 大多数研究中，<span style="color:red;">对象</span>在识别和分类像素**时间模式之后**创建的
- 相关算法：
	- Geographic object-based image analysis (**GEOBIA**)
	- **CNN**

---

### 3.2  对表征森林干扰很重要的空间-光谱-时间预测因子

- **短波红外(SWIR)区域**的<u>光谱变化幅度</u>和<u>干扰发生时间</u>是最常用的预测因子，还包括**受干扰斑块尺寸**和**高程**的测量
	- Landsat 波段对应：B3 – red，B4 – Near Infrared (NIR)，**B5** – Shortwave Infrared (SWIR) 1，**B7** – Shortwave Infrared (SWIR) 2
- 光谱：85.8%，空间：8.6%，地形：4.2%

（1）**光谱预测因子**【占比见Fig. 4.】

- 最常用的光谱因子：normalized burn ratio 标准化燃烧比 （NBR）
- 其他常用指标：
	- bands：Landsat bands 5, 7 and 3
	 - 植被指数（VIs）：tasseled cap wetness 缨帽湿度 (TCW)、normalized difference vegetation index 归一化差异植被指数 (NDVI)、tasseled cap greenness 缨帽绿度 (TCG)、tasseled cap brightness 缨帽亮度 (TCB)、normalized difference moisture index 归一化差异水分指数 (NDMI)、tasseled cap angle  缨帽角度 (TCA)
- 考虑方面：光谱时间序列值、光谱变化幅度、干扰发生时间、干扰率、持续时间和年份

（2）**空间预测因子**
- 常用空间因子：斑块（42.4%）、形状（33.7%）、邻近指标（10.9%）

（3）**地形预测因子**
- 常用地形因子：高程（31.1 %）、坡度（28.9 %）、坡向（20 %）

<br>

- 不同类型干扰常用因子：【见Fig. 5.】
	- Biotic disturbances  生物干扰
	- Abiotic disturbances 非生物干扰：如野火、风
	- Management disturbances **管理干扰**：如收获

---

### 3.3 多源遥感数据的数据融合方法

- 图像融合分为<u>像素或数据级</u>、<u>特征级</u>和<u>决策层</u>
	- 像素级：应用任何分类策略之前，在像素级别上结合了来自多个来源的信息
	- 特征级：将多个来源提取的特征进行融合，如多个时间序列分割算法的输出
	- 决策级：结合多传感器分类图生成最终输出
- 14篇文章使用

（1）**数据级**：4/14
- MODIS & Landsat 融合经典算法：
	- Spatial-Temporal Adaptive Algorithm for mapping Reflectance Change (**STAARCH**)
	- Spatial and Temporal Adaptive Reflectance Fusion Model **(STARFM**)
- 提高Landsat分辨率：**pan sharpening** 全色锐化

（2）**特征级**：5/14
- 常用算法：
	- Composite Analysis
	- rule-based (RB)
	- **random forest (RF)**
	- **NN**
- <u>提取特征的变化检测算法</u>：VCT，LandTrendr，breakpoints detection，ShapeSelectForest

（3）**决策级**：5/14
- 常用算法：
	- Bayesian Updating of Land-cover Estimates (BULC)
	- RB
	- **RF**

<br>

- **总体准确率**：数据级（91%）、决策级（94%）较高，特征级（75%）较低

---

### 3.4 用于森林干扰表征的分类算法

- <mark class="highlight">随机森林</mark>是最常用的分类器，其次分别是<u>基于概率，基于规则，基于实例，基于聚类和神经网络</u>【见Fig. 7.】
- 可能使用多个分类器，并可能被分配到多个类别
- 时间线：无监督方法：ISODATA、最大似然、基于规则-->ML 机器学习：决策树-->ML：随机森林（被广泛采用为**黄金分类框架**）
<br>

- 分类算法中：<mark class="highlight"><strong>神经网络</strong></mark>**采用率相对较低**，已证实较之CCDC（Continuous Change Detection and Classification）、LandTrendr 和 VCT（Vegetation Change Tracker）有**较高的准确性**，<u>但在未分类的组别中整体准确率差异较大</u>。 #缺口 

---

### 3.5 参考数据的可访问性

- 不到10 %的研究提供了用于其分析的数据集

## 4 Discussion

- 欧洲&北美森林扰动研究较集中，热带地区因缺少无云卫星数据受限制。

### 4.1 时空模式

- 光谱-时间模式
	- 光谱-时间模式依赖**分析光谱轨迹**的算法的选择
		- CCDC、VCT：检测突变，难以检测昆虫爆发的细微变化
		- LandTrendr：检测突变和渐变；但噪声的存在，使得许多微小幅度变化被错误地归类为渐变，因此需要进行额外的幅度滤波
	- 算法实现：
		- Google Earth Engine (GEE) ：已集成CCDC, EWMACD, BFAST (monitor version), LandTrendr, Structural Breakpoint Detection, VCT,  VeRDET算法
		- Python包：CCDC家族
		- R包：BFAST、ShapeSelectForest
- 空间模式
	- 1/4研究包括空间模式，以利用**受干扰斑块的几何属性**丰富时间信息
- 像素-->对象
	- 大多数研究主要在**像素**水平上进行**光谱-时间分析**，而后通常将同一年内发生扰动的像素聚合成变化过程**对象**，但分析的<u>主要焦点仍然是单个像素而不是聚合对象</u>。
	- object-based change detection 基于对象的变化检测 (**OBCD**)
		- <mark class="highlight">挑战</mark>：变化对象的精度&特征受限于分类算法&参数；人工调参；光谱-时间分析的**对象可能会随着时间变化**，从而影响其可解释性；长期OBCD计算开销大
		- <mark class="highlight">应对</mark>：同一扰动事件影响下，受影响的像素通常会在短时间内表现出**类似的光谱变化**，将那些在同一时间段内发生同步变化的像素聚合成一个空间单元（spatial unit），用于进一步的光谱-时间分析
		- 其挑战导致了对光谱-时间特征的偏好高于空间特征

- **可见光和红外范围**内的光谱反射率对植被特性的变化有高度敏感性，有利于检测可能不会显著影响植被空间构型的细微变化

<br>

### 4.2 重要预测因子

- SWIR中的波段 & vegetation indices 植被指数

（1）光谱预测因子
- 可见光（Visible）和近红外（Near-Infrared, NIR）区域的波段和植被指数仍常用
- 与上述二者相比，短波红外（Short-Wave Infrared, SWIR）光谱区域，其精度更高、噪声更低
- Sentinel - 2数据获取时间较晚，缺乏历史上下文信息，可协调Landsat数据集以避免

（2）非光谱预测因子：空间、地形
- 充分证实了重要性：与景观信息、高程和斑块维度指标有关的预测因子

### 4.3 数据融合

- <span style="color:red;"><strong>目的</strong></span>：<mark class="highlight">增强扰动特征</mark>
- 并非总有帮助

（1）**数据级融合**

- 中分辨率 融合 粗分辨率（eg: Landsat and MODIS）
	- 数据级增加时序时间信息的有力工具
	- 粗分辨率图像在融合过程引入伪迹
	- <u>变化必须在粗分辨率下检测到，才能被融合到最终的产品中</u>

（2）**特征级融合**

- 融合方法：
	- 方式1：通过分类集成将多个变化检测算法or多个波段orVI的输出进行组合
	- 方式2：通过<mark class="highlight"><strong>深度学习</strong></mark>方法，对遥感图像提取的特征进行融合（eg. Sentinel - 1&Sentinel - 2）
		- 类似方法在森林扰动上的应用还是**缺失**的 #缺口 

（3）**决策级融合**

- 集成**异构EO数据源**，增强了时空扰动映射，特别是解决了**云雾阻碍**的限制

### 4.4 分类器

- **ML**分类器方法
	- **RF**以其相对较高的准确率，被作为行业标准程序
	- 适用性的瓶颈：
		1. **伪重复（Pseudo-replication）的挑战**：无法有效处理由于<u>同一地点不同时间发生干扰</u>而产生的伪重复
			- 因此，研究者聚焦于对每个样本中最显著的干扰进行分类，而<u>忽略了其他干扰阶段</u>
		2. 虽然提供了预测器的重要性评估，但并未**解释**每个特征如何具体参与到干扰分类的预测过程中
			- 改进：对每个特征对扰动事件预测的贡献进行解释性分析
- **深度学习**方法
	- 受限于缺乏高质量、标注充足的训练数据
		- **森林监测计划**：提供了大规模的时空数据，但存在以下阻碍
			1. 点状分布
			2. 高定位误差
			3. 非特意设计用于与遥感数据结合使用

### 4.5 数据可获得性

- 数据的不可获得性：复现、进一步分析等受阻


## 5 Limitations
/

## 6 Conclusion and futurework

- conclusion：
	- RQ1：时空模式
		- **光谱-时间模式**已被广泛使用，空间模式较少
		- 基于轨迹分割的研究占主导
		- 需进一步开发从空间对象中高校提取光谱-时间模式的算法 
	- RQ2：预测因子
		- 利用**短波红外的光谱波段**或**植被指数的光谱变化幅度**是预测森林干扰的**最重要特征**
		- 与<u>尺寸、位置和海拔</u>相关的特征也很重要
		- 考虑其他<span style="color:red;">局地气候和环境信息</span>可能有助于森林干扰特征化，其可谓理解和区分干扰驱动因素提供有价值的见解 
	- RQ3：数据融合
		- 在**特征级和决策级**上进行数据融合以提高森林干扰检测和表征的是一种有前途的策略，因其可以<u>集成多种遥感数据产品</u>
		- 数据融合还没得到广泛探索，建议**集成多模态EO数据**，以互补增强光谱、空间、时间和结构信息，增强干扰特征的可靠性和鲁棒性
	- RQ4：分类方法
		- 从基于专家的规则集到<span style="color:red;"><strong>深度学习</strong></span>，显示出中等到先进的性能 
			- RF使用较多，可能是由于其处理复杂非线性数据的能力和<u>特征重要性</u>指标，但解释各特征对预测做出的贡献很少
			- <u>基于人工智能的方法</u>已开始使用，并预计在未来几年将进一步<span style="color:red;"><strong>急剧增加</strong></span>  
		- 不仅要优先考虑模型和特征的可解释性，还要强调可解释性的重要性 
	- RQ5：数据可获得
		- 数据的不可获得性阻碍了研究发展，鼓励透明公开

- future work：
	- 使用先进的ML技术，例如<span style="color:red;"><strong>深度学习</strong></span>，在端到端的学习框架中进一步探索扰动的**光谱-时间-空间关系**
	- 注重开放的数据获取和可重复性


## Z 附录

### Fig. 1.  文献筛选流程

-  List of databases queried and other sources. After removing duplicates, titles not fitting the scope of the review, and reports not retrieved, the final selection included 104 publications reviewed in this study.
	![[Pasted image 20250218153202.png]]

### Fig. 2. 纳入综述的研究的地理分布

- Geographic distribution of studies included in the review. Circle size depict the total sum of all disturbance events and types across all years per country.
	![[Pasted image 20250218184524.png]]

###  Fig. 3. 时间or光谱-时间or空间模式的研究占比

- Spectral and spatial patterns found in approximately 82% of the publications. 
	- The **first level (most inner circle)** displays the proportion of temporal patterns (~74%), spatial–temporal patterns (~25%) and spatial patterns only (~1%). 
	- The **second level (middle circle)** illustrates the subgroups of the temporal patterns: Hypothesized trajectory, trajectory segmentation, and trajectory thresholding. 
	- The **third level (outer circle)** displays the names of the algorithms/methods used to extract each type of pattern. A description of the acronyms can be found in the Appendix B1.
	![[Pasted image 20250218191424.png]]

### Fig. 4. 出版物中出现次数最多的预测变量
- Top occurring predictors across publications. 
	- Panel (a) corresponds to the **spectral bands and VIs** with their extracted predictors. 
	- Panel (b) corresponds to **spatial and topography predictors**. 
	- Variables with a frequency below 2% or that are used in less than five publications are excluded. 
	- Landsat bands: **B3 – red, B4 Near Infrared (NIR), B5 – Shortwave Infrared (SWIR) 1 and B7 – Shortwave Infrared (SWIR) 2**. (For interpretation of the references to colour in this figure legend, the reader is referred to the web version of this article.)
	![[Pasted image 20250219095249.png]]

### Fig. 5. 预测变量在不同干扰中出现的频次
- Relative occurrence of selected predictors with respect to selected disturbance agents. The frequency is divided by the disturbance agent’s total occurrence. **Spectral predictors (grey bars), spatial predictors (orange) and topographic predictors (green)**. (For interpretation of the references to colour in this figure legend, the reader is referred to the web version of this article.)
	![[Pasted image 20250219095518.png]]


### Fig. 6. 各层级数据融合方法

- Data fusion methods according to each fusion level: data level ( 紫色), feature level (蓝青色 ) and decision level (绿色 ). 
- Moving from the centre to the borders, the top level is the **fusion level**, followed by the reference paper, the **fusion method** and the **data source**.
- The figure highlights the varied fusion approaches employed in the selected studies, potentially leading to the inclusion of certain algorithms multiple times. DM stands for disturbance map. [Reference ID] List of included studies is provided in the Appendix A.
 ![[Pasted image 20250219102858.png]]

### Fig. 7. 分类器使用频次
- Classification framework frequency organized by technique and year. 
	- The right-side panel indicates the frequency and accuracy in relation to the size of the study area. UC: unsupervised classification, TB: tree-based, RB: rule-based, PB: probability-based, NN: neural network, NC: uncategorized and IB: instance-based.
	![[Pasted image 20250219103838.png]]
