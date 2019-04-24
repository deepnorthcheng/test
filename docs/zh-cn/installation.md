## 安装

安装之前，请浏览本指南，注意所需平台的信息。Sunergy是运行在Ubuntu 16.04 - 12.04 及 Windows10 上的深度学习框架。

-------

## Linux

## 1 平台信息

- [Ubuntu 安装](https://www.ubuntu.com/download/desktop) *标准平台*

## 2 概述

- [依赖项](#依赖项)
- [编译](#编译)

在更新Sunergy库之前，我们建议在编译之前执行 `make clean`。

### **依赖项**

Sunergy平台有一些依赖项:

* [CUDA](https://developer.nvidia.com/cuda-zone) 运行GPU模式必须的依赖项.
    * 我们建议使用CUDA 8+和最新CUDA库。
* [CuDNN](https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/cudnn/cudnn-8.0-linux-x64-v6.0.tgz) 
    * 我们建议使用CuDNN 6+和最新CuDNN库。

选择依赖项:

* [NNPACK](https://github.com/Maratyszcza/NNPACK) 
    *NNPACK是Facebook研发的基于深度学习的加速库，主要用于多CPU核进行线程级加速运算。
* [Pthread]POSIX 线程: 可运行在 Solaris、FreeBSD、Linux 等平台.


Pysunergy 是基于python的Sunergy接口动态库，对python版本有限制。

* 对于Python版本的:  `Python 2.7` 或 `Python 3.3+`, `numpy (>= 1.7)`

**CUDA + cuDNN Sunergy**: 为了实现最快的操作，Sunergy通过CUDA和cuDNN加速。在安装Sunergy时，安装cuDNN，然后在“Makefile”中的修改如下“GPU:=1 CUDNN:=1”标志。加速是自动的。

**CPU-only Sunergy**: Sunergy支持纯CPU下的操作，在“MakefilE”中修改如下“GPU：=0 CUDNN：=0” 用于配置和构建没有CUDA的Sunergy。这对于云或集群部署很有帮助。

#### CUDA and CUDNN

在GPU模式下，Sunergy需要CUDA提供的`nvcc`编译器编译GPU代码。
安装CUDA，请转到[NVIDIA CUDA网站]（https://developer.nvidia.com/cuda-downloads）按照安装说明进行安装。*警告！** 331.*CUDA驱动程序系列有一个关键的性能问题：不要使用它。

为了获得最佳性能，Sunergy可以通过[NVIDIA cuDNN]（https://developer.nvidia.com/cudnn）加速。在cuDNN站点免费注册，安装它，然后继续执行安装说明。



#### Python

你可以安装python依赖项通过以下命令

    pip install -r requirements.txt

但是我们建议首先安装[Anaconda]（https://store.continuum.io/cshop/anaconda/）Python发行版，它提供了大多数必需的包，以及“hdf5”库依赖性。

*Sunergy的Python接口与Python 2.7/3.3+ 一起工作。

### **编译**

Sunergy可以通过Make命令进行相应编译。

* 通过Make命令进行编译

通过编译修改后的Makefile文件可以得到你需要的版本。 默认版本是可以正常工作的。

    make all
    make install
    make example

**分布**:

**速度**: 

现在你已经安装好Sunergy。

-----

## Windows

## 1 平台信息
* Windows10

## 2 IDE运行环境
* Microsoft Visual Studio 2015
   * 从 [这里](https://visualstudio.microsoft.com/zh-hans/downloads/) 下载  
   * CUDA8.0不支持VS2017版本，因此建议使用VS2015
   * 在安装GPU环境前应先安装好VS
  
## 3 依赖项
* [CUDA](https://developer.nvidia.com/cuda-toolkit-archive) 运行GPU模型必须的依赖项  
  * 建议使用CUDA8+和最新的CUDA库  
* [CuDNN](https://developer.nvidia.com/cudnn) 加速包  
  * 建议使用CuDNN6+和最新的CuDNN库