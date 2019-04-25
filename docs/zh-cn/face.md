## 人脸属性预测

这个例子展示了如何在Sunergy上使用深度学习模型进行人脸属性预测，如：年龄，性别，情绪等。本文以年龄预测为例。

----------

## 在**Linux**上使用:

#### 1. 进入Sunergy目录并编译
```python
cd Sunergy
make
```

#### 2.  将 libsunergy.so 复制到目标检测文件夹

```pyhton
cp -i lib/linux/libsunergy.so example/python/face_attribute_prediction
```

#### 3. 进入人脸属性预测文件夹

```python
cd example/python/face_attribute_prediction
```

#### 4.读取图片和模型

####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

```python
"../../model/classifier/age1.1.names";
"../../model/classifier/age1.1.cfg";
"../../model/classifier/age1.1.weights";
"../../model/classifier/en.jpg";
```

&nbsp;&nbsp; **age1.1.names** 文件是所要预测属性的值，如本例中要预测年龄，则文件内容为年龄值 0，1，2，3 ... 100  
&nbsp;&nbsp; **age1.1.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **age1.1.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **en.jpg** 文件是您需要预测人脸属性的图片

#### 5. 运行程序

```python
python face_attribute_prediction.py
```

---------

## 在 **Windows**上使用: 

### C程序

#### 1. 启动VS2015, 打开项目 Sunergy.sln, 解决方案配置选择 x64 和 Release .

#### 2. 读取图片和模型

####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

```
char names[] = "../../model/classifier/age1.1.names";
char cfg_file[] = "../../model/classifier/age1.1.cfg";
char weight_file[] = "../../model/classifier/age1.1.weights";
char image_file[] = "../../model/classifier/en.jpg";
```
&nbsp;&nbsp; **age1.1.names** 文件是所要预测属性的值，如本例中要预测年龄，则文件内容为年龄值 0，1，2，3 ... 100  
&nbsp;&nbsp; **age1.1.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **age1.1.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **en.jpg** 文件是您需要预测人脸属性的图片

#### 3. 选择项目Sunergy，右击鼠标属性，配置类型选择为静态库(.lib)，之后右击鼠标选择生成解决方案 .
#### 4. 选择项目face_attribute_prediction，右击鼠标选择生成解决方案 .
####  &nbsp;&nbsp;&nbsp;&nbsp; 再次右击鼠标将其设为启动项目，并运行 .

### python程序

#### 1. 启动VS2015，打开Sunergy.sln, 解决方案配置选择 x64 和 Release .
#### 2. 选择项目sunergy，右击鼠标属性，配置类型选择为动态库(.dll)，之后右击鼠标选择生成解决方案 .
#### 3. 将 lib/windows 下的 libsunergy.dll 拷贝至 example/python/face_attribute_prediction 目录下，将其重命名为libsunergy.pyd .
#### 4. 读取图片和模型

####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

```python
"../../model/classifier/age1.1.names"
"../../model/classifier/age1.1.cfg"
"../../model/classifier/age1.1.weights"
"../../model/classifier/en.jpg"
```

&nbsp;&nbsp; **age1.1.names** 文件是所要预测属性的值，如本例中要预测年龄，则文件内容为年龄值 0，1，2，3 ... 100  
&nbsp;&nbsp; **age1.1.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **age1.1.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **en.jpg** 文件是您需要预测人脸属性的图片

#### 5. 打开命令行，运行程序 .
```python
cd example/python/face_attribute_prediction
python face_attribute_prediction.py
```


----------

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

void show_image_face(char* file, detect_out_t* out)
{
	IplImage* src = cvLoadImage(file, 1);
	cvNamedWindow("face", CV_WINDOW_AUTOSIZE);
	CvPoint lt, rb;
	CvScalar color;
	for (int i = 0; i < out->total_num; i++)
		cvRectangle(src, cvPoint(out->info[i].left, out->info[i].top),
			cvPoint(out->info[i].right, out->info[i].bottom), cvScalar(255, 0, 0, 0), 3, 4, 0);
	cvShowImage("face", src);
	cvWaitKey(0);
	cvReleaseImage(&src);
	cvDestroyWindow("face");
}

int main(int argc, char** argv)
{
	char names[] = "../../model/classifier/age1.1.names";
	char cfg_file[] = "../../model/classifier/age1.1.cfg";
	char weight_file[] = "../../model/classifier/age1.1.weights";
	char image_file[] = "../../model/classifier/en.jpg";
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
	int top = 5;
	sunergy_data_t data;
	classifier_out_t* out;
	net_information_t info;
	sunergy_t sun = sunergy_init(names, cfg_file, weight_file, gpuid, inference);
	sunergy_get_network_infomation(sun, 1, &info);
	set_sunergy_param(sun, 0, 0, 255, 0.25, 768, 576, 1);
	set_data(image_file, &data);
	printf("start\n");
	
	ret = sunergy_classifier(sun, data, top, &out);
	printf("%i",ret);
	
	for (int i = 0; i < top; i++)
	{
		printf("%d\t\t", i); 
		printf("%s\t\t", out[i].names);
		printf("%f\n", out[i].confident);					
	}

	show_image_face(image_file, &out);							

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
np.set_printoptions(threshold=np.inf)
#np.set_printoptions(16, suppress=True)


def multi_classifier():
    net1 = sun.init("../../model/classifier/age1.1.names",  # names file
                   "../../model/classifier/age1.1.cfg",  # cfg file
                   "../../model/classifier/age1.1.weights")  # weight file
	
    net = net1[0]['index']
    nms = 0.4  # param of detect
    means = 0  # image reduce means before run in network
    normalization = 255  # image normalization coefficient
    threshold = 0.24  # detect threshold
    video_w = 1920  # image real width used by detect and pose
    video_h = 1080  # image real height used by detect and pose
    resize_format = 0 # 0: darknet 1: opencv 2:caffe

    sun.set_param(net, nms, means, normalization, threshold, video_w, video_h,1)

    # frame = cv2.imread("/home/al/pytorch2sunergy/reid_sunergy/test.jpg")
    face_img = cv2.imread("../../model/classifier/en.jpg")
    #cv2.imshow('img_show', frame)
    #cv2.waitKey(0)
    #print('img', frame)
    face_img = cv2.cvtColor(face_img, cv2.COLOR_BGR2RGB)
    #print('img', face_img)
    #face_img = cv2.resize(face_img, (224, 224))
    #print('img', face_img)
    img = {"width": face_img.shape[1],  # image width
           'height': face_img.shape[0],  # image height
           'step': face_img.shape[2] * face_img.shape[1],  # image step
           'channel': face_img.shape[2],  # image channel
           'batch': 1,  # image batch
           'location': 0,  # location of image data 0:CPU 1:GPU
           'preprocessing': 0,
           # image data run in network directly,0:image with no preprocessing 1: image have preprocessed
           'format': 0,  # format of image data 0:RGB 1:BGR 2: RGP 3:BGRP
           'type': 1,  # type of image data 0: char 1: float
           'data': face_img.data}  # image data

    ret = sun.classifier(net, img)

    for i in range(len(ret)):
        print i, ret[i]['name'], ret[i]['confident']
    #print ("lenret",ret)
    # print np.array(ret)
    #f = open('feature.txt', 'wb')
    #f.write(str(np.array(ret)))
    #f.close()
    face = cv2.cvtColor(face_img, cv2.COLOR_RGB2BGR)
    cv2.imshow("face", face)
    cv2.waitKey(0)
    sun.free(net)


if __name__ == "__main__":   
    multi_classifier()
   


```
