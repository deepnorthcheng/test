## Quick start

-----

## Linux

### 1 Install necessary dependency Libraries
```bash
sudo apt-get install git
sudo apt-get install python-pip
sudo apt-get install python-dev
sudo apt-get install python-numpy
pip install opencv-python
```

### 2 Check GPU environmental

If you do not use GPU environment, please skip

* [Check GPU Driver Installation](gpu_driver.md)<br>
* [Check CUDA environment](cuda8.0&cudnnv6.md)<br>

### 3 Clone sunergy source from github
The folder includes five examples: object detection, face attribute prediction, pose estimation, cross-camera re-id and multi-object tracking.
```bash
git clone https://github.com/DeepNorthAI/Sunergy_multiPlatform.git
```


Go to Sunergy and download yolo-v3 model

```bash

cd Sunergy_multiPlatform/Sunergy/
wget -P model/ https://pjreddie.com/media/files/yolov3.weights
```

### 4 Compile sunergy
Modify Makefile
```bash
gedit Makefile
```

Modify Makefile macro definition according to computer requirement
Use 1 and does not use 0, python_version 2 or 3 corresponds to 2.7 and 3.5, respectively.
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

Save exit and compile
```bash
make clean
make
```

### 5 Test sunergy

Execute yolov3 demo to detect dogs

```bash
python demo.py
```
![http://yicun.vmaxx.tech:3000/mingzhe_pan/sunergy_multiplatform/src/master/docs/screenshot/dog.png](https://github.com/DeepNorthAI/Sunergy_multiPlatform/tree/master/docs/screenshot/dog.png)

-----

## Windows

### 1 IDE Environment
* Microsoft Visual Studio 2015

### 2 Check GPU environmental

If you do not use GPU environment, please skip
* [Check GPU Driver Installation and CUDA environment](cuda-windows.md)


### 3  Clone sunergy source from github
The folder includes five examples: object detection, face attribute prediction, pose estimation, cross-camera re-id and multi-object tracking.
```bash
git clone https://github.com/DeepNorthAI/Sunergy_multiPlatform.git
```

### 4 Open Projects
#### 4.1 Sunergy and Examples
   * Start VS2015, open project Sunergy.sln .
*This project includes Sunergy and four examples: object detection, face attribute prediction, pose estimation and cross-camera reid*

#### 4.2 Example : multi-object tracking
   * Start VS2015, open project example/c/tracking/tracking.sln .

### 5 Build and Run

#### 5.1 Sunergy and Examples
* Choose sunergy, right-click and choose build .
* Choose object detection/face attribute prediction/pose estimation/cross-camera re-id, right-click and choose build .
* Choose object detection/face attribute prediction/pose estimation/cross-camera re-id, right-click and choose set as startup project, and then press F5 to run .

*Note: These four examples all work as described above. Different examples correspond to different projects.*

#### 5.2 Example : multi-object tracking
* choose tracking, right-click and choose build .
* choose tracking, right-click and choose set as startup item, and then press F5 to run .

#### 5.3 See detailed information in  [Examples](example.md) 
