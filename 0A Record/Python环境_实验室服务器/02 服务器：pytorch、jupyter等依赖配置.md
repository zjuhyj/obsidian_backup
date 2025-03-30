1. [[#1 显卡、驱动、CUDA|1 显卡、驱动、CUDA]]
	1. [[#1 显卡、驱动、CUDA#1.1 显卡、驱动、CUDA介绍|1.1 显卡、驱动、CUDA介绍]]
	2. [[#1 显卡、驱动、CUDA#1.2 三者版本间的关系对应(服务器&本机)|1.2 三者版本间的关系对应(服务器&本机)]]
2. [[#2 pytorch安装|2 pytorch安装]]
	1. [[#2 pytorch安装#2.1 确认要安装的python版本，查看对应的pytorch版本|2.1 确认要安装的python版本，查看对应的pytorch版本]]
	2. [[#2 pytorch安装#2.2 根据python版本创建虚拟环境|2.2 根据python版本创建虚拟环境]]
	3. [[#2 pytorch安装#2.3 激活虚拟环境，安装pytorch|2.3 激活虚拟环境，安装pytorch]]
3. [[#3 常见依赖安装|3 常见依赖安装]]
4. [[#4 Jupyter Notebook 安装|4 Jupyter Notebook 安装]]
	1. [[#4 Jupyter Notebook 安装#4.1 安装命令|4.1 安装命令]]
	2. [[#4 Jupyter Notebook 安装#4.2 检验安装成功|4.2 检验安装成功]]





## 1 显卡、驱动、CUDA

### 1.1 显卡、驱动、CUDA介绍

- Nvidia显卡：计算机硬件
- Nvidia<span style="color:red;"><strong>显卡驱动</strong></span>：是一个<span style="color:red;"><strong>软件程序</strong></span>，操作系统和计算机应用程序与显卡进行通信，从而使用显卡的功能来加速图像和视频处理等任务
- <strong>CUDA</strong> ：NVIDIA推出的用于自家GPU的<span style="color:red;"><strong>并行计算框架</strong></span>，可以在NVIDIA的GPU上运行
    - 不是一个软件也不是一个纯硬件，属于软件+硬件架构统称
    - CUDA版本是<strong>可以向下兼容</strong>的
    - 与Nvidia显卡驱动并非一对一对应的关系
-  pytorch：调用需要调用cuda，所以要与cuda适配
    -<mark class="highlight"> 版本最好小于2.0.1</mark>
- tensorflow：调用需要调用cuda，所以要与cuda适配
    - 版本最好是2.x

### 1.2 三者版本间的关系对应(服务器&本机)
参考资料：https://zhuanlan.zhihu.com/p/686484960

- <span style="color:red;"><strong>显卡（硬件）——>能支持的最高驱动版本</strong></span>
- <span style="color:red;"><strong>你电脑装的驱动版本——>能支持的最高cuda</strong></span>
- <span style="color:red;"><strong>你电脑装的cuda版本——>能支持的最高pytorch版本</strong></span>

1. <mark class="highlight-gray">查看服务器/本机显卡</mark>
- 操作系统不同，命令不同
- NVIDIA 显卡：`nvidia-smi`（可一键直达显卡、驱动版本及**所能支持的最高CUDA版本**，注意：**并非是当前安装的CUDA版本**）

2. <mark class="highlight-gray">查看服务器/本机安装的驱动版本（可卸载、升级）</mark>
- NVIDIA显卡能支持的最高驱动版本，参考NVIDIA官网：https://www.nvidia.cn/geforce/drivers/
- `nvidia-smi`命令外查看方式：
	- 方式1：打开NVIDIA控制面板-系统信息-显示，查看电脑驱动版本
	- 方式2：或者win+r，输入DxDiag，点击【显示】2标签，显卡的名称与制造商会显示在设备栏位中，驱动程序的版本显示在驱动程序栏位中
- 未尝试自己卸载、升级驱动版本（不敢试哈哈哈）

3. <mark class="highlight-gray"> 查看服务器/本机安装的cuda版本（可卸载、升级）</mark>
- 驱动版本与cuda版本对应关系，如参考https://blog.csdn.net/qq_58611650/article/details/123450460
- 显卡算力查询[https://developer.nvidia.com/zh-cn/cuda-gpus#compute](https://developer.nvidia.com/zh-cn/cuda-gpus#compute)
- 未尝试自己卸载、升级CUDA版本（不敢试哈哈哈）

	1）查看本机能够支持的最高cuda版本
		- 方式1： Win+r，打开cmd，输入 `nvidia-smi`
		- 方式2：打开NVIDIA控制面板-系统信息-组件，查看cuda
	2）查看本机**当前安装的cuda版本**：输入命令`nvcc --version`

4. 实践：
	- 1、`nvidia-smi`命令一键直达
		1）服务器
		![[Pasted image 20250210142900.png]]
		2）本机
		![[Pasted image 20250210143215.png]]
	 - 2、查看当前安装的cuda版本：`nvcc --version`
		1）服务器
		![[Pasted image 20250210143311.png]]
		2）本机
		![[Pasted image 20250210143243.png]]

## 2 pytorch安装

### 2.1 确认要安装的python版本，查看对应的pytorch版本
- 百度查找pytorch、python版本对应关系
    - 如参考：https://blog.csdn.net/WOSHIRENXIN/article/details/127415609
	     ![[Pasted image 20250210143721.png]]
    - <mark class="highlight">我装的是py3.8，pytorch准备安装2.0的</mark>

### 2.2 根据python版本创建虚拟环境
- 注意虚拟环境存储路径（在安装 Miniconda 或 Anaconda时建议自定义）
- 输入命令：`conda create --name myenv`：
	![[Pasted image 20250210145212.png]]

- 安装成功：
	![[Pasted image 20250210145128.png|400]]

### 2.3 激活虚拟环境，安装pytorch
- 以`hyj_py38_torch2`环境为例
1. <mark class="highlight-gray">激活虚拟环境</mark>
	- 输入命令：`conda activate hyj_py38_torch2`
		 ![[Pasted image 20250210145402.png]]
2. <mark class="highlight-gray">查看pytorch版本、安装pytorch</mark>
- pytorch历史版本，官网查看下载：https://pytorch.org/ ，点击getstart，打开历史版本页面
	 ![[Pasted image 20250210153745.png]]
	![[Pasted image 20250210154325.png]]
- 查看对应安装命令：
```
conda install pytorch==2.0.0 torchvision==0.15.0 torchaudio==2.0.0 pytorch-cuda=11.8 -c pytorch -c nvidia
```
- 若没有联网，指定浙大镜像源安装
	- 浙大镜像源网站：https://mirrors.zju.edu.cn/
	- 能否ping通镜像源，输入命令：`ping 10.203.1.201`
		![[Pasted image 20250210164838.png]]
- <mark class="highlight">输入安装命令：参考浙大源网站</mark>
	![[Pasted image 20250210165212.png]]
	```
	pip install torch==2.0.0 torchvision==0.15.1 torchaudio==2.0.1 -i https://mirrors.zju.edu.cn/pypi/web/simple
	```

- 注意：若在安装过程中如果发现某些包没有安装完全，单独重装一下即可
	![[Pasted image 20250210154110.png]]
	- 命令：
		```
		conda install libnpp-dev-11.8.0.86
		conda insatll cuda-nvrtc-11.8.89
		```
 	
3. <mark class="highlight-gray">检验安装是否成功</mark>

- 检查pytorch是否安装成功
```
python3    #进入python环境 
import torch    #导入pytorch包 
print(torch.__version__)    #查看torch版本
```

- 检查安装的 PyTorch 是否能使用 GPU 运行
```
device = torch.device("cuda" if torch.cuda.is_available() else "cpu") print("Using device:", device)
#这会显示正在使用的设备类型。如果是 `"cuda"`，说明 PyTorch 将尝试使用 GPU；如果是 `"cpu"`，则说明没有找到合适的 GPU 或者 CUDA 环境未正确配置。
```
![[Pasted image 20250210172447.png]]

## 3 常见依赖安装

`pip install numpy pandas matplotlib`

## 4 Jupyter Notebook 安装

### 4.1 安装命令

- <mark class="highlight">注意</mark>：jupyter最新版本7.x别轻易装，会不兼容导致报错
- 输入安装命令，指定为以下版本
	 ![[Pasted image 20250211084749.png]]


### 4.2 检验安装成功
- 检验安装成功：`jupyter --version`
	![[Pasted image 20250211084733.png]]
- 输入启动命令：`jupyter notebook`
	![[Pasted image 20250211084825.png]]

- 检查jupyter进程：`ps aux | grep jupyter`
	- 删除进程：`kill PID`
