# GPU Environment Installation in Windows10
------------
## 1 CUDA
### 1.1 Download CUDA
* CUDA is a general parallel computing architecture developed by NVIDIA, which enables GPU to solve complex computing problems .
* Click [here](https://developer.nvidia.com/cuda-toolkit-archive) to download the installation package, select the version above 8.0 .
* Download the Local type and corresponding patches .<br>
![](/screenshot/CUDA/0.png)

### 1.2 Install CUDA
* Double-click cuda_8.0.61_win10.exe to install .<br>
![](/screenshot/CUDA/1.png)
* Choose the custom mode, click Next .<br>
![](/screenshot/CUDA/2.png)
* All options are checked by default, click Next .<br> 
![](/screenshot/CUDA/3.png)
* Use default installation location, click Next .<br> 
![](/screenshot/CUDA/4.png)
* Finish installation .<br>
![](/screenshot/CUDA/5.png)

### 1.3 Configure Environment Variables
  Right-click  Computer, open Properties -> Advanced System Settings -> Environment Variable.<br>
  Configure environment variables in the System Variables as follows .
```
CUDA_PATH 		 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0
CUDA_PATH_V8_0		 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0
CUDA_BIN_PATH 		 %CUDA_PATH%\bin
CUDA_LIB_PATH 	      %CUDA_PATH%\lib\x64
CUDA_SDK_BIN_PATH	 %CUDA_SDK_PATH%\bin\win64
CUDA_SDK_LIB_PATH	 %CUDA_SDK_PATH%\common\lib\x64
CUDA_SDK_PATH		 C:\Program Files\NVIDIA Corporation\CUDA Samples\v8.0
```
*Note: Please check whether the CUDA path is consistent with that on your computer* .

### 1.4 Check the Installation State
* Open cmd, enter nvcc -V to check the installation state .<br>
![](/screenshot/CUDA/7.png)


## 2 cuDNN
* Click [here](https://developer.nvidia.com/cudnn) to download cuDNN, select the version above 6.0 .
* Register and log in for downloading .
* After downloading, decompress the downloaded compressed package .
* Copy cudnn64_6.dll, cudnn.h, cudnn.lib to the bin, include, lib\x64 folders under the path of CUDA respectively .<br> 
*The default path of CUDA is C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0* .
