# Windows10下GPU环境安装
------------
## 1 CUDA
### 1.1 CUDA下载
* CUDA是NVIDIA公司基于GPU的深度学习计算架构
* 从 [这里](https://developer.nvidia.com/cuda-downloads) 下载安装包，选择的版本为8.0+
* 注意要下载Local版本，有patch（补丁）要下载时就先下载安装Base Installer，再按照patch编号从小到大下载安装patch
+6
### 1.2 CUDA安装
* 下载结束后，点击.exe进行安装<br>
![](/screenshot/CUDA/1.png)
* 系统自动启动安装程序，选择自定义模式，点击下一步<br> 
![](/screenshot/CUDA/2.png)
* 安装选项默认全部勾选，点击下一步<br>
![](/screenshot/CUDA/3.png)
* 默认安装位置，点击下一步<br> 
![](/screenshot/CUDA/4.png)
* 安装结束<br> 
![](/screenshot/CUDA/5.png)

### 1.3 配置环境变量
  安装完成后默认配置了CUDA_PATH和CUDA_PATH_V8_0两个环境变量，如下配置剩余环境变量
```
CUDA_BIN_PATH 		%CUDA_PATH%\bin
CUDA_LIB_PATH 		%CUDA_PATH%\lib\x64
CUDA_SDK_BIN_PATH	%CUDA_SDK_PATH%\bin\win64
CUDA_SDK_LIB_PATH	%CUDA_SDK_PATH%\common\lib\x64
CUDA_SDK_PATH		C:\Program Files\NVIDIA Corporation\CUDA Samples\v8.0
```
*注意：请确认CUDA的路径与您电脑上的一致*

### 1.4 检查安装情况
* 进入命令行cmd，输入nvcc -V，应输出NVIDIA驱动及显卡信息



## 2 CuDNN
* 从 [这里](https://developer.nvidia.com/rdp/cudnn-download) 下载对应版本的CuDNN，选择的版本为
* 下载时需要注册登录
* 将下载得到的压缩包解压缩
* 将全部内容复制到CUDA的安装路径中，CUDA默认安装路径为 **C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0**