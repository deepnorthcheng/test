# sunergy快速入门

-----

## Linux 

### 1 安装必要依赖库
```bash
sudo apt-get install git
sudo apt-get install python-pip
sudo apt-get install python-dev
sudo apt-get install python-numpy
pip install opencv-python
```

### 2 检查GPU环境
若不使用GPU加速请跳过

* [检查GPU驱动安装](/zh-cn/gpu_driver.md)<br>
* [检查cuda环境](/zh-cn/cuda8.0&cudnnv6.md)<br>


### 3 从github克隆sunergy源代码
```bash
git clone https://github.com/VMaxxInc/Sunergy_multiPlatform.git
或者
git clone http://yicun.vmaxx.tech:3000/mingzhe_pan/sunergy_github.git

```

进入sunergy目录，下载yolo-v3模型
```bash

cd Sunergy_multiPlatform/Sunergy/
wget -P model/ https://pjreddie.com/media/files/yolov3.weights
```

### 4 编译sunergy
修改编译Makefile
```bash
gedit Makefile
```

根据电脑需求修改Makefile宏定义
使用为1不使用为0，python_verson为2或者3分别对应2.7和3.5
```bash
GPU=1
CUDNN=1
OPENCV=0
OPENMP=1
DEBUG=0
PYTHON=1
LIBPATH=1
TESTTIME=0
NNPACK=0
PYTHON_VERSON=2
```

保存退出，然后编译
```bash
make
```

### 5 测试sunergy
执行yolov3 检测dog的demo
```bash
python test.py
```
![http://yicun.vmaxx.tech:3000/mingzhe_pan/sunergy_multiplatform/src/master/docs/screenshot/dog.png](https://github.com/VMaxxInc/Sunergy_multiPlatform/blob/master/docs/screenshot/dog.png)

-----

## Windows

### 1 IDE运行环境
* VS2015

### 2 GPU环境
Windows下GPU环境安装请参考 [这里](/zh-cn/cuda-windows.md)
* CUDA8.0
* CuDNN6.0

### 3 下载代码
文件夹中包括opencv,sunergy库,以及四个例子：目标检测, 人脸属性预测, 位姿识别, 行人追踪
```bash
git clone https://github.com/VMaxxInc/Sunergy_multiPlatform.git
或者
git clone http://yicun.vmaxx.tech:3000/mingzhe_pan/sunergy_github.git

```

### 4 打开项目
#### 4.1 项目：目标检测, 人脸属性预测, 位姿识别
   * 运行VS2015,打开项目Sunergy.sln .

#### 4.2 项目：行人追踪
   * 运行VS2015,打开项目example/c/tracking/tracking.sln .


### 5 生成解决方案及运行

#### 5.1 项目：目标检测, 人脸属性预测, 位姿识别
* 选择项目sunergy,右击鼠标选择生成解决方案 .
* 选择项目object detection/face attribute prediction/pose estimation,右击鼠标选择生成解决方案 .
* 选择项目object detection/face attribute prediction/pose estimation,右击鼠标选择设为启动项,F5执行 .

#### 5.2 项目：行人追踪
* 选择项目tracking,右击鼠标选择生成解决方案 .
* 选择项目tracking,右击鼠标选择设为启动项,F5执行 .

### 6 附
* 具体项目配置信息参考 [例子](/zh-cn/example.md)