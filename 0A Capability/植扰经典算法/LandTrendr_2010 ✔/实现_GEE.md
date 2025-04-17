
## 0 个人思考

- <mark class="highlight">！！！！输入既可以是点，也可以是线、面，能兼容！!</mark> 
	- ![[Pasted image 20250312195400.png]]

<br>

- 需要看源码深入（还没看）

- 源码的输入是一个研究点，在运行模型时输入的影像是覆盖这个研究点的所有影像，存在限制。但是对于landtrendr算法来说，本身算法处理只涉及点的时序，点与点之间的关系似乎不会对算法本身产生影响？ （去云一般也是点的质量波段，不受其他点影响）所以当我需要某一个区域的结果时，可以通过多个点的输入，将区域都覆盖到，然后裁剪？ 或者buffer的取值更大，最后裁剪？感觉后者较前者稍微可靠些
- 一个问题：如果影像中存在其他土地类型，结果是否会因此受影响不准确？比如水体中的藻类？不过这部分只需要最后clip掉就可以了，因为点与点之间不互相影响（需要看一下源码
	![[Pasted image 20250305134607.png]]


## 1 源码实现（以覆盖某点为输入）
- Apps:[LT-GEE Pixel Time Series](https://emaprlab.users.earthengine.app/view/lt-gee-pixel-time-series) （**点计算**）
- 调用官方封装好的js文件（源码1500+行）
	- 源码：[LT-GEE/LandTrendr.js at master · eMapR/LT-GEE · GitHub](https://github.com/eMapR/LT-GEE/blob/master/LandTrendr.js)
- <mark class="highlight">注意</mark>：
	- <span style="color:red;">模型处理的影像范围</span>
	    - LandTrendr运行时处理的影像为所有覆盖aoi点的原始Landsat场景
		- 筛选逻辑：`filterCollection`函数中通过`filterBounds(aoi)`筛选**覆盖该点的Landsat影像**（如`LANDSAT/LC08/C02/T1_L2`）
	- <span style="color:red;">导出范围与运行时范围不一致 </span>
		- 用户代码中导出时使用`aoi.buffer(100000)`生成100公里缓冲区域，但此区域**仅在导出时生效**，模型运行时仍以原始点筛选影像

- 以下为官方示例脚本，可在官方文件中查看
	- ![[Pasted image 20250312210057.png]]

```cpp
//######################################################################################################## 
//#                                                                                                    #\\
//#                           LANDTRENDR GREATEST DISTURBANCE MAPPING                                  #\\
//#                                                                                                    #\\
//########################################################################################################


// date: 2018-10-07
// author: Justin Braaten | jstnbraaten@gmail.com
//         Zhiqiang Yang  | zhiqiang.yang@oregonstate.edu
//         Robert Kennedy | rkennedy@coas.oregonstate.edu
// parameter definitions: https://emapr.github.io/LT-GEE/api.html#getchangemap
// website: https://github.com/eMapR/LT-GEE
// notes: 
//   - you must add the LT-GEE API to your GEE account to run this script. 
//     Visit this URL to add it:
//     https://code.earthengine.google.com/?accept_repo=users/emaprlab/public
//   - use this app to help parameterize: 
//     https://emaprlab.users.earthengine.app/view/lt-gee-change-mapper


//##########################################################################################
// START INPUTS
//##########################################################################################

// define collection parameters
var startYear = 1985;
var endYear = 2017;
var startDay = '06-20';
var endDay = '09-20';
var aoi = ee.Geometry.Point(-122.8848, 43.7929);
// var aoi=table;
var index = 'NBR';
var maskThese = ['cloud', 'shadow', 'snow', 'water'];

// define landtrendr parameters
var runParams = { 
  maxSegments:            6,
  spikeThreshold:         0.9,
  vertexCountOvershoot:   3,
  preventOneYearRecovery: true,
  recoveryThreshold:      0.25,
  pvalThreshold:          0.05,
  bestModelProportion:    0.75,
  minObservationsNeeded:  6
};

// define change parameters
var changeParams = {
  delta:  'loss',
  sort:   'greatest',
  year:   {checked:true, start:1986, end:2017},
  mag:    {checked:true, value:200,  operator:'>'},
  dur:    {checked:true, value:4,    operator:'<'},
  preval: {checked:true, value:300,  operator:'>'},
  mmu:    {checked:true, value:11},
};

//##########################################################################################
// END INPUTS
//##########################################################################################

// load the LandTrendr.js module
var ltgee = require('users/emaprlab/public:Modules/LandTrendr.js'); 

// add index to changeParams object
changeParams.index = index;

// run landtrendr
var lt = ltgee.runLT(startYear, endYear, startDay, endDay, aoi, index, [], runParams, maskThese);
// print("lt",lt);

// get the change map layers
var changeImg = ltgee.getChangeMap(lt, changeParams);

// print("changeImg",changeImg);

// set visualization dictionaries
var palette = ['#9400D3', '#4B0082', '#0000FF', '#00FF00', '#FFFF00', '#FF7F00', '#FF0000'];
var yodVizParms = {
  min: startYear,
  max: endYear,
  palette: palette
};

var magVizParms = {
  min: 200,
  max: 800,
  palette: palette
};

// display the change attribute map - note that there are other layers - print changeImg to console to see all
Map.centerObject(aoi, 11);
Map.addLayer(changeImg.select(['mag']), magVizParms, 'Magnitude of Change');
Map.addLayer(changeImg.select(['yod']), yodVizParms, 'Year of Detection');

// var ValueImage=changeImg.select(['mag']);
// // 提取 breakTime 波段的值
// var Value = ValueImage.reduceRegion({
//   reducer: ee.Reducer.mean(), // 使用均值统计
//   geometry: aoi, // 分析区域
//   scale: 30 // 分辨率（单位：米）
// });
// print("Value",Value);


// export change data to google drive
var region = aoi.buffer(100000).bounds();
var exportImg = changeImg.clip(region).unmask(0).short();
// var exportImg = changeImg.clip(aoi).unmask(0).short();
Export.image.toDrive({
  image: exportImg, 
  description: 'lt-gee_disturbance_map', 
  folder: 'lt-gee_disturbance_map', 
  fileNamePrefix: 'lt-gee_disturbance_map', 
  region: aoi, 
  scale: 30, 
  crs: 'EPSG:5070', 
  maxPixels: 1e13
});
});
```

## 2 官方解释

- 官方文档：[https://emapr.github.io/LT-GEE](https://links.jianshu.com/go?to=https%3A%2F%2Femapr.github.io%2FLT-GEE)

### 1.1 代码逻辑

```cpp
var startYear = 1985;
var endYear   = 2010;

var coords = [[-123.925, 42.996],
              [-122.327, 42.996],
              [-122.327, 43.548],
              [-123.925, 43.548],
              [-123.925, 42.996]];

var aoi = ee.Geometry.Polygon(coords);


var run_params = { 
  maxSegments:            6,
  spikeThreshold:         0.9,
  vertexCountOvershoot:   3,
  preventOneYearRecovery: true,
  recoveryThreshold:      0.25,
  pvalThreshold:          0.05,
  bestModelProportion:    0.75,
  minObservationsNeeded:  6
};

//合成年度影像，采取先去云后取中值的方法
function reduceToSingleImageMockFunction(img) {
  // 使用 QA 波段过滤掉有云的像素
  var withCloudiness = img.map(function(image) {
    var qa = image.select('QA_PIXEL'); 
    var cloudMask = getCloudMask(qa); // 自定义函数，根据 QA 波段生成云掩码
    return image.updateMask(cloudMask);
  });

  // 计算中值合成影像
  var medianImage = withCloudiness.median();
  
  return medianImage;
}

// 根据 QA 波段生成云掩码的函数
function getCloudMask(qa) {
  // 在这里根据 QA 波段的具体格式定义云掩码。
  var cloudBitMask = 1 << 5; // 云标记位
  var mask = qa.bitwiseAnd(cloudBitMask).eq(0); // 如果第5位不是1，则认为不是云
  return mask;
}


for(var year = startYear; year <= endYear; year++) {
  var img = ee.ImageCollection('LANDSAT/LT05/C02/T1_L2')
              .filterBounds(aoi)
              .filterDate(year+'-06-15', year+'-09-15');
  
  //从每年的年度影像中，通过最适合的方法合成一张年度影像
  img = reduceToSingleImageMockFunction(img);  

  //此处以SR_B5波段为例，可以输入波段or某个指数
  var tempCollection = ee.ImageCollection(img.select(['SR_B5']));   
  
  //将每一年的年度影像合成一个合集
  if(year == startYear) {
    var srCollection = tempCollection;
	  } else {
	    srCollection = srCollection.merge(tempCollection); 
	  }
};


run_params.timeSeries = srCollection;

var LTresult = ee.Algorithms.TemporalSegmentation.LandTrendr(run_params);

print("LTresult",LTresult)

```

### 1.2 结果分析（没成功）
- LT-GEE的结果不能立即用于分析、显示或作为变化图或拟合的时间序列数据导出。
- 将每个像素视为需要解压缩的一束数据。每个像素的数据打包类似于Python或r中的嵌套列表。主列表看起来像这样：
	- 默认包含**LandTrendr** 和**rmse**
	![[Pasted image 20250305123421.png]]
	- **B5_fit** ：SR_B5波段的拟合曲线
```cpp
[[Annual Segmentation Info], Fitting RMSE, [Fitted Time Series n]]
```
![[Pasted image 20250305123302.png]]
![[Pasted image 20250312205944.png]]


