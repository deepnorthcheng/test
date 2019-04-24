## 跨摄像头重识别

这个例子展示了如何在Sunergy上使用深度学习模型进行特征提取.

-----

## 在**Linux**上使用:

#### 1. 进入Sunergy目录并编译
```python
cd Sunergy
make
```

#### 2. 将 libsunergy.so 复制到跨摄像头重识别文件夹

```pyhton
cp -i lib/linux/libsunergy.so example/python/cross-camera_re-id
```

#### 3. 进入跨摄像头重识别文件夹

```python
cd example/python/cross-camera_re-id
```

#### 4. 配置图片和模型

####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

```python
	"../../model/extract/re_id.names";
	"../../model/extract/re_id.cfg";
	"../../model/extract/re_id.weights";
	"../../model/extract/test.jpg";
```
&nbsp;&nbsp; **re_id.names** 文件是所要预测属性的值
&nbsp;&nbsp; **re_id.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **re_id.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **test.jpg** 文件是您需要特征提取的图片

#### 5. 运行程序

```python
	python cross-camera_re-id.py
```

-------

## 在 **Windows**上使用:  

### C程序

#### 1. 启动VS2015, 打开项目 Sunergy.sln, 解决方案配置选择 x64 和 Release .

#### 2. 配置图片和模型 .

####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

```C
char names[] = "../../model/extract/re_id.names";
char cfg_file[] = "../../model/extract/re_id.cfg";
char weight_file[] = "../../model/extract/re_id.weights";
char image_file[] = "../../model/extract/test.jpg";
```

&nbsp;&nbsp; **re_id.names** 文件是所要预测属性的值
&nbsp;&nbsp; **re_id.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **re_id.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **test.jpg** 文件是您需要特征提取的图片

#### 3. 选择项目sunergy，右击鼠标属性，配置类型选择为静态库(.lib)，之后右击鼠标选择生成解决方案 .
#### 4. 选择项目corss-camera_re-id，右击鼠标选择生成解决方案 .
####  &nbsp;&nbsp;&nbsp;&nbsp; 再次右击鼠标将其设为启动项目，并运行.


### python程序

#### 1. 启动VS2015，打开Sunergy.sln，解决方案配置选择 x64 和 Release .
#### 2. 选择项目sunergy，右击鼠标属性，配置类型选择为动态库(.dll)，右击鼠标选择生成解决方案 .
#### 3. 将 lib/windows下的libsunergy.dll 拷贝至 example/python/cross-camera_re-id 目录下，将其重命名为libsunergy.pyd .
#### 4. 检查 cross-camera_re-id.py 程序中的文件路径是否正确 .

```python
"../../model/extract/re_id.names"
"../../model/extract/re_id.cfg"
"../../model/extract/re_id.weights"
"../../model/extract/test.jpg"
```

&nbsp;&nbsp; **re_id.names** 文件是所要预测属性的值
&nbsp;&nbsp; **re_id.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **re_id.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **test.jpg** 文件是您需要特征提取的图片

#### 5. 打开命令行，运行程序 .
```python
cd example/python/cross-camera_re-id
python cross-camera_re-id.py
```

--------

&nbsp;
#### *参考代码:* 
 
#### C
```C
#include "Sunergy.h"
#include <stdio.h>
#ifdef _WIN32
#include "io.h"
#else
#include <unistd.h>
#include <fcntl.h>
#endif

#include "opencv2/highgui/highgui_c.h"
#include "opencv2/imgproc/imgproc_c.h"


void set_data(char* file, sunergy_data_t* data)
{
	IplImage* src = 0;
	src = cvLoadImage(file, 1);
	data->width = src->width;
	data->height = src->height;
	data->step = src->width * 3;
	data->batch = 1;
	data->channel = 3;
	data->data_location = CPU_DATA;
	data->format = BGR;
	data->preprocessing = false;
	data->type = DATA_UCHAR;
	data->data = src->imageData;
}


void show_image_extract(char* file, extract_out_t* out)
{
	IplImage* src = cvLoadImage(file, 1);
	cvNamedWindow("extract", CV_WINDOW_AUTOSIZE);
	CvPoint lt, rb;
	CvScalar color;
	
	cvShowImage("extract", src);
	cvWaitKey(0);
	cvReleaseImage(&src);
	cvDestroyWindow("extract");
}


int main(int argc, char** argv)
{
	char names[] = "../../model/extract/re_id.names";
	char cfg_file[] = "../../model/extract/re_id.cfg";
	char weight_file[] = "../../model/extract/re_id.weights";
	char image_file[] = "../../model/extract/test.jpg";
	FILE* fp0, *fp1, *fp2, *fp3;
	fp0 = fopen(names, "r");
	fp1 = fopen(cfg_file, "r");
	fp2 = fopen(weight_file, "r");
	fp3 = fopen(image_file, "r");
	if (fp0 == NULL || fp1 == NULL || fp2 == NULL || fp3 == NULL)
	{
		printf("please run modle.sh\n");
		exit(0);
	}
	int gpuid = 0;
	bool inference = true;
	int ret = 0;
	sunergy_data_t data;
	extract_out_t out;
	net_information_t info;
	sunergy_t sun = sunergy_init(names, cfg_file, weight_file, gpuid, inference);
	sunergy_get_network_infomation(sun, 1, &info);
	set_sunergy_param(sun, 0, 0, 255, 0.25, 768, 576, 1);
	set_data(image_file, &data);
	printf("start\n");

	ret = sunergy_extract(sun, data, &out);

	for (int i = 0; i < out.size; i++)
	{
		printf("%.8f \n",out.feat[i]);
	}
	
	//show_image_extract(image_file, &out);
	ret = sunergy_free(sun);

	system("pause");
}


```


#### python
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import libsunergy as sun
import cv2
import time
import numpy as np

def extract():
    exnet1 = sun.init("../../model/extract/re_id.names",#names file
             "../../model/extract/re_id.cfg",#cfg file
             "../../model/extract/re_id.weights")#weight file
    exnet = exnet1[0]['index']
    nms=0.4 #param of detect
    means=127.5 # image reduce means before run in network
    normalization=128 #image normalization coefficient
    threshold=0.24#detect threshold
    video_w= 640#image real width used by detect and pose
    video_h=424#image real height used by detect and pose
    resize_format = 0 # 0: darknet 1: opencv 2:caffe
    sun.set_param(exnet,nms,means,normalization,threshold,video_w,video_h, resize_format)

    #frame = cv2.imread("/home/al/pytorch2sunergy/reid_sunergy/test.jpg")
    frame = cv2.imread("../../model/extract/test.jpg")
    # cv2.imshow("1",frame)
    frame1 = cv2.resize(frame, (112, 112))
    frame = cv2.cvtColor(frame1, cv2.COLOR_BGR2RGB)
    img={"width":frame.shape[1],#image width
         'height':frame.shape[0],#image height
         'step':frame.shape[2]*frame.shape[1],#image step
         'channel':frame.shape[2],#image channel
         'batch':1,#image batch
         'location':0,#location of image data 0:CPU 1:GPU
         'preprocessing':0,#image data run in network directly,0:image with no preprocessing 1: image have preprocessed
         'format':1,#format of image data 0:RGB 1:BGR 2: RGP 3:BGRP
         'type':1,#type of image data 0: char 1: float
        'data':frame.data}#image data

    ret = sun.extract(exnet,img)
    print ((ret))
    #print np.array(ret)
    #f = open('feature.txt', 'wb')
    #f.write(str(np.array(ret)))
    #f.close()
    sun.free(exnet)

if __name__ == "__main__":
   extract()
```
