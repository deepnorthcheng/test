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

####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

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

#### 1. 启动VS2015, 打开项目 Sunergy.sln, 解决方案配置选择 x64 和 Release .

#### 2. 读取图片和模型 .
####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

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

#### 3. 选择项目Sunergy，右击鼠标属性，配置类型选择为静态库(.lib)，之后右击鼠标选择生成解决方案 .
#### 4. 选择项目pose_estimation，右击鼠标选择生成解决方案 .
####  &nbsp;&nbsp;&nbsp;&nbsp; 再次右击鼠标将其设为启动项目，并运行 .

### python程序

#### 1. 启动VS2015，打开Sunergy.sln, 解决方案配置选择 x64 和 Release .
#### 2. 选择项目sunergy，右击鼠标属性，配置类型选择为动态库(.dll)，右击鼠标选择生成解决方案 .
#### 3. 将 lib/windows下的 libsunergy.dll 拷贝至 example/python/pose_estimation 目录下，将其重命名为libsunergy.pyd .
#### 4. 读取图片和模型 .
####  &nbsp;&nbsp;&nbsp;检查以下文件是否在相应位置，名称是否与代码中一致 .

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

void show_poseimage(char* file, detect_out_t* out)
{
	IplImage* src = cvLoadImage(file, 1);
	cvNamedWindow("detect", CV_WINDOW_AUTOSIZE);
	CvPoint lt, rb;
	CvScalar color;
	for (int i = 0; i < out->total_num; i++)
		cvRectangle(src, cvPoint(out->info[i].left*src->width, out->info[i].top*src->height),
		cvPoint(out->info[i].right*src->width, out->info[i].bottom*src->height), cvScalar(255, 0, 0, 0), 3, 4, 0);
	cvShowImage("detect", src);
	cvWaitKey(0);
	cvReleaseImage(&src);
	cvDestroyWindow("detect");


}

//   pose 18
void draw_skeleton(char* file ,pose_out_t* out)
{
IplImage* src = cvLoadImage(file, 1);
cvNamedWindow("pose", CV_WINDOW_AUTOSIZE);
int pairs[] = { 1, 2, 1, 5, 2, 3, 3, 4, 5, 6, 6, 7, 1, 8, 8, 9, 9, 10,
1, 11, 11, 12, 12, 13, 1, 0, 0, 14, 14, 16, 0, 15, 15, 17 };
int colors[] = { 255.0, 0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0,
255.0, 255.0, 0.0, 170.0, 255.0, 0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 0.0,
0.0, 255.0, 85.0, 0.0, 255.0, 170.0, 0.0, 255.0, 255.0, 0.0, 170.0, 255.0,
0.0, 85.0, 255.0, 0.0, 0.0, 255.0, 255.0, 0.0, 170.0, 170.0, 0.0, 255.0,
255.0, 0.0, 255.0, 85.0, 0.0, 255.0 };
int pairs_size = 34;
int number_colors = 54;
int num_keypoints = 18;
for (int person = 0; person < out->human_num; person++){
for (int pair = 0; pair < pairs_size; pair += 2){
int index1 = pairs[pair];
int index2 = pairs[pair + 1];
if (out->pose[person].confident[index1] > 0.05 &&
out->pose[person].confident[index2] > 0.05){
int color_index = pairs[pair + 1] * 3;
CvScalar color = CV_RGB((int)(colors[(color_index + 2) % number_colors]),
(int)(colors[(color_index + 1) % number_colors]),
(int)(colors[(color_index + 0) % number_colors]));
CvPoint point1 = cvPoint((int)(out->pose[person].X[index1] * src->width + 0.5),
(int)(out->pose[person].Y[index1] * src->height + 0.5));
CvPoint point2 = cvPoint((int)(out->pose[person].X[index2] * src->width + 0.5),
(int)(out->pose[person].Y[index2] * src->height + 0.5));
cvLine(src, point1, point2, color, 2,8,0);
}
}
for (int part = 0; part < 18; part++){
int index = part;
int color_index = part * 3;
CvScalar color = CV_RGB(colors[(color_index + 2) % number_colors],
colors[(color_index + 1) % number_colors],
colors[(color_index + 0) % number_colors]);
CvPoint center = cvPoint((int)(out->pose[person].X[index]*src->width + 0.5),
(int)(out->pose[person].Y[index]*src->height + 0.5));
cvCircle(src, center, 3, color, -1,8,0);
}
}
cvShowImage("pose", src);
cvWaitKey(0);
cvReleaseImage(&src);
cvDestroyWindow("pose");
}





int main(int argc, char** argv)
{
	char names[] = "../../model/pose/coco.names";
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
	pose_out_t out;
	net_information_t info;
	sunergy_t sun = sunergy_init(names, cfg_file, weight_file, gpuid, inference);
	sunergy_get_network_infomation(sun, 1, &info);
	set_sunergy_param(sun, 0, 0, 255, 0.25, 768, 576, 1);
	set_data(image_file, &data);
	printf("start\n");

	ret = sunergy_pose(sun, data, &out);

	printf("num = %d\n", out.human_num);
	for (int i = 0; i < out.human_num; i++)
	{
		for (int j = 0; j < 18; j++)
			printf("personidx =%d , idx = %d,x = %f,y=%f\n",i, j, out.pose[i].X[j], out.pose[i].Y[j]);
	}
	draw_skeleton(image_file, &out);
	ret = sunergy_free(sun);
	system("pause");
}


```

#### python
```python
#!/usr/bin/python3
#-*- coding: utf-8 -*-

import libsunergy
import sys, os, time
import colorsys
import math

import cv2
import numpy as np


#try:
#    from shapely.geometry import Polygon
#except Exception as e:
#    print('shapely not found')
#    print('sudo pip3 install shapely')


import warnings
warnings.filterwarnings("ignore")

class Skeleton():
    def __init__(self):
        self.poses = []

    def add_pose(self, pose):
        self.poses.append(pose)

class Pose():

    def __init__(self, keypoints):
        self.keypoints = [keypoints[i:i + 3] for i in range(0, len(keypoints), 3)]
        self.id = -1
        self.seen = True

    def get_flat_keypoints(self):
        return [i for sublist in self.keypoints for i in sublist]

    def distance_squared(self, other):
        d = 0.0
        for i in range(0, len(self.keypoints)):
            # only calculate distance for points with suitable confidence
            if (self.keypoints[i][2] > 0.2 and other.keypoints[i][2] > 0.2):
                d += (self.keypoints[i][0] - other.keypoints[i][0]) ** 2 + \
                     (self.keypoints[i][1] - other.keypoints[i][1]) ** 2
            else:
                d += 1000.0 # otherwise punish a fixed error
        return d

    def is_seen(self):
        return self.seen

class PoseTools():
    def __init__(self, data, cfg, weight, gpu_idx):
        # self.__net, self.names,self._w,self._h = libsunergy.load("./data/pose/coco.data", "/home/hup/project/sunergy_pose_test/model/pose_3.cfg", "/home/hup/project/sunergy_pose_test/model/pose3_58400.weights", 1100, 1080, gpu_idx)
		self.__net, self.names,self._w,self._h = libsunergy.load("../../model/pose/coco.data", 
		"../../model/pose/BODY-POSE25_DET_002.cfg", 
		"../../model/pose/BODY-POSE25_DET_002.weights", 
		1100, 1080, gpu_idx)
        print (self._w)
        print (self._h)
        #self._w = 656   #656 1100 1080
        #self._h = 368
        self._c = 3
        self.skeleton_dim = 75#54
        self.num_keypoints = 25
        self.human_num = 0
        self.point_num = 0
        self.point_data = 0
        self.id_counter = 1
        self.free_flag = True


    def run_skeleton(self, frame):
        cfg_size = (self._w, self._h)
        img = cv2.resize(frame, cfg_size)
        sp = frame.shape
        libsunergy.set_image_w_h(self.__net, sp[1], sp[0])
        self.human_num, self.point_data = libsunergy.predict_expand(img.data, self._w, self._h,self._c, 1, self.__net, 1, "openpose_25", 256.0, 1, 0.5)
        print("point", self.point_data)

    def draw_skeleton(self, color, x, y, centers, frame):
        CocoPairs = [
            #(1, 2), (1, 5), (2, 3), (3, 4), (5, 6), (6, 7), (1, 8), (8, 9), (9, 10), (1, 11),
            #(11, 12), (12, 13), (1, 0), (0, 14), (14, 16), (0, 15), (15, 17), (5, 17), (2, 16)
            (1, 2), (1, 5), (2, 3), (3, 4), (5, 6), (6, 7), (1, 8), (8, 9), (9, 10), (10, 11),(8, 12), 
			(12, 13), (13, 14), (1, 0), (0, 15), (15, 17), (0, 16), (16, 18), (14, 19),(19, 20),(14,21),
			(11, 22),(22, 23),(11, 24),(2,17),(5,18)
        ]  # = 19
        CocoPairsRender = CocoPairs[:-2]
        if x.count(0) < 10:  # filter out the skeleton with less keypoints
            # draw point
            for i in range(self.num_keypoints):
                center = (int(x[i] + 0.5), int(y[i] + 0.5))
                centers[i] = center
                if center == (0, 0):
                    continue
                cv2.circle(frame, center, 3, color, thickness=3, lineType=8, shift=0)

            # draw line
            for pair_order, pair in enumerate(CocoPairsRender):
                if centers[pair[0]] == (0, 0) or centers[pair[1]] == (0, 0):
                    continue
                cv2.line(frame, centers[pair[0]], centers[pair[1]], color, 2)

    def draw_humans(self, frame):

        centers = {}
        for person in range(0, self.human_num):
            single_skeleton = self.point_data[person * self.skeleton_dim: (person + 1) * self.skeleton_dim]
            keys = [item for sublist in zip(single_skeleton[0::3], single_skeleton[1::3]) for item in sublist]
            x = keys[0::2]
            y = keys[1::2]
            color = (0, 255, 0)
            self.draw_skeleton(color, x, y, centers, frame)

    def test_openpose(self, video_path):
        vc = cv2.VideoCapture(video_path)
        frame_idx = 0
        max_frame = int(vc.get(cv2.CAP_PROP_FRAME_COUNT))
        interval = 1
        if not vc.isOpened():
            print('cannot find file or cannot open the camera!\n')
            # continue
        else:
            for ii in range(max_frame):
                vflag, frame = vc.read()
				
                if vflag and frame_idx % interval == 0:
                    self.run_skeleton(frame)
                    self.draw_humans(frame)
                    cv2.imshow('pose', frame)

                    key = cv2.waitKey(1)
                    if key & 255 == 27:  # ESC
                        break

                frame_idx += 1
				
			cv2.imshow("pose",frame)
			cv2.waitKey(0) 
			libsunergy.free_pose(self.__net)

    def __del__(self):
        if self.free_flag:
			
            #libsunergy.free_pose(self.__net)
            self.free_flag = False
        pass

    def free(self):
        self.__del__()

if __name__ == '__main__':

    PoseEvent = PoseTools("./data/pose/coco.data", "/home/hup/project/sunergy_pose_test/model/pose_3.cfg", \
                        "/home/hup/project/sunergy_pose_test/model/pose3_58400.weights", gpu_idx=0)
    video_path = '../../model/pose/person.jpg'
    PoseEvent.test_openpose(video_path)





```