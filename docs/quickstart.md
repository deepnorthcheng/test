## Quick start

## Install necessary dependency Libraries
```bash
sudo apt-get install git
sudo apt-get install python-pip
sudo apt-get install python-dev
sudo apt-get install python-numpy
pip install opencv-python
```

## Check GPU environmental

If you do not use GPU environment, please skip

* [Check GPU Driver Installation](/gpu_driver.md)<br>
* [Check CUDA environment](/cuda8.0&cudnnv6.md)<br>

## Clone sunergy source from github
```bash
https://github.com/DeepNorthAI/Sunergy_multiPlatform
```



Go to Sunergy and download yolo-v3 model

```bash

cd Sunergy_multiPlatform/Sunergy/
wget -P model/ https://pjreddie.com/media/files/yolov3.weights
```

## Compile sunergy
Modify Makefile
```bash
gedit Makefile
```

Modify Makefile macro definition according to computer requirement
Use 1 and does not use 0, python_verson 2 or 3 corresponds to 2.7 and 3.5, respectively.
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

## Test sunergy

Execute yolov3 demo to detect dogs

```bash
python test.py
```
![http://yicun.vmaxx.tech:3000/mingzhe_pan/sunergy_multiplatform/src/master/docs/screenshot/dog.png](https://github.com/DeepNorthAI/Sunergy_multiPlatform/tree/master/docs/screenshot/dog.png)
