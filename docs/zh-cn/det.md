## 目标检测

这个例子展示了如何使用Sunergy上训练好的深度神经网络进行目标检测.

------

## 在**Linux**上使用:

#### 1. 在主目录打开终端，输入make后，将 lib/linux 中的 libsunergy.so文件拷贝至目标检测程序所在文件夹

```pyhton
cp -i lib/linux/libsunergy.so example/python/object_detection
```

#### 2. 进入目标检测程序所在文件夹

```python
cd example/python/object_detection
```

#### 3. 将你需要进行目标检测的图片重命名并放入路径： "../../model/detect/dog.jpg".

```python
char image_file[] = "../../model/detect/dog.jpg";
```

####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

```python
char names[] = "../../model/detect/coco.names";
char cfg_file[] = "../../model/detect/yolov3.cfg";
char weight_file[] = "../../model/detect/yolov3.weights";
char image_file[] = "../../model/detect/dog.jpg";
```
&nbsp;&nbsp; **coco.names** 文件是所要预测属性的值，如：bed,diningtable,toilet,tvmonitor,laptop,mouse,remote,eyboard 等。  
&nbsp;&nbsp; **yolov3.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **yolov3.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **dog.jpg** 文件是您需要目标检测的图片

#### 4. 运行程序

```python
python object_detection.py
```

-------

## 在 **Windows**上使用:  

### C程序

#### 1. 启动MSVS, 打开项目 Sunergy.sln, 解决方案配置选择 x64 和 Release .

#### 2. 将你需要进行目标检测的图片重命名并放入路径： "../../model/detect/dog.jpg".

```C++
char image_file[] = "../../model/detect/dog.jpg";
```
####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

```C++
char names[] = "../../model/detect/coco.names";
char cfg_file[] = "../../model/detect/yolov3.cfg";
char weight_file[] = "../../model/detect/yolov3.weights";
char image_file[] = "../../model/detect/dog.jpg";
```

&nbsp;&nbsp; **coco.names** 文件是所要预测属性的值，如：bed,diningtable,toilet,tvmonitor,laptop,mouse,remote,eyboard 等。  
&nbsp;&nbsp; **yolov3.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **yolov3.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **dog.jpg** 文件是您需要目标检测的图片

#### 3. 选择项目sunergy，右击鼠标属性，配置类型选择为静态库(.lib)，之后右击鼠标选择生成解决方案 .
#### 4. 选择项目object_detection，右击鼠标选择生成解决方案 .
####  &nbsp;&nbsp;&nbsp;&nbsp; 再次右击鼠标将其设为启动项目，并运行.

### python程序

#### 1. 启动MSVS，打开Sunergy.sln，解决方案配置选择 x64 和 Release .
#### 2. 选择项目sunergy，右击鼠标属性，配置类型选择为动态库(.dll)，右击鼠标选择生成解决方案 .
#### 3. 将 lib/windows下的libsunergy.dll 拷贝至 example/python/object_detection 目录下，将其后缀名 .dll 改为 .pyd .
#### 4. 检查 object_detection.py 程序中的文件路径是否正确 .

```python
"../../model/detect/coco.names"
"../../model/detect/yolov3.cfg"
"../../model/detect/yolov3.weights"
"../../model/detect/dog.jpg"
```

&nbsp;&nbsp; **coco.names** 文件是所要预测属性的值，如：bed,diningtable,toilet,tvmonitor,laptop,mouse,remote,eyboard 等。  
&nbsp;&nbsp; **yolov3.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **yolov3.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **dog.jpg** 文件是您需要目标检测的图片

#### 5. 由命令行进入目录 example/python/object_detection，执行 python object_detection.py 运行 .


--------

&nbsp;
#### *参考代码:* 
 
#### C++
```C++
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
	data->step = src->width*3;
	data->batch = 1;
	data->channel = 3;
	data->data_location = CPU_DATA;
	data->format = BGR;
	data->preprocessing = false;
	data->type = DATA_UCHAR;
	data->data = src->imageData;
}

	 
void show_image_dete(char* file, detect_out_t* out)
{
	IplImage* src = cvLoadImage(file, 1);
	cvNamedWindow("detect", CV_WINDOW_AUTOSIZE);
	CvPoint lt,rb;
	CvScalar color;
	for (int i = 0; i < out->total_num;i++)
		cvRectangle(src, cvPoint(out->info[i].left, out->info[i].top),
		cvPoint(out->info[i].right, out->info[i].bottom), cvScalar(255, 0, 0, 0), 3, 4, 0);
	cvShowImage("detect", src);
	cvWaitKey(0);
	cvReleaseImage(&src);
	cvDestroyWindow("detect");	
}


int main(int argc, char** argv)
{
	char names[] = "../../model/detect/coco.names";
	char cfg_file[] = "../../model/detect/yolov3.cfg";
	char weight_file[] = "../../model/detect/yolov3.weights";
	char image_file[] = "../../model/detect/dog.jpg";
	FILE* fp0,*fp1,*fp2,*fp3;
	fp0 = fopen(names, "r");
	fp1 = fopen(cfg_file, "r");
	fp2 = fopen(weight_file, "r");
	fp3 = fopen(image_file, "r");
	if (fp0 == NULL||fp1==NULL||fp2==NULL||fp3==NULL)
	{
		printf("please run modle.sh\n");
		exit(0);
	}
	int gpuid = 0;				
	bool inference = true;
	int ret = 0;
	sunergy_data_t data;
	detect_out_t out;
	net_information_t info;
	sunergy_t sun = sunergy_init(names, cfg_file, weight_file, gpuid, inference);
	sunergy_get_network_infomation(sun, 1, &info);
	set_sunergy_param(sun, 0, 0, 255, 0.25, 768, 576, 1);
	set_data(image_file, &data);
	printf("start\n");

	ret = sunergy_detect(sun, data, &out);
	printf("%d",ret);
	for (int i = 0; i < out.total_num; i++)
		printf("%d,%s %f,[%f,%f,%f,%f]\n", i, out.info[i].name, out.info[i].confident, out.info[i].left, out.info[i].right,
		out.info[i].top, out.info[i].bottom);
	show_image_dete(image_file,&out);
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

def draw_box(image,result):
    width = image.shape[1]
    height = image.shape[0]

    for obj in result:
        conf = obj['confident']
        if conf > 0:
            point1 = (int(obj['left']), int(obj['top']))
            point2 = (int(obj['right']), int(obj['bottom']))
            print point1, point2
            cv2.rectangle(image, point1, point2, (255, 0, 0), 2)


def detect():
    net1 = sun.init("../../model/detect/coco.names",#names file
             "../../model/detect/yolov3.cfg",#cfg file
             "../../model/detect/yolov3.weights")#weight file
    net = net1[0]['index']
    nms=0.4 #param of detect
    means=0 # image reduce means before run in network
    normalization=255 #image normalization coefficient
    threshold=0.24#detect threshold
    video_w= 768#image real width used by detect and pose
    video_h=576#image real height used by detect and pose
    resize_format = 0 # 0: darknet 1: opencv 2:caffe
    depth = sun.get_depth(net)
    print depth
    net_info = sun.get_net_info(net,1)
    print net_info
    sun.set_param(net,nms,means,normalization,threshold,video_w,video_h, 1)

    frame1 = cv2.imread('../../model/detect/dog.jpg')
    cfg_size = (864, 864)
    frame = cv2.resize(frame1, cfg_size)
    #print frame
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
    
    t0=time.time()
    ret = sun.detect(net,img)
    t1 = time.time()
    print ret
    print str(t1-t0)
    draw_box(frame1, ret)
    cv2.imshow("detection",frame1)
    cv2.waitKey(0) 
    sun.free(net)


if __name__ == "__main__":   
   detect()


```
