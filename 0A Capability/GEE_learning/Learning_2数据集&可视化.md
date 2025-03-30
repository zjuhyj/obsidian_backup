来源：[云羽徐徐的个人空间-云羽徐徐个人主页-哔哩哔哩视频](https://space.bilibili.com/397763925?spm_id_from=333.1391.0.0)

- Landsat常用的是5，8，9
	- Tier1 精度要高于 Tier2
![[Pasted image 20250227142651.png]]
![[Pasted image 20250227141354.png]]

## 1 数据集导入

（1）**通过研究区调用**

```cpp
//加载并显示研究区边界
var roi = ee.Geometry.Rectangle([115,39,117,41]);

//调用数据集，且筛选时间、范围
var dataset = ee.ImageCollection('LANDSAT/LT05/C02/T1_L2')
				.filterDate('2000-06-01','2000-07-01')
				.filterBounds(roi);
print('研究区所有影像',dataset);
```

![[Pasted image 20250227142549.png]]

（2）**通过条带号调用**


```cpp
//加载并显示研究区边界
var roi = ee.Geometry.Rectangle([115,39,117,41]);
Map.centerObject(roi,8);
Map.addLayer(roi,{'color':'yellow'},'研究区');


//调用数据集，且筛选时间、条带号
	//条带号的增减与常规坐标轴相反，可以通过调整条带号找到要的区域影像
	
//注意：最后加载到地图上的是整个影像合集，所以没有显示真彩色而只是一个白框
	//如果需要真彩色显示可以使用.median()方法生成一个中值合成影像
	//去云操作应在.median()之前
	
var dataset = ee.ImageCollection('LANDSAT/LT05/C02/T1_L2')  
				.filterDate('2000-06-01','2000-07-01')
				.filterMetadata('WRS_PATH','equals',122) 
				.filterMetadata('WRS_ROW','equals',33);
print('研究区所有影像',dataset);

//在地图上可视化影像范围
var visualization={
	bands:['SR_B3','SR_B2','SR_B1'],
	min: 0.0,
	max: 0.3,
};

Map.addLayer(dataset,visualization,'True Color (321)');

```

![[Pasted image 20250227150311.png]]


## 2 去云&真彩色

- 官方文档：[usgs.gov/media/files/landsat-8-9-collection-2-level-2-science-product-guide](https://www.usgs.gov/media/files/landsat-8-9-collection-2-level-2-science-product-guide)

- **比特位含义**：根据需要选择合适的比特位
	- 基于遥感影像数据集提供的质量评估波段（如Landsat系列中的`QA_PIXEL`波段）
	-**Bit 0**：通常表示无效或缺失数据。
	- **Bit 1**：表示该像素可能受云的影响（扩展云）。
	- **Bit 2**：通常是未使用的保留位。
	- **Bit 3**：表示该像素位于云阴影中。
	- **Bit 4**：在Landsat 8中表示植被，在Landsat 4-5 TM和Landsat 7 ETM+中表示云。
	- **Bit 5**：在Landsat 8中表示水体，在Landsat 4-5 TM和Landsat 7 ETM+中是未使用的保留位。
	- **Bit 6**：仅在Landsat 8中使用，表示雪或冰。
	- **Bit 7**：仅在Landsat 8中使用，表示云。

```cpp

// 加载并显示研究区边界
var roi = ee.Geometry.Rectangle([115, 39, 117, 41]);
Map.centerObject(roi, 8);
Map.addLayer(roi, {'color': 'yellow'}, '研究区');

// 定义去云函数
function maskL457sr(image) {  //457指对Landsat457均适用

	//检查前5个比特位是否全为0，若全为0则没有问题
    var qaMask = image.select('QA_PIXEL').bitwiseAnd(parseInt('11111', 2)).eq(0);  


    // 直接使用原始波段而不进行任何变换
    var opticalBands = image.select('SR_B.');
    var thermalBand = image.select('ST_B6');
    
    // Replace the original bands with the selected ones and apply the masks.
    return image.addBands(opticalBands, null, true)
                .addBands(thermalBand, null, true)
                .updateMask(qaMask);
}

// 调用数据集，并筛选时间、条带号
var dataset = ee.ImageCollection('LANDSAT/LT05/C02/T1_L2')
                .filterDate('2000-06-01', '2000-07-01')
                .filterMetadata('WRS_PATH', 'equals', 122)
                .filterMetadata('WRS_ROW', 'equals', 33);

print('研究区所有影像', dataset);

// 应用去云函数到影像集合
var cleanedDataset = dataset.map(maskL457sr);

// 创建一个中值合成影像
var medianImage = cleanedDataset.median();

// 在地图上可视化影像范围
var visualization = {
    bands: ['SR_B3', 'SR_B2', 'SR_B1'], // 标准真彩色组合：红、绿、蓝
    min: 0.0,
    max: 0.3,
};

// 添加去云后的中值合成影像到地图
Map.addLayer(medianImage, visualization, 'Cleaned True Color (321)');
```

![[Pasted image 20250227200206.png]]

- 如果不进行辐射校正，显示为全白or全黑，需根据反射率范围对可视化映射范围进行调整
- 查看反射率范围
```cpp
var sampleImage = cleanedDataset.first();
print('Sample Image Band Statistics', sampleImage.reduceRegion({
    reducer: ee.Reducer.minMax(),
    geometry: roi,
    scale: 30,
    maxPixels: 1e9
}));
```
![[Pasted image 20250227194928.png]]
```cpp
var visualization = {
    bands: ['SR_B3', 'SR_B2', 'SR_B1'], // 标准真彩色组合：红、绿、蓝
    min: 9979,
    max: 17180, // 看到的所需波段的DN值范围
};
```

![[Pasted image 20250227201658.png]]

---
- 去云操作的不同写法：仅检查第3、第5个比特位
	- 多用于Landsat5
```cpp
// 加载并显示研究区边界
var roi = ee.Geometry.Rectangle([115,39,117,41]);
Map.centerObject(roi,8);
Map.addLayer(roi,{'color':'yellow'},'研究区');

// 调用数据集，并筛选时间、条带号
var dataset = ee.ImageCollection('LANDSAT/LT05/C02/T1_L2')
              .filterDate('2000-06-01','2000-07-01')
              .filterMetadata('WRS_PATH','equals',122) 
              .filterMetadata('WRS_ROW','equals',33);

// 去云操作：这里我们使用QA_PIXEL波段进行云检测
function maskClouds(image) {
  var qa = image.select('QA_PIXEL');
  
  // Bits 3 and 5 are cloud shadow and cloud, respectively.
  //只检查第3和第5个比特位
  var cloudShadowBitMask = (1 << 3);
  var cloudsBitMask = (1 << 5);
  
  // 取出QA_PIXEL波段的比特值，并按需屏蔽云和云阴影
  var mask = qa.bitwiseAnd(cloudShadowBitMask).eq(0)
                 .and(qa.bitwiseAnd(cloudsBitMask).eq(0));
  return image.updateMask(mask);
}

// 应用去云函数到影像集合，并使用.median()方法生成一个中值合成影像以去除未掩码掉的异常值
var cloudFreeComposite = dataset.map(maskClouds).median();

// 在地图上可视化影像范围
var visualization = {
  bands:['SR_B3','SR_B2','SR_B1'],
  min: 0.0,
  max: 0.3,
};

// 显示去云后的中值合成影像
Map.addLayer(cloudFreeComposite, visualization, 'True Color (321) - Cloud Free');
```
![[Pasted image 20250227195232.png]]


## 3 辐射校正

- **Min & Max**：波段值的最小值和最大值，定义了波段数据值的动态范围。未经校正的原始DN（Digital Number）值，这通常是0到255或0到65535。

- **Scale & Offset**：用于将原始DN值转换为物理量（如反射率或温度）。公式一般为`Physical_Value = DN * Scale + Offset`。不同的产品级别（如TOA反射率、地表反射率或亮度温度）会有不同的比例因子和偏移量


![[Pasted image 20250227193754.png]]

```cpp

// 加载并显示研究区边界
var roi = ee.Geometry.Rectangle([115, 39, 117, 41]);
Map.centerObject(roi, 8);
Map.addLayer(roi, {'color': 'yellow'}, '研究区');

// 定义去云函数
function maskL457sr(image) {

    var qaMask = image.select('QA_PIXEL').bitwiseAnd(parseInt('11111', 2)).eq(0);

	//检查在辐射校正过程中出现的过饱和像素，确保最终结果中只包含高质量（去云）且未饱和的像素
    var saturationMask = image.select('QA_RADSAT').eq(0);

    // Apply the scaling factors to the appropriate bands.
    // 对光学波段和热红外波段分别应用了线性变换（乘以比例因子并加上偏移量）以转换为反射率和亮度温度
    var opticalBands = image.select('SR_B.').multiply(0.0000275).add(-0.2);
    var thermalBand = image.select('ST_B6').multiply(0.00341802).add(149.0);
    
    // Replace the original bands with the scaled ones and apply the masks.
    // 进行去云操作
    return image.addBands(opticalBands, null, true)
                .addBands(thermalBand, null, true)
                .updateMask(qaMask).updateMask(saturationMask);
}

// 调用数据集，并筛选时间、条带号
var dataset = ee.ImageCollection('LANDSAT/LT05/C02/T1_L2')
                .filterDate('2000-06-01', '2000-07-01')
                .filterMetadata('WRS_PATH', 'equals', 122)
                .filterMetadata('WRS_ROW', 'equals', 33);

print('研究区所有影像', dataset);

// 应用去云函数到影像集合
var cleanedDataset = dataset.map(maskL457sr);

// 创建一个中值合成影像
var medianImage = cleanedDataset.median();

// 在地图上可视化影像范围
var visualization = {
    bands: ['SR_B3', 'SR_B2', 'SR_B1'], // 标准真彩色组合：红、绿、蓝
    min: 0.0,
    max: 0.3,
};

// 添加去云后的中值合成影像到地图
Map.addLayer(medianImage, visualization, 'Cleaned True Color (321)');

```

![[Pasted image 20250227165320.png]]