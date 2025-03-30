- Apps：[BFASTmonitorGEE](https://andreim.users.earthengine.app/view/bfastmonitor)
- 官方源码：[GitHub - bfast2/geeBfastMonitor: Google Earth Engine implementation of the popular R bfast package](https://github.com/bfast2/geeBfastMonitor)

## 0 收获

- <mark class="highlight">一定要学会看GEE的输出结果的结构，然后去获得自己想要的值！！</mark>
<br>
- <mark class="highlight">输入是点，但线、面也可以兼容！!</mark>

## 1 GEE实现
- 看官方文档的模块介绍，结果输出自己gpt+debug搞出来的
	- ![[Pasted image 20250312205056.png]]

```cpp
// 加载模块
var engine = require('users/andreim/geeMonitor:monitor.js');

// 定义研究区域
var roi = ee.Geometry.Point([-122.8848, 43.7929]);
// var roi = table;//输入是区域

// 设置时间范围和参数
var historyStart = '2013-01-01';
var historyEnd = '2016-12-31';
var monitoringStart = '2017-01-01';
var monitoringEnd = '2018-12-31';

var h = 0.25;
var period = 10;
var alpha = 0.05;
var magnitudeThreshold = -0.000000000000000000000000000000121;
var harmonics = 1;

// 调用 bfastMonitor 函数
var result = engine.bfastMonitor(roi, historyStart, historyEnd, monitoringStart, monitoringEnd, h, period, alpha, magnitudeThreshold, harmonics);

// 检查返回值
print('bfastResults:', result.bfastResults);

var bfastResults=result.bfastResults;

// 将 ImageCollection 转换为 List
var imageList = bfastResults.toList(bfastResults.size());

// 获取第 2 张图像（索引为 1，因为索引从 0 开始）
var secondImage = ee.Image(imageList.get(1));

// 打印第 2 张图像的信息
print('Second Image:', secondImage);
print('Second Image Band Names:', secondImage.bandNames());

// var ValueImage=secondImage.select(['breakTime']);
// // 提取 breakTime 波段的值
// var ValueRegion=roi.buffer(100).bounds();
// var Value = ValueImage.reduceRegion({
//   reducer: ee.Reducer.mean(), // 使用均值统计
//   geometry: ValueRegion, // 分析区域
//   scale: 30 // 分辨率（单位：米）
// });
// print("Value",Value);


var region = roi.buffer(10000).bounds();
var exportImg = secondImage.clip(region).unmask(0).short();
// var exportImg = secondImage.clip(table).unmask(0).short();//输入是区域
Map.addLayer(exportImg, {min: 2013, max: 2016, palette: '00BFFF,CC2EFA,A901DB,6A0888,5858FA,0101DF,2E2EFE,0B0B61'}, "Second Image");

// 如果需要，可以将图像添加到地图中
// Map.addLayer(secondImage, {min: 2017, max: 2018, palette: '00BFFF,CC2EFA,A901DB,6A0888,5858FA,0101DF,2E2EFE,0B0B61'}, "Second Image");

// 设置地图中心
Map.centerObject(roi, 10);
```

