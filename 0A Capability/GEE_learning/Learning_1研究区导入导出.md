来源：[云羽徐徐的个人空间-云羽徐徐个人主页-哔哩哔哩视频](https://space.bilibili.com/397763925?spm_id_from=333.1391.0.0)

## 1 导入研究区

### 1.1 行政区划获取
- [DataV.GeoAtlas地理小工具系列](https://datav.aliyun.com/portal/school/atlas/area_selector)
- [mapshaper](https://mapshaper.org/)

### 1.2 GEE加载

（1） **通过矢量shp文件导入**
- 注意点：
	- 打包成.zip文件导入
	- 文件名不能有中文

- 方式1：
```cpp
//按文件名导入研究区
var roi = ee.FeatureCollection("projects/ee-zjuhyj2/assets/jinhua");

//在地图中央加载研究区边界，并显示为黄色
Map.centerObject(roi,10);  //10是放大倍数
Map.addLayer(roi,{'color':'yellow'},'研究区');
```

![[Pasted image 20250227101555.png]]

- 方式2：
```cpp
//通过交互界面导入文件
(红框部分)

//在地图中央加载研究区边界，并显示为黄色
var roi = table;
Map.centerObject(roi,10);
Map.addLayer(roi,{'color':'yellow'},'研究区');
```

![[Pasted image 20250227102321.png]]

（2） **通过代码设置研究区导入**

```cpp
//按经纬度导入研究区
var roi = ee.Geometry.Rectangle([115,39,117,41]); 

//在地图中央加载研究区边界，并显示为黄色
Map.centerObject(roi,10);
Map.addLayer(roi,{'color':'yellow'},'研究区');
```

![[Pasted image 20250227103052.png]]

（3） 在地图上创建

![[Pasted image 20250227105016.png]]

```cpp
//****地图上手动创建(红框部分)****//

var roi = geometry; 

//在地图中央加载研究区边界，并显示为黄色
Map.centerObject(roi,10);
Map.addLayer(roi,{'color':'yellow'},'研究区');
```

- 运行后有两个图层
	- 左边：创建的；右边：加载的
	![[Pasted image 20250227104921.png]]

## 2 导出研究区



```cpp
//****手动绘制****//

var roi = geometry; 

//在地图中央加载研究区边界，并显示为黄色
Map.centerObject(roi,10);
Map.addLayer(roi,{'color':'yellow'},'研究区');

//导出研究区域的shapefile文件
Export.table.toDrive({       //Export函数导入到谷歌云盘
  collection:ee.FeatureCollection([ee.Feature(roi)]), 
  description:'learning_shapefile',  //文件名
  folder:'GEE_exports',  //导入到谷歌云盘的文件夹
  fileFormat:'SHP'   //导出的文件格式
})


```

- 运行代码后，打开Tasks，点击run
	 ![[Pasted image 20250227105938.png]]

- 运行结束后，打开云盘全选下载到本地
- 解压后，选择.shp文件到Arcgis Pro中查看效果