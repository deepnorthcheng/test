# GPU Environment Installation in Windows10
------------
## 1 CUDA
### 1.1 Download CUDA
* CUDA is a GPU-based deep learning computing architecture of NVIDIA
* Click [here](https://developer.nvidia.com/cuda-downloads) to download the installation package, select the version above 8.0 .
* Note to download the Local version. When patches are needed, download and install Base Installer first, and then download and install patches according to patch number .

### 1.2 Installation CUDA
* After downloading, click on .exe to install .<br>
![](/screenshot/CUDA/1.png)
* The system will automatically start the installer, chooses the custom mode, and click Next .<br>
![](/screenshot/CUDA/2.png)
* Installation options are all checked by default, click Next .<br> 
![](/screenshot/CUDA/3.png)
* Using default installation location, click Next .<br> 
![](/screenshot/CUDA/4.png)
* The installation is completed .<br>
![](/screenshot/CUDA/5.png)

### 1.3 Configure Environment Variables
  After installation, two environment variables CUDA_PATH and CUDA_PATH_V8_0 are configured by default. The remaining environment variables are configured as follows .
```
CUDA_BIN_PATH 		%CUDA_PATH%\bin
CUDA_LIB_PATH 		%CUDA_PATH%\lib\x64
CUDA_SDK_BIN_PATH	%CUDA_SDK_PATH%\bin\win64
CUDA_SDK_LIB_PATH	%CUDA_SDK_PATH%\common\lib\x64
CUDA_SDK_PATH		C:\Program Files\NVIDIA Corporation\CUDA Samples\v8.0
```
*Note: Please check whether the CUDA path is the same as that on your computer* .

### 1.4 Check the Installation State
* Enter command line cmd, input nvcc-V, NVIDIA driver and graphics card information should be displayed .



## 2 CuDNN
* Click [here](https://developer.nvidia.com/rdp/cudnn-download) to download the corresponding version of CuDNN, select the version above 6.0 .
* Need to register and log in for downloading .
* Decompress the downloaded compressed package .
* Copy the entire content to the installation path of CUDA. The default installation path of CUDA is **C: Program Files NVIDIA GPU Computing Toolkit CUDA v8.0** .