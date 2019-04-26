# sunergy

## 安装必要依赖库
```bash
sudo apt-get install git
sudo apt-get install python-pip
sudo apt-get install python-dev
sudo apt-get install python-numpy
pip install opencv-python
```

## 从github克隆sunergy源代码
```bash
git clone https://github.com/VMaxxInc/Sunergy_multiPlatform.git
```

进入sunergy目录，下载模型
```bash
cd Sunergy_multiPlatform/PySunergy/
wget -P model/ https://pjreddie.com/media/files/yolov3.weights
```

## 编译sunergy
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

## 测试sunergy
执行yolov3 检测dog的demo
```bash
python demo.py
```


