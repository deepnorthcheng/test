## Installation

Prior to installing, have a glance through this guide and take note of the details for your platform.
We install and run Sunergy on Ubuntu 16.04–12.04 and Windows10 .

-----

## Linux

## 1 Platform

- [Ubuntu installation](https://www.ubuntu.com/download/desktop) *the standard platform*

## 2 Overview

- [Prerequisites](#prerequisites)
- [Compilation](#compilation)


When updating Sunergy, it's best to `make clean` before re-compiling.

### **Prerequisites**

Sunergy has several dependencies:

* [CUDA](https://developer.nvidia.com/cuda-zone) is required for GPU mode.
    * library version 8+ and the latest driver version are recommended 
* [CuDNN](https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/cudnn/cudnn-8.0-linux-x64-v6.0.tgz) 
    * library version 6+ and the latest driver version are recommended 

Optional dependencies:

* [NNPACK](https://github.com/Maratyszcza/NNPACK) 
    *NNPACK, developed by Facebook, is an acceleration package for accelerating neural network      	computing. NNPACK can improve the performance of convolution layer computing on multi-core CPU   	 platform.
* [Pthread]POSIX thread: run on Solaris、FreeBSD、Linux platform.


Pysunergy interfaces have their own natural needs.

* For Python Sunergy:  `Python 2.7` or `Python 3.3+`, `numpy (>= 1.7)`

**CUDA + cuDNN Sunergy**: for fastest operation Sunergy is accelerated by drop-in integration of CUDA and cuDNN. To speed up your Sunergy models, install cuDNN then uncomment the `GPU :=1 CUDNN := 1` flag in `Makefile` when installing Sunergy. Acceleration is automatic. 

**CPU-only Sunergy**: for cold-brewed CPU-only Sunergy uncomment the `GPU :=0 CUDNN := 0` flag in `MakefilE` to configure and build Sunergy without CUDA. This is helpful for cloud or cluster deployment.

#### CUDA and CUDNN

Sunergy requires the CUDA `nvcc` compiler to compile its GPU code and CUDA driver for GPU operation.
To install CUDA, go to the [NVIDIA CUDA website](https://developer.nvidia.com/cuda-downloads) and follow installation instructions there. Install the library and the latest standalone driver separately; the driver bundled with the library is usually out-of-date. **Warning!** The 331.* CUDA driver series has a critical performance issue: do not use it.

For best performance, Sunergy can be accelerated by [NVIDIA cuDNN](https://developer.nvidia.com/cudnn). Register for free at the cuDNN site, install it, then continue with these installation instructions.


#### Python

You can install the dependencies with

    pip install -r requirements.txt

but we suggest first installing the [Anaconda](https://store.continuum.io/cshop/anaconda/) Python distribution, which provides most of the necessary packages, as well as the `hdf5` library dependency.

*Sunergy's Python interface works with Python 2.7. Python 3.3+ should work out of the box without protobuf support. For protobuf support please install protobuf 3.0 alpha (https://developers.google.com/protocol-buffers/). Earlier Pythons are your own adventure.*


### **Compilation**

Sunergy can be compiled with Make. 

* Compilation with Make

Configure the build by copying and modifying the example `Makefile` for your setup. The defaults should work.

    make all
    make install
    make example

**Distribution**:

**Speed**: 

Now that you have installed Sunergy.

-----

## Windows10

## 1 Platform
* install and run Sunergy on Windows10

## 2 IDE Environment
* Visual Studio  
   * Click [here](https://visualstudio.microsoft.com/zh-hans/downloads/) to download  
   * CUDA 8.0 does not support VS2017, so VS2015 is recommended
   * Visual Studio should be installed before installing GPU environment
  
## 3 Prerequisites
* [CUDA](https://developer.nvidia.com/cuda-downloads) is required for GPU mode  
  * library version 8+ and the latest driver version are recommended   
* [CuDNN](https://developer.nvidia.com/rdp/cudnn-download) is an accelerator package  
  * library version 6+ and the latest driver version are recommended