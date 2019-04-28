## 姿态估计

这个例子展示了如何在Sunergy上使用深度学习模型进行人体的姿态估计。

-------------

## 在**Linux**上使用:

#### 1. 进入Sunergy目录并编译
```python
cd Sunergy
make
```

#### 2. 将 libsunergy.so 复制到姿态估计文件夹

```pyhton
cp -i lib/linux/libsunergy.so example/python/pose_estimation
```

#### 3. 进入姿态估计程序所在文件夹

```python
cd example/python/pose_estimation
```

#### 4. 读取图片和模型

检查以下文件是否在相应位置，名称是否与代码中一致 .

```python
"../../model/pose/coco.data";
"../../model/pose/BODY-POSE25_DET_002.cfg";
"../../model/pose/BODY-POSE25_DET_002.weights";
"../../model/pose/person.jpg";
```

&nbsp;&nbsp; **coco.data** 文件是COCO数据集信息，并可索引至coco.names  
&nbsp;&nbsp; **BODY-POSE25_DET_002.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **BODY-POSE25_DET_002.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **person.jpg** 文件是您要姿态估计的图片

#### 5. 运行程序

```python
python pose_estimation.py
```


------------

## 在 **Windows**上使用: 

### C程序

#### 1. 打开项目
启动VS2015, 打开项目 Sunergy.sln, 解决方案配置选择 x64 和 Release .

#### 2. 读取图片和模型 
检查以下文件是否在相应位置，名称是否与代码中一致 .

```C
char names[] = "../../model/pose/coco.names";
char cfg_file[] = "../../model/pose/BODY-POSE25_DET_002.cfg";
char weight_file[] = "../../model/pose/BODY-POSE25_DET_002.weights";
char image_file[] = "../../model/pose/person.jpg";
```

&nbsp;&nbsp; **coco.names** 文件是所要预测属性的值  
&nbsp;&nbsp; **BODY-POSE25_DET_002.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **BODY-POSE25_DET_002.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **person.jpg** 文件是您要姿态估计的图片

#### 3. 生成Sunergy
选择项目**sunergy**，右击鼠标属性，配置类型选择为静态库(.lib).   
之后右击鼠标选择生成解决方案 .
#### 4. 生成项目并运行
选择项目**pose_estimation**，右击鼠标选择生成解决方案 .  
再次右击鼠标将其设为启动项目，并运行 .

### python程序

#### 1. 打开项目
启动VS2015，打开Sunergy.sln, 解决方案配置选择 x64 和 Release .
#### 2. 生成Sunergy
选择项目**sunergy**，右击鼠标属性，配置类型选择为动态库(.dll).   
之后右击鼠标选择生成解决方案 .
#### 3. 将 libsunergy.dll 复制到姿态估计文件夹
将 *lib/windows* 下的 **libsunergy.dll** 拷贝至 *example/python/pose_estimation* 目录下.   
将其重命名为 **libsunergy.pyd**.
#### 4. 读取图片和模型 
检查以下文件是否在相应位置，名称是否与代码中一致 .

```python
"../../model/pose/coco.data";
"../../model/pose/BODY-POSE25_DET_002.cfg";
"../../model/pose/BODY-POSE25_DET_002.weights";
"../../model/pose/person.jpg";
```

&nbsp;&nbsp; **coco.data** 文件是COCO数据集信息，并可索引至coco.names  
&nbsp;&nbsp; **BODY-POSE25_DET_002.cfg** 文件是所采用的深度神经网络的结构  
&nbsp;&nbsp; **BODY-POSE25_DET_002.weights** 文件是已训练好的权重  
&nbsp;&nbsp; **person.jpg** 文件是您要姿态估计的图片

#### 5. 打开命令行，运行程序 .
```python
cd example/python/pose_estimation
python pose_estimation.py
```


------------

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


void draw_skeleton(char* file, pose_detect_t* out)
{
	IplImage* img = cvLoadImage(file, 1);
	cvNamedWindow("pose", CV_WINDOW_AUTOSIZE);

	const int pairs18[] = { 1, 2, 1, 5, 2, 3, 3, 4, 5, 6, 6, 7, 1, 8, 8, 9, 9, 10,
		1, 11, 11, 12, 12, 13, 1, 0, 0, 14, 14, 16, 0, 15, 15, 17 };

	const int pairs25[] = { 1,2, 1,5,  2,3,  3,4,  5,6,  6,7, 1,8, 8,9, 9,10, 10,
		11,8,12,12,13,13,14, 1,0, 0,15, 15,17, 0,16, 16,18, 14,19,19,20,14,21, 11,22,22,23,11,24 };//2,17,	5,18,

	int colors[] = { 255.0, 0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0,
		255.0, 255.0, 0.0, 170.0, 255.0, 0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 0.0, 255.0, 0.0, 0.0,
		0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0, 255.0, 255.0, 0.0, 170.0, 255.0,
		0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 255.0, 0.0, 170.0, 170.0, 0.0, 255.0,
		255.0, 0.0, 255.0, 85.0, 0.0, 255.0, 0.0, 0.0, 255.0, 0.0, 0.0, 255.0, 0.0, 0.0, 255.0,
		0.0, 255.0, 255.0, 0.0, 255.0, 255.0, 0.0, 255.0, 255.0 };


	int pairs_size = 34;
	int number_colors = 54;
	int num_keypoints = out->poseNum;
	const int* pairs;
	if (25 == out->poseNum)
	{
		pairs = pairs25;
		pairs_size = 48;
	}
	if (18 == out->poseNum)
	{
		pairs = pairs18;
	}

	for (int person = 0; person < out->human_num; person++) {
		for (int pair = 0; pair < pairs_size; pair += 2) {
			int index1 = pairs[pair];
			int index2 = pairs[pair + 1];
			if (out->pose[person].confident[index1] > 0.05 &&
				out->pose[person].confident[index2] > 0.05) {
				int color_index = pairs[pair + 1] * 3;
				CvScalar color = CV_RGB((int)(colors[(color_index + 2) % number_colors]),
					(int)(colors[(color_index + 1) % number_colors]),
					(int)(colors[(color_index + 0) % number_colors]));
				CvPoint point1 = cvPoint((int)(out->pose[person].X[index1] + 0.5), (int)(out->pose[person].Y[index1] + 0.5));
				CvPoint point2 = cvPoint((int)(out->pose[person].X[index2] + 0.5), (int)(out->pose[person].Y[index2] + 0.5));
				cvLine(img, point1, point2, color, 2, 8, 0);
			}
		}
		for (int part = 0; part < num_keypoints; part++) {
			int index = part;
			int color_index = part * 3;
			CvScalar color = CV_RGB(colors[(color_index + 2) % number_colors],
				colors[(color_index + 1) % number_colors],
				colors[(color_index + 0) % number_colors]);
			CvPoint center = cvPoint((int)(out->pose[person].X[index] + 0.5), (int)(out->pose[person].Y[index] + 0.5));
			cvCircle(img, center, 3, color, -1, 8, 0);
		}
	}

	cvShowImage("pose", img);
	cvWaitKey(0);
	cvReleaseImage(&img);
	cvDestroyWindow("pose");
}

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



int main(int argc, char** argv)
{
	char names[] = "../../model/pose/coco.data";
	char cfg_file[] = "../../model/pose/BODY-POSE25_DET_002.cfg";
	char weight_file[] = "../../model/pose/BODY-POSE25_DET_002.weights";
	char image_file[] = "../../model/pose/person.jpg";
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
	pose_detect_t out;
	out.poseNum = 25;
	net_information_t info;

	float nms = 0.4;
	float means = 0;
	float normalization = 255;
	float threshold = 0.24;
	int width = 640;
	int height = 424;

	sunergy_t sun = sunergy_init(names, cfg_file, weight_file, gpuid, inference);
	sunergy_get_network_infomation(sun, 1, &info);
	set_sunergy_param(sun, nms, means, normalization, threshold, width, height, 1);
	set_data(image_file, &data);

	printf("start\n");

	ret = sunergy_pose(sun, data, &out);
	sunergy_pose2detect(sun, &out, UPPER_BODY_HEAD);

	printf("total num = %d\n", out.human_num);
	for (int i = 0; i < out.human_num; i++)
	{
		printf("%d,%s %f,[%d,%d,%d,%d]\n", i, out.info[i].name, out.info[i].confident,
					out.info[i].left, out.info[i].right,
					out.info[i].top, out.info[i].bottom);
		for (int j = 0; j < 25; j++)
			printf("personidx =%d , idx = %d,x = %f,y=%f\n", i, j, out.pose[i].X[j], out.pose[i].Y[j]);
	}
		
	draw_skeleton(image_file, &out);
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
    for obj in result:
        conf = obj['confident']
        if conf > 0:
            cv2.rectangle(image, (obj['left'], obj['top']), (obj['right'], obj['bottom']), (255, 0, 0), 2)
"""
    pairs = [1, 2, 1, 5, 2, 3, 3, 4, 5, 6, 6, 7, 1, 8, 8, 9, 9, 10,
             1, 11, 11, 12, 12, 13, 1, 0, 0, 14, 14, 16, 0, 15, 15, 17]
    colors = [255.0, 0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0,
              255.0, 255.0, 0.0, 170.0, 255.0, 0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 0.0,
              0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0, 255.0, 255.0, 0.0, 170.0, 255.0,
              0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 255.0, 0.0, 170.0, 170.0, 0.0, 255.0,
              255.0, 0.0, 255.0, 85.0, 0.0, 255.0]
"""
def draw_skeleton(image,result,poseNum):
    pairs = 0
    colors = [255.0, 0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0,255.0, 255.0, 0.0, 170.0, 255.0, 0.0, 85.0, 255.0, 0.0, 0.0,
			  255.0, 0.0, 255.0, 0.0, 0.0,0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0, 255.0, 255.0, 0.0, 170.0, 255.0,0.0, 85.0, 255.0, 0.0, 0.0,
			  255.0, 255.0, 0.0, 170.0, 170.0, 0.0, 255.0,255.0, 0.0, 255.0, 85.0, 0.0, 255.0, 0.0, 0.0, 255.0, 0.0, 0.0, 255.0, 0.0, 0.0, 255.0,
			  0.0, 255.0, 255.0, 0.0, 255.0, 255.0, 0.0, 255.0, 255.0]
    if(poseNum == 25):
        pairs = [1,2, 1,5, 2,3, 3,4, 5,6, 6,7, 1,8, 8,9, 9,10, 10,11,8,12,12,13,13,14, 1,0, 0,15, 15,17, 0,16, 16,18, 14,19,19,20,14,21, 11,22,22,23,11, 24]
    elif(poseNum == 18):
        pairs = [1, 2, 1, 5, 2, 3, 3, 4, 5, 6, 6, 7, 1, 8, 8, 9, 9, 10, 1, 11, 11, 12, 12, 13, 1, 0, 0, 14, 14, 16, 0, 15, 15, 17]
    else:
        print('Pose point number error!\n')
    pairs_size = len(pairs)
    number_colors = len(colors)

    for person in range(0, len(result)):
        for pair in range(0, pairs_size, 2):
            index1 = pairs[pair];
            index2 = pairs[pair + 1];

            if (result[person]['confident'][index1] > 0.05 and result[person]['confident'][index2] > 0.05):
                color_index = pairs[pair + 1] * 3
                color = (int(colors[(color_index + 2) % number_colors]),
                         int(colors[(color_index + 1) % number_colors]),
                         int(colors[(color_index + 0) % number_colors]))
                point1 = (int(result[person]['X'][index1] + 0.5), int(result[person]['Y'][index1] + 0.5))
                point2 = (int(result[person]['X'][index2] + 0.5), int(result[person]['Y'][index2] + 0.5))
                # cv2.line(frame, point1, (1171,655), (0,85,255), 2)

                #bounding_w = result[person]['right'] - result[person]['left']
                #bounding_h = result[person]['bottom'] - result[person]['top']
                point_l_t = (result[person]['left']-8,result[person]['top']-35)
                point_r_t = (result[person]['right']+8,result[person]['top']-35)
                point_l_b = (result[person]['left']-8,result[person]['bottom']+10)
                point_r_b = (result[person]['right']+8,result[person]['bottom']+10)
                cv2.line(image, point_l_t, point_r_t, (0,85,255), 2)
                cv2.line(image, point_l_t, point_l_b, (0,85,255), 2)
                cv2.line(image, point_r_t, point_r_b, (0,85,255), 2)
                cv2.line(image, point_l_b, point_r_b, (0,85,255), 2)
                cv2.line(image, point1, point2, color, 2)

        for part in range(0, poseNum):
            index = part
            color_index = part * 3
            color = (colors[(color_index + 2) % number_colors],
                     colors[(color_index + 1) % number_colors],
                     colors[(color_index + 0) % number_colors])
            center = (int(result[person]['X'][index] + 0.5), int(result[person]['Y'][index] + 0.5))
            cv2.circle(image, center, 3, color, -1)


def openpose():
    net1 = sun.init("../../model/pose/coco.data",#names file
                   "../../model/pose/BODY-POSE25_DET_002.cfg",#cfg file
                   "../../model/pose/BODY-POSE25_DET_002.weights")#weight file
    #net1 = sun.init("./data/pose/coco.names",#names file
    #               "/home/hup/project/pytestMnist/body_25/pose_3.cfg",#cfg file
    #               "/home/hup/project/pytestMnist/body_25/pose3_58400.weights")#weight file
    net = net1[0]['index']
    nms=0.4 #param of detect
    means=0 # image reduce means before run in network
    normalization=255 #image normalization coefficient
    threshold=0.24#detect threshold
    video_w= 640#image real width used by detect and pose1100
    video_h= 424#image real height used by detect and pose1080
    resize_format = 1 # 0: darknet 1: opencv 2:caffe
    sun.set_param(net, nms, means, normalization, threshold, video_w, video_h, resize_format)
    vc = cv2.VideoCapture('../../model/pose/person.jpg')
    frame_idx = 0
    interval = 1
    max_frame = int(vc.get(cv2.CAP_PROP_FRAME_COUNT))
    for ii in range(max_frame):
        vflag, frame = vc.read()
        

        #cv2.imshow("1",frame)
        #frame = cv2.resize(frame, (656, 368))
        #frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        img={'width':frame.shape[1],
        'height':frame.shape[0],    #image height
        'step':frame.shape[2]*frame.shape[1],#image step
        'channel':frame.shape[2],#image channel
        'batch':1,#image batch
        'location':0,#location of image data 0:CPU 1:GPU
        'preprocessing':0,#image data run in network directly,0:image with no preprocessing 1: image have preprocessed
        'format':1,#format of image data 0:RGB 1:BGR 2: RGP 3:BGRP
        'type':1,#type of image data 0: char 1: float
        'data':frame.data}#image data

        pose2detect_label = 1
        draw_box
        out = sun.pose(net, img, 25)
        print out
        draw_skeleton(frame,out,25)
        cv2.imshow("pose",frame)
            
        if cv2.waitKey(1) & 255 == 27:  # ESC
            break
	

	cv2.waitKey(0)

    #print out
    	#det = sun.pose2detect(net,out,3)
    	#feat = sun.getfeat(net,50)
    	#print feat
    	#draw_box(frame, det)
    	#print det
    #print out
	
        #for i in out:
        #    draw_skeleton(frame,i)
        #    cv2.imshow("1",frame)
        #    cv2.waitKey(1)

    sun.free(net)

if __name__ == "__main__":
   openpose()
  


```