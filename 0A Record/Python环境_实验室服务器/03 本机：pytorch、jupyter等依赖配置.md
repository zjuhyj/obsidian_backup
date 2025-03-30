1. [[#1 官网安装miniconda，清华镜像源|1 官网安装miniconda，清华镜像源]]
2. [[#2 修改配置，更改镜像源|2 修改配置，更改镜像源]]
3. [[#3 查看conda信息|3 查看conda信息]]
4. [[#4 安装所需依赖|4 安装所需依赖]]




## 1 官网安装miniconda，清华镜像源
- conda4.9版本base python环境3.9
- 安装过程：<span style="color:red;"><strong>路径更改到d盘</strong></span>，自动增加环境变量，生成py3.9的base环境
- 路径更改到d盘后，创建的虚拟环境路径也会自动到d盘，可以win+r，打开cmd输入`conda info`命令查看
```
#查看conda信息
conda info
```

## 2 修改配置，更改镜像源

- 打开浙大镜像源https://mirrors.zju.edu.cn/docs/pypi/ ，转到网址https://mirrors.zju.edu.cn/docs/anaconda/ ，按照教程，修改配置信息
	 ![[Pasted image 20250210164015.png]]

## 3 查看conda信息
- 能够看到镜像源和虚拟环境默认安装地址（第一个）都已经改变
![[Pasted image 20250210164228.png]]

## 4 安装所需依赖
- 参见[[02 服务器：pytorch、jupyter等依赖配置]]