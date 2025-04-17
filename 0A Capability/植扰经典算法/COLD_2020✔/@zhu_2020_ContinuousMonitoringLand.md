---
NO: M009
title: "Continuous monitoring of land disturbance based on Landsat time series"
authors: Zhe Zhu, Junxue Zhang, Zhiqiang Yang, Amal H. Aljaddani, Warren B. Cohen, Shi Qiu, Congliang Zhou
year: 2020
journal: 
DOI: 10.1016/j.rse.2019.03.009
---

<font size="5"><strong>标题</strong>：<strong>Continuous monitoring of land disturbance based on Landsat time series</strong></font>
<font size="5">基于 Landsat 时间序列的土地扰动<span style="color:red;"><strong>连续监测</strong></span></font>

- 关键词：
	- COLD 
	- Land disturbance 
	- Change detection 
	- Time series Landsat 
	- <span style="color:red;"><strong>Near real-time</strong></span>
- 主要内容：
![[Pasted image 20250319091052.png]]

## 1 方法概述
- 提出使用Landsat时序数据的新方法：**COntinuous monitoring of Land Disturbance** (<span style="color:red;"><strong>COLD</strong></span>)
	- **当收集到新的图像时**，COLD可以回顾历史土地扰动并持续检测<u>多种土地扰动类型</u>
- 以往时序分析技术监测土地扰动的<mark class="highlight">总结发现</mark>：
	1. 数据源选择
		- <span style="color:red;">地表反射率</span>时间序列优于<u>大气层顶（TOA）反射率</u>时间序列
		- <span style="color:red;">Landsat分析就绪数据（ARD）</span>生成的时间序列优于<u>同景Landsat原始影像</u>生成
			- ARD在“侧向重叠（side-lap）”区域提供更密集时间序列观测
	2.  光谱特征组合
		- <span style="color:red;">组合使用多光谱波段</span>始终优于<u>单一波段或指数</u>。
		- 若已采用所有核心光谱波段，额外添加其他指数并不会提升算法性能
	3. 异常值剔除方法：
		- 基于概率分布的阈值判定：利用正态分布或卡方分布设定概率阈值，剔除偏离模型预测值的异常点
	4. 模型初始化：
		- 模型初始化对陆地扰动检测至关重要，<span style="color:red;">稳定性测试</span>显著影响算法性能
	5. 时序模型建模：
		   - 采用包含<u>8个系数</u>的模型，<span style="color:red;">每次观测更新参数，基于所有无云观测数据</span>，可获最佳结果
	6. 土地扰动判定条件：
		- 在<span style="color:red;"><strong>连续 6 次异常观测</strong></span>中，变化概率阈值为 0.99（卡方分布），光谱向量平均夹角< 45°，以确认变化可提供最佳结果
		- 同时推荐联合使用时序调整均方根误差（RMSE）与最小RMSE指标
	7. 植被再生长干扰：
		- 植被扰动制图应<span style="color:red;">排除因植被恢复导致的光谱突变</span>（如森林砍伐后的自然再生）
	- ➡**COLD 算法根据上述所有经验教训开发和校准**

- <span style="color:red;"><strong>COLD</strong></span>：基于CCDC
	1. 确定检测土地干扰的<u>最佳时间序列输入</u>
	2. 根据经过良好解释的**参考数据集**开发和校准土地扰动检测算法
	3. **删除与干扰无关的“中断”**
---
## 局限

1. 计算成本很高，并且需要大量的数据存储
	- 处理一个 Landsat ARD 切片（1-3000 张图像）所需的时间仍需要 1000-3000 个计算小时，并且将 Landsat 时间序列存储在一个 ARD 切片中大约需要 0.5-2 TB。
2. COLD 算法适用于相对稳定的地方，但对于经常受到干扰的地方（例如，农业和城市地区），COLD 算法很难找到一个稳定的时间段来初始化时间序列模型，并且可能会错过这些干扰事件。
3.  COLD 算法只能识别干扰事件，无法提供更详细的干扰类型信息
	- USGS 和 USFS 正在合作收集独立的参考样本，用于验证和表征土地变化监测、评估和预测 （LCMAP） 项目的<mark class="highlight">年度土地覆盖产品</mark>（Pengra 等人，2016 年;Zhu et al.， 2016），其中总共将收集 25,000 个 Landsat 图。此数据集提供每个日历年中**每个像素的土地覆被和干扰信息**，并且由于它是基于简单随机采样创建的，因此不会偏向于森林。 #⭐ 


---
## 2 算法步骤 / 3. Methods

### 3.1. The COLD algorithm⭐

- COLD：基于Zhu 和 Woodcock （2014b） 以及 Zhu et al. （2015b），经过改进和校准以更好地监测各种陆地干扰
- COLD由三个主要过程组成：<u>数据准备、模型初始化、持续监控</u>
![[Pasted image 20250325095420.png]]
#### 3.1.1. Data preparation

- 三个**主要步骤**：
	1. Fmask
	2. 初始化模型窗口
	3. Tmask
- Steps：
	1. 下载所有可用Landsat影像，使用Fmask算法去云、云阴影和雪
		- 由Quality Assessment (QA) band 质量评估波段提供
	2. 初始化模型窗口
		- 目的：指定了**启动时间序列模型**所需的时间窗口
		- 模型需满足以下三个准则
			1. 包含大于或等于12个clear的观测值
				- clear：去云、云阴影和雪
			2. 时序总长度至少一年
			3. 最大的数据间隔小于一年
	3. 应用Tmask算法
		- COLD<u>对Tmask算法进行了修改</u>（<u>详见 Section 3.3.2.1</u>）
#### 3.1.2. Model initialization

- 测试初始化模型的稳定性（<u>详见Section 3.3.3</u>）
	- 若未通过，则删除最早的clear观测值，并添加新观测值。直至模型稳定。
	- （<mark class="highlight">Fig. 3. 紫色箭头</mark>）
- 对于每个新初始化的时序模型，COLD会将其作为基准，回顾时序模型起始时间之前的观测数据，以<span style="color:red;"><strong>调整时序模型的起始时间</strong></span>。
	- 确定观测值是否纳入模型：采用与持续监测流程相同的变化监测方法<u>（详见Section 3.3.5）</u>
		- COLD搜索所有位于当前模型起始时间之前、但未被先前或当前模型包含的无云观测，<u>直至发现一个已确认的变化点，或搜索至最后一个观测</u>
		- 对于明显偏离模型预测但未连续被检测为异常的观测，COLD将其归类为由云、云阴影、雪或土壤湿度变化引起的**非干扰相关短暂变化**，并在后续时间序列分析中剔除（<mark class="highlight">Fig. 3. 橙色箭头</mark>，<u>详见Section 3.3.2.2</u>）
	- ➡COLD初始化时序模型，该模型包含起始时间之前的所有无云观测，并符合当前模型的预测轨迹
	- 总结：
		- 如果
			1. 在当前模型起始时间前确认了一个变化点，且该模型是像元的首次初始化
			2. COLD将基于变化点前的观测重新估计一个新模型，并将此新模型及变化点记录至“时间段数据库”（如Table 1所示）
		- 否则
			- COLD将继续使用当前模型监测土地扰动

#### 3.1.3. Continuous monitoring

- 主要包括**五步**：updating model, looking forward, detecting change, recording time segments, and extracting disturbance

1. 所有新收集的clear观测值都将用于更新当前时序模型 
	- 时序模型：
		- 包含**三组**具有不同时间频率的谐波分量，由一定数量的可获得的clear观测值触发
			- Basically，clear的观测值数量越多，<span style="color:red;">谐波分量的频率越高</sapn>
			
				- *即周期越短*
		- ![[Pasted image 20250325160441.png|450]]
		- ![[Pasted image 20250325160512.png|450]]
	- clear观测值数量：
		- between 12 and 17 (k = 1)：模型参数为**4**
		- between 18 and 23 (k = 2)：模型参数为6
		- greater than or equal to 24 (k = 3)：模型参数为**8**
			- 最多参数为8的原因，<u>详见Section 3.3.4.1</u>
2. 时序模型更新
	- 当收集到新的clear观测值，COLD将模型预测值与实际clear观测值比较，计算光谱变化的幅度。<u>若幅度在预期范围内</u>，则该观测值视为稳定，<span style="color:red;">并用于迭代更新时序模型</sapn>
	- <u>（详见Section 3.3.4.2）</u>
 3. 变化检测，与2相似
	- 对于实际clear观测值与模型预测值产生很大偏差（**幅度 & 方向**）
	1. **未以连续方式**出现：
		- COLD识别为与干扰无关的短暂变化
		- 将其从未来时序中删去（<mark class="highlight">Fig.3 右侧橙色箭头</mark>）
	2. **以连续方式**出现
		- COLD识别为变化
		- <u>（详见Section 3.3.5）</u>
4. 时间段光谱、时间信息记录
	- N个断点，有N+1个时间段
	- 每个时间段记录光谱、时间等77 个变量
	- ![[Pasted image 20250325165556.png]]
5. 提取土地干扰信息
	- 从断点中提取土地干扰信息，并向前追溯最近一次clear观测记录
	- <u>（详见Section 3.3.6）</u>

### 3.2. Algorithm performance evaluation metrics

- 随机选择50%的参考样本进行算法开发和校准，50%用于算法验证
- 三个精度指标
	- ![[Pasted image 20250325170212.png|450]]

### 3.3. COLD algorithm development and calibration ⭐

- COLD算法主要包括6个主要部分：
	- 输入数据、异常值去除、模型初始化、模型拟合、变化检测、干扰提取
	- ![[Pasted image 20250325170545.png]]
- COLD 与 CCDC算法进行比较
	- ![[Pasted image 20250325180552.png]]

#### 3.3.1. Input data
- 最佳输入：1) ARD or same scene data; 2) TOA or SR, and; 3) which variable or selections of variables?
- **ARD数据时间密度不一致**➡提出一种<span style="color:red;">自适应调整方法</span>：根据**时间序列整体密度**<span style="color:red;">动态调节</span><u>变化概率阈值 & 确认变化所需连续异常观测次数</u>
	- <u>（详见Section 1.1 in Supplementary）</u>

（1）ARD vs 同景数据（same scene data） **&** 顶层反射率（TOA）vs 地表反射率（SR）

- 发现：
	1. 数据时间密度不一致，<u>SR精度大大高于TOA精度</u>（ARD & 同景数据）
	2. 对于SR和TOA，使用ADR比同景数据发现更多变化
		- <span style="color:red;">基于ARD检测地表扰动的挑战</span>：由于**重叠区**与**非重叠区**的时间密度差异，最终生成的扰动图会呈现不一致的空间分布模式 #挑战
			- 降低了漏检率（omission error），但误检率（commission error）上升
			- 【】ARD数据在空间覆盖上存在固有特性
				- <u>重叠区</u>：多轨道覆盖 → 时间密度可达16天3-4次观测
				- <u>非重叠区</u>：仅单轨道覆盖 → 维持基础16天周期
				- 密度差异导致算法在不同区域的**置信区间宽度**不同：重叠区置信带更窄
					- ➡相同强度的变化信号在非重叠区可能达不到检测阈值，而在重叠区则被过度检出
				- 为<u>补偿非重叠区的时间密度不足</u>，ARD常采用时空插值生成连续序列
	
- 若采用自适应调整方法
	- **SR依旧优于TOA**
	1. ARD：调整后的ARD（SR&TOA反射率）优于未调整的ARD
		- 误检率大幅降低约10%
	2. 同景数据：调整前后效果持平
	- ➡调整方法不会人为改变非重叠区域的扰动检测结果，而<span style="color:red;">对于重叠区域</span>调整后的方法能实现更高精度的地表扰动识别
	- 验证了**高密度时间序列**的重要性：时间密度越高，地表扰动检测效果越好
- ➡本研究最终选用<mark class="highlight">Landsat ARD SR</mark>作为输入数据类型，并同步采用<u>自适应调整法</u>进行检测

（2）变量选择

- **波段和指数的组合使用**效果优于单一变量
	- <u>单一变量使用</u>：
		1. 单一波段：
			- SWIR波段表现相似（SWIR2略好），性能远远优于其他波段
			- Red波段第三，其他波段几乎无用，TIR、NIR、Blue bands表现最差
		2. 单一指数：
			- Normalized Difference Vegetation Index (NDVI), Normalized Burn Ratio (NBR), 、Tasseled Cap Wetness效果并不好
				- 包含SWIR的NBR和Wetness**效果甚至不如单个SWIR的使用**
			- <u>NDVI效果与Red波段相似</u>
	- <u>组合变量使用</u>： #⭐
		- Red & NIR 与 NDVI相似
		- NIR/Red & SWIR1 与 SWIR1相似
		- Red & NIR & SWIR1 较 SWIR1 略优
		- <span style="color:red;"><strong>Red & NIR & SWIR1 & SWIR2 在COLD算法中表现最优</strong></span>
			- **遗漏误差大大减少**
			- ⬅所有提供有用信息的光谱波段的使用，<u>不同类型的陆地扰动，变化的方向和幅度可能大不相同</u>  
		- COLD中，Blue、TIR、Blue 和 TIR 波段或其他指数未提供更好的性能
			1. ⬅Blue、TIR波段受大气影像大于陆地扰动
			2. ⬅<span style="color:red;">若所有有用的光谱波段均已在陆地扰动监测中使用，则植被指数中的信号是多余的</span>

#### 3.3.2. Outlier removal

- Fmask：筛选云、云阴影、雪
	- Fmask存在缺陷，且任何异常值对时序均有害接着应用了<span style="color:red;"><strong>Tmask（多时态掩膜算法）</strong></span>，进一步筛选云、云阴影、雪和<u>其他与干扰无关</u>的异常值
		- [[@zhu_2014_ContinuousChangeDetection]]
			- Fmask**缺点**：无法筛查<mark class="highlight">短暂的变化</mark>，如浓气溶胶、烟雾或<mark class="highlight">洪水</mark>，<u>可能与土地覆被变化相混淆</u>。
			- 模型初始化时，Fmask使用后接着使用**多时相分析**排除异常值
- Tmask：
	- 会剔除部分短暂性变化 ➡<span style="color:red;">只在模型初始化时使用</span>
		- *如：某农田在3-5月因灌溉出现反射率波动，Tmask可能将其视为异常并剔除*
		- 模型初始化后，**非连续的异常观测值**也会被剔除，<u>因为陆地扰动在时间上比异常更具有一致性</u>
			- *地表扰动在时间维度上具有持续稳定性，与偶然异常值存在本质差异*

---
（1）Tmask modification

- Tmask算法中：<u>Green & SWIR1波段</u>（TOA反射率）的**固定阈值0.04**用于检测云、云阴影和雪
	- 该阈值针对新英格兰Landsat场景制定，<u>尚未针对其他区域</u>
	- 缺陷：对于自然变化较大的地方（如城市和农业区域），固定阈值会删除许多clear观测值，留下极少的观测值用于模型拟合
	- ➡ 提出了一种<span style="color:red;"><strong>动态阈值</strong></span>的Tmask算法

- <u>动态阈值</u>的Tmask算法
	- 基于**lag-1 madogram**的特定类型变异函数<u>（见Section 2.1 in Supplementary）</u>
	- 动态阈值：
		- 探索了基于一系列概率（例如 0.99、0.999、0.9999、0.99999 和 0.999999）的各种<u>动态阈值</u>的使用，得到变化概率为 0.99999（**正态分布**）达到最佳性能
		- <u>（见Section 2.2 in Supplementary）</u>

（2）Non-consecutive outlier removal

- 变化检测过程中，可用计算clear观测值的<span style="color:red;"><strong>变化概率</strong></span>（<mark class="highlight">Eq. 8</mark>
	- ![[Pasted image 20250326092000.png|450]]
	- **异常值**：较大的变化概率，但持续时间短暂
	- **阈值**：测试了不同<u>变化概率阈值</u>（0.99、0.999、0.9999、0.99999及0.999999）对此类**非连续异常值**的过滤效果，最终确定**卡方分布**下0.99999为最优阈值。
		- 若连续多个观测均未检测到变化，但<u>该序列首个观测值超出变化阈值</u>且变化概率较高，则剔除
	- ➡如果变化概率小于0.99999，则 COLD 将使用此观察结果来更新模型拟合（<mark class="highlight">Fig.3. 橙色虚线箭头</mark>）

#### 3.3.3. Model initialization

（1）CCDC vs. COLD initialization

- 模型初始化的**稳定性测试**：
	- CCDC
		- 检查模型斜率、第一次和最后一次观测值与模型预测的差异
			- 如果模型在开始or结束发生变化，变化数量太少以至于斜率没有发生改变，但模型预测和实际观测值之间存在很大的差异
			- ![[Pasted image 20250326145154.png|450]]
		- ➡通过标准：若$l_{slope},l_{start},l_{end}$均小于变化概率
			- 缺陷：
				- 如果变化发生在中期，模型斜率部分收到已更改的观测值的影响，这种情况下，$l_{slope},l_{start},l_{end}$<u>都有可能小于变化概率</u>
				- ![[Pasted image 20250326145348.png|450]]
		- ➡修改了COLD的初始化：<span style="color:red;"><strong>完善了稳定性测试</strong></span>
	- COLD
		- ![[Pasted image 20250326145701.png]]
	- 比较：变化概率为0.99时，COLD、CCDC 的漏检率（omission rate）相似，但<u>CCDC 的<strong>误检率</strong>（commission rate）下降了约10%</u>

#### 3.3.4. Model fit

（1）Number of coefficients.

- **谐波（傅里叶）模型**（Harmonic (Fourier) models）模型广泛用于卫星时序建模
	- 先前研究表明3个谐波项（annual, bimodal, and trimodal changes）最适合，但没有相关证据
	- ➡探索了系数为 4、6 和 8 的时间序列模型（Eq. 1），<u> 结果表明：8 优于 6 优于 4</u>
		- 系数的增加**大大降低漏检率**，而误检率相似
		- ⬅建模更好，更多的系数不会导致大过拟合：由**最小绝对收缩和选择算子**（Least Absolute Shrinkage and Selection Operator，**LASSO**) 控制<u>（见Section 3.3.4.4y）</u>
- ➡选择了<u>系数为8</u>的时序模型
	- **基于LASSO 拟合**，若使用其他（如OLS），结果可能会有所不同

（2）Update frequency.

- 新收集到的clear观测值用于迭代更新时序模型
	- 过于频繁：计算成本过大，且效果不一定更好
- 更新频率探索：**固定时间** & **先前时间的固定比例**（1/5, 1/4, 1/3 or 1/2）
	- 固定时间：
		- 线性减少计算时间
		- 频率越低，效果越差
	- 固定时间比例：
		- 基于前一个模型拟合窗口时间长度的1/N来更新模型（例如，如果前一个模型拟合需要T天，那么下一个模型拟合需要额外的T/N天）
		- <u>指数</u>减少计算时间
		- 时序较长时，可以高效计算
		- N越大，效果越差
	- 无论哪种，**针对每次观测进行更新的情况效果最好**
- ➡COLD选择<u>针对每次观测进行更新</u>

（3）Moving window.

- 拟合数据
	- clear观测值越多，模型拟合越准确
		- ➡COLD<u>使用所有可获得的历史稳定像素值</u>进行模型拟合
- 单个时序模型无法拟合一些细微变化，被改变的像素值导致时序模型拟合不准确
	- ➡方法：<span style="color:red;">限制模型估计的最大窗口大小</span>，拟合窗口随着新观测值的获取移动（移动窗口法）➡<u>被改变像素的影响随着窗口迁移消失</u>
	- 当可获取的稳定观测值＜窗口大小时，有多少拟合多少
- 窗口大小
	- 2~所有可获得年限：<u>3年</u>已经能达到良好的精度；**年限越长，精度越佳**
	- ➡使用所有可获得的年限作为窗口大小

（4）Least Absolute Shrinkage and Selection Operator (LASSO) fit.

- 更多的谐波项提供更准确的时序估计，但<u>额外的自由度会导致过拟合</u>
- LASSO回归
	- 以通过**最小化残差平方和与系数绝对值之和**来<span style="color:red;">降低过拟合</span>而著称（Eq. 6）
		- ![[Pasted image 20250327160657.png]]
	- <u>关键参数</u>：<span style="color:red;"><strong>罚参项</strong></span>$\lambda$
		- 值越大，过拟合的惩罚越大
		- 当$\lambda = 0$，<u>LASSO 和 OLS 相同</u>
	- $\lambda$取值：8参数时序模型$\lambda$取值0~100中
		- LASSO 优于 OLS
		- $\lambda = 20$表现最佳
- ➡COLD拟合时选择$\lambda = 20$的LASSO拟合

#### 3.3.5. Change detection

（1）Number of consecutive anomaly observations. 连续观测奇异值的数量

- 将连续的异常值剔除
- 连续的数量
	- 4~7中，4~6准确性上升，6~7准确性下降
- ➡COLD选择**6次**作为标准
	- 一些短期的变化会被漏检，比如火灾、洪水等
	- 在多云区域，COLD算法<u>需要等待较长时间</u>，以获得6个clear观测值以确定变化

（2）Change probability <mark class="highlight">变化概率</mark>

- 变化概率是COLD算法的<span style="color:red;"><strong>关键变量</strong></span>：变化检测的阈值
	- *即p值*
	- ![[Pasted image 20250328090857.png]]
- CCDC & COLD 阈值公式
	- ![[Pasted image 20250327165113.png|450]]
- CCDC：
	- **使用RMSE的3倍检测变化**（<mark class="highlight">Eq. 7</mark>），最新版本低于2倍以获取更多细微变化
		- Eq. 7中，模型预测值与观测值的**归一化RMSE差异**需在所有Landsat光谱波段上取均值
			- 问题：若土地扰动仅在某单一波段表现出变化，<u>该波段差异会被其他波段均值稀释</u>，导致检测失效
- ➡COLD：采用**归一化后**的<span style="color:red;"><strong>变化向量的大小</strong></span>计算变化检测阈值（<mark class="highlight">Eq. 8</mark>）
	- 通过平方偏差求和，即使某一波段的变化与其他波段取均值，仍能显著体现异常
	- 每个归一化**变化偏差**均服从<u>标准正态分布</u>，其**平方和**服从自由度为光谱波段数的卡方分布，因此基于**卡方分布**的概率设定阈值
		- 实验表明，在5个光谱波段下，0.99的变化概率阈值效果最优
	- ![[Pasted image 20250328091319.png]]

#### 3.3.6. Change angle

- 与随机的环境和系统噪声不同，**干扰信号**在<span style="color:red;"><strong>绝对变化幅度、变化矢量角度</strong></span>方面**更一致**
	- <u>噪声和其他非干扰信号</u>在时序中可能存在多次，但不可能朝着同一个方向改变
	- ➡可以利用每对<u>相邻<span style="color:red;"><strong>变化向量</strong></span>（<strong>RMSE归一化后</strong>）</u>之间的<span style="color:red;"><strong>平均夹角</strong></span>来确认真实的土地扰动（Fig. 7 & Eq. 9）
		- ![[Pasted image 20250327205109.png|500]]
		- ![[Pasted image 20250327205325.png]]
		- ![[Pasted image 20250328085736.png]]
	- 平均夹角的**阈值**：45°实现了最佳效果
		- 相邻变化矢量对之间的平均夹角<span style="color:red;"><strong>小于 45°</strong></span>， COLD 确认发生变化

（1） Root Mean Square Error (RMSE)

- 模型拟合中，每个光谱波段的均方根误差（RMSE）被用来<u>对变化向量进行归一化处理</u>
	- 若某波段的 **未解释变异（unexplained variations）** 越大（即 RMSE 越大），该波段对最终变化概率的贡献权重就越小
	- 该方法<u>适用于许多土地覆被类型</u>，因为多年来无法解释的变化通常是相同的
	- **缺陷**：
		- 对于农业、半干旱灌木丛和草原，由于受到人类活动和年际气候变化的影响，其green-up时间存在相当大的年际变化。
		- 也就是说，<u>一年中某个时间段的变化远远大于其他时间段的变化，导致RMSE不太适合</u>。
- ➡ 使用时间调节的RMSE（<span style="color:red;"><strong>temporally-adjusted RMSE</strong></span>）作为归一化变化向量的分母
	1. 当总clear观测值数量＞24时，使用最近的**24**个观测值计算RMSE
	2. 当RMSE＜设定的最小值时，使用最小值计算
		- ⬅对于光谱暗像素或清晰观测数量有限的模型，有时 RMSE 可能非常小。这导致**变化向量**被RMSE归一化后，很微小的变化会导致最终**变化概率**的巨大差异。
		- （<u>详见Section 5.2 in Supplementary</u>）
- 各RMSE使用效果
	- RMSE、最小值RMSE表现相似
	- 时间调节RMSE表现更好
	- **时间调节RMSE & 最小值RMSE**联合使用效果最佳（COLD中）

#### 3.3.7. Disturbance extraction

- 在变化检测的识别过程中，一些由<span style="color:red;">偏离模型预测值</span>识别出来的<strong>“breaks”</strong>，实际上并非是土地扰动造成
	- eg. 植被再生（例如，幼龄林和成熟林之间的中断）
- ➡区分 <mark class="highlight">生长中断 vs 土地扰动</mark>
	- 提出了一种**基于通用规则（generic-rule-based）**的土地扰动提取方法
		- ![[Pasted image 20250328092506.png|450]]
		- 假设均为真实的土地扰动，再逐步移除由植被生长造成的中断
			- 与其他通过植被指数的阈值提取扰动不同
			- ➡<u>与植被生物量无关的土地扰动得以保留</u>
	1. 使用<u>Red, NIR, and SWIR1</u>波段的变化向量将中断分为两类
		1. “greener”
			- 矢量变化只会显示在 “更绿 ”的方向上
				- ![[Pasted image 20250328093329.png|450]]
			- “break”之后：较高的 NIR，较低的的Red和 SWIR1 波段（Eq. 10）
				- ![[Pasted image 20250328093129.png|450]]
		2. 陆地扰动
			- **所有方向**上都有变化矢量
			- 除了Reforestation（再造林）与 Afforestation（造林）的土地扰动几乎都被包括
				- *Reforestation = 旧地重植，重在修复；Afforestation= 新地造林，重在扩展*
	2. 根据<u>变化前后</u>的时间序列模型<u>斜率</u>的差异区分（Fortunately 差异较大）
		1. regrowth breaks
			- "breaks"前的斜率大小通常＞"breaks"后的斜率大小
		2. reforestation/Afforestation
			- "breaks"前的斜率大小通常＜"breaks"后的斜率大小
			- ![[Pasted image 20250328155157.png|450]]
			- 由于forestation/Afforestation后时间序列模型的坡向已经确定（NIR 波段为正，红色和 SWIR1 波段为负），可以根据<u>斜坡的大小和方向</u>创建一些规则，从regrowth breaks提取forestation/Afforestation的断点
		- ![[Pasted image 20250328154841.png|450]]

（1）Thresholds for disturbance extraction.

- “greener”方向·定义：NIR 增加， Red/SWIR 降低
	- 定量增加/降低的幅度未知➡探索-0.04到0.04，并纳入了不使用干扰提取过程的场景（将所有断点都识别为土地干扰）
		- 使用干扰提取方法 优于 不使用
		- 阈值-0.02效果最佳，COLD算法采用

## 3 示例图

## 4 重要摘录

### 1. Introduction

- 土地扰动：
	- 包括：stress 胁迫、风、hydrology 水文、debris 碎屑、采伐、mechanical 机械作用、火灾等
	- 拥有**高度的时空异变性**
- 土地扰动 · <span style="color:red;"><strong>定义</strong></span>：<mark class="highlight">发生在<strong>地表自然变率范围</strong>之外的任何<strong>离散</strong>事件</mark>
	- 包含：<u>abrupt changes</u>  &  <u>gradual changes (or stress)</u>
	- 一些土地扰动并不一定会减少植被覆盖
	- 常规的农业实践并不属于土地扰动，除土地覆盖类型or农业活动改变，如作物类型改变or休耕
- 土地变化研究的现有<mark class="highlight">不足</mark>： #缺口 #⭐ 
	- 大多数研究只关注土地覆盖和土地利用变化（变化目标），<u>而很少研究干扰（变化的推动者）</u> 
	- 几乎所有扰动集中在森林干扰，对于<span style="color:red;">非森林区域发生的扰动</span>了解有限
	- **不具备连续监测扰动**的能力

<br>

- **CCDC算法**
	- 利用所有可获得的Landsat数据连续变化检测，具有较高的空间 & 时间精度
	- **缺点**：
		1. 变化幅度相对较小的森林干扰的准确性相对较低
			- ⬅设计用于检测较大变化量级的土地覆被变化
			- ➡对于**具有细微光谱变化的森林干扰**（<span style="color:red;"><strong>干扰后通常仍然是相同的森林覆盖类型</strong></span>），如甲虫侵扰和选择性采伐，CCDC 的帮助较小 #缺口  #⭐ 
		2. 检测到的许多变化（或“中断”）不一定与陆地扰动相对应
			- 对于由植被再生等**渐进条件**变化引起的突变，没有与之相关联的干扰事件

### 2. Study area and data
#### 2.1. Study area

- 研究数据：
	- 利用**泰森多边形**将美国本土划分为 442个TSAs
		- Thiessen Scene Areas （TSAs）：单个遥感影像帧的非重叠覆盖部分
		- ![[Pasted image 20250320154656.png]]
	- 按分层抽样、森林区域优先的抽样原则，最终选定 **180个TSA** 作为研究区
		1. 高森林干扰频率使偏向性对整体结论影响有限，但样本仍具多样性
			- 基于2006 National Land Cover Database（NLCD）数据，森林仅占研究区总面积的 <40%，其余60%为农业、灌木等非森林类型，<u>说明样本仍具多样性</u>
		2. 存在**参考数据**：覆盖所有土地类型的7200个Landsat参考样本已被<u>美国林务局（USFS）</u>进行<span style="color:red;"><strong>专家解译</strong></span>，用于**估算森林干扰**
			- 据作者所知，这是<span style="color:red;"><strong>目前唯一公开可用的</strong></span>、能提供较大区域范围内年际尺度高空间分辨率（30米）森林干扰信息的数据集 #数据

#### 2.2. Reference data

- 180个TSA中，每个TSA基于随机策略选择30米Landsat图像
- <span style="color:red;"><strong>参考数据</strong></span>： 7200 个 Landsat 图的样本量➡**6634个** Landsat plots
	- 7200个Landsat plots：[[@cohen_2016_ForestDisturbanceConterminous]]
		- 基于 Landsat 时间序列可视化和干扰数据收集软件 <mark class="highlight">TimeSync</mark> 进行解释
			- 在 Google Earth 高空间分辨率图像和各种辅助数据的帮助下，在 TimeSync 中解释了 Harvest、Fire、Stress、Wind、Mechanical、Hydrology等干扰事件
				- TimeSync提供的干扰年份不准确➡本研究未提供干扰年份，但<span style="color:red;">保留了干扰年份的<strong>时间间隔</strong></span>，用于训练和验证
		- 尽管参考 Landsat 图质量很高，但存在**缺陷**：
			1. 对于<u>非森林区域</u>，准确性较低⬅解译员来自USFS
			2. 对于<span style="color:red;"><strong>记录森林干扰过度敏感</strong></span>：一些记录的干扰在Landsat中没有信号
	- ➡<u>组织了5位专家对7200个样本进行双重验证</u>，最后得到6634个
		1. 在时序中标记了各种陆地扰动，但Landsat中不可见的扰动被标记为稳定类别
		2. 参考像素有效观测不足（晴空观测次数<24次）、数据缺失过长（连续超过三年）或目视解译存疑的像元，均剔除
	

#### 2.3. Landsat time series from the same scene and Analysis Ready Data (ARD)

- 比较了四种观测数据
	- TOA reflectance Collection 1 data from the same Landsat scene (Scene-TOA)
	- TOA reflectance from ARD (ARD-TOA)
	- SR Collection 1 data from the same Landsat scene (Scene-SR)
	- SR from ARD (ARD-SR).
	- ![[Pasted image 20250325094012.png]]
- 7个主要光谱波段：
	- Blue, Green, Red, Near Infrared (NIR), Short-wave Infrared 1 (SWIR1), SWIR2, and **Thermal Infrared 热红外 (TIR)** bands.
- 借助Google Earth Engine平台提取观测值
