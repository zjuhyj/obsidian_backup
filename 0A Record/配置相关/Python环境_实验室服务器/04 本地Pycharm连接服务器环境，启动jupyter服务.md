1. [[#0 理解|0 理解]]
2. [[#1 连接到实验室服务器|1 连接到实验室服务器]]
3. [[#2 填写解释器对应路径|2 填写解释器对应路径]]
4. [[#3 配置文件同步（未做，自动生成）|3 配置文件同步（未做，自动生成）]]
5. [[#4 连接到服务器conda环境、配置jupyter服务|4 连接到服务器conda环境、配置jupyter服务]]




## 0 理解
- 在一个环境中，jupyter有且只有一个，它的配置文件是唯一的
- 不同的人可以在jupyter上起各自的服务，运行自己的代码程序
	![[Pasted image 20250214104859.png]]

## 1 连接到实验室服务器

- 进入解释器设置界面，选择相应选项
	![[Pasted image 20250210194422.png]]
- 连接高分服务器：之前已经连过，故选择existing
	![[Pasted image 20250210194342.png]]

## 2 填写解释器对应路径

- 查看服务器conda环境下的python解析器：激活相应conda环境，输入命令`which python3`
	![[Pasted image 20250210200957.png]]
- 填写相应路径
	- 在 `Interpreter` 字段中，输入 Conda 环境的 Python 路径 
	- 在 `Sync folders` 中，配置本地项目与远程服务器的同步目录。默认情况下，PyCharm 会将本地项目上传到远程服务器的 `/tmp` 目录下，可以根据需要修改。
	![[Pasted image 20250211085412.png]]

## 3 配置文件同步（未做，自动生成）
- Apply新链接后，好像pycharm自己自动生成了
	![[Pasted image 20250211085656.png]]
	![[Pasted image 20250211085715.png]]

## 4 连接到服务器conda环境、配置jupyter服务

- 连接到实验室对应conda环境
	![[Pasted image 20250211085729.png]]
- 终端切换至服务器终端
	![[Pasted image 20250211085744.png]]

- 激活相应环境，启动jupyter：` jupyter notebook --no-browser --ip=10.130.11.21 --port=8888`
	- 检验是否启动成功：能否打开网址
	- <mark class="highlight-red">这里总是会碰到问题</mark>：jupyter启动成功但浏览器服务进不去
	- 解决方式1：<mark class="highlight">启动jupyter时指定远程服务器ip</mark>
		-` jupyter notebook --no-browser --ip=10.130.11.21 --port=8888`
		-  jupyter启动命令里面的 -ip是设置能够访问这个jupyter服务的ip白名单；本地访问jupyter服务的时候填的ip是jupyter服务所在主机的ip地址。
			- `-ip=0.0.0.0` 失败：原因未知，可能与ip、网络接口被限制有关
			 ![[Pasted image 20250211091846.png]]
	- 解决方式2： SSL转发端口（未复现）
		- 本地终端输入：`ssh -L 8888:localhost:8888 zju@10.130.11.21`
			- zju：远程服务器用户名
			- 将本地的 `8888` 端口转发到远程服务器的 `localhost:8888`，所有发送到本地 `8888` 端口的请求，都会通过 SSH 隧道转发到远程服务器的 `8888` 端口
		- 进入服务器，激活环境，启动jupyter：` jupyter notebook --no-browser --ip=0.0.0.0 --port=8888`
			- 也许可以不用指定ip了？（服务被拒无法复现，也许也是ip=0.0.0.0失败的原因？可以多试几次或换网）
			- ![[Pasted image 20250211092439.png]]
	

- 配置jupyter：指向远程服务器的 IP 和端口（配置的ip与上面的解决方式1对应）
	![[Pasted image 20250211094045.png]]
	- 设置配置server：`http://10.130.11.21:8888`，并点击Apply
		- 默认是上面那个，好像会自动生成端口，你在本地连接远程的终端退出jupyter进程，那个端口的jupyter进程还在，需要手动kill
		- 如果采取解决方式2，ip可以尝试用本地的（未尝试）
	- 成功连接：
		![[Pasted image 20250211094135.png]]
	- 检验：
		- 输入代码：
		```
		import torch    #导入pytorch包  
		print(torch.__version__)    #查看torch版本  
		device = torch.device("cuda" if torch.cuda.is_available() else "cpu")  
		print("Using device:", device)
		```
		- 输入token(启动服务后第一次运行需要)
			![[Pasted image 20250211094334.png]]
		- 运行成功：
			![[Pasted image 20250211094450.png]]
		- 退出jupyter服务：ctrl+c
- 