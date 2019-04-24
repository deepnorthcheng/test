## 多目标跟踪

这个例子展示了如何在Sunergy上使用深度学习模型对视频进行多目标跟踪.

-------------

## 在**Linux**上使用:

#### 1. 进入Sunergy目录并编译
```python
cd Sunergy
make
```

#### 2. 将 libsunergy.so 复制到多目标跟踪文件夹

```pyhton
cp -i lib/linux/libsunergy.so example/python/multi-object_tracking
```

#### 3. 进入多目标追踪程文件夹

```python
cd example/python/multi-object_tracking
```

#### 4. 配置视频图片
```python
MOTDIR ="../../model/tracking/MOT16/test/MOT16-06/det/"
image_file = "../../model/tracking/MOT16/test/MOT16-06/img1/"
```

#### 5. 运行程序

```python
python multi-object_tracking.py
```

---------------

## 在 **Windows**上使用: 

### C程序 

#### 1. 启动VS2015, 打开项目 example/c/tracking/tracking.sln, 解决方案配置选择 x64 和 Release .

#### 2. 配置视频图片 .

```C++
#define MOTDIR "../../../model/tracking/MOT16/test/MOT16-06/"
```
#### 3. 选择项目tracking，右击鼠标选择生成解决方案 .
#### 4. 再次右击鼠标将其设为启动项目，并运行 .


---------------

&nbsp;
#### *参考代码:*  

#### C

```C
#include "stdlib.h"
#include "string.h"
#include <opencv2/core.hpp> 
#include <opencv2/highgui.hpp>
#include <iostream>
#include <vector>
#include <cstdlib>
#include <map>
#include "list"
extern "C"
{
	#include "VideoTracker.h"
	#include "model.h"
	#include "tracker.h" 
	extern Track tracks[1024];
};
//set MOTDIR as your video address
#define MOTDIR "../../../model/tracking/MOT16/test/MOT16-06/"
#define RUNGT
using namespace std;

int main()
{
	int ret = 0;
	int max_track_num = 50;
	int nn_budget = 100;
	float max_feat_distance = 0.2;
	float dete_threshold = 0.3;
	float nms_max_overlap = 1.0;
	float max_distance = 0.7;
	int metric = 2;
	int max_age = 30;
	int n_init = 3;
	float kalman_weight_velocity = 1. / 160;
	int min_frame_idx = 1;
	int max_frame_idx = 1194;
	char sstr[20];
	char ssd[20];
	char image_file[100];
	std::vector<int> del_object_id;
	
	CvFont font;
	cvInitFont(&font, CV_FONT_HERSHEY_PLAIN, 0.5, 1.0, 0);
	ModelDetection* M = NULL;
	track_total_t* handle = Tracking_Init(nn_budget, max_feat_distance, dete_threshold,
		nms_max_overlap, max_distance, metric, max_age, n_init, kalman_weight_velocity);

	M = getInstance();

	if (loadDataFromFile_(M, MOTDIR, true) == false) {
		return false;
	}

	for (int k = min_frame_idx; k <= max_frame_idx; k++) {
		sprintf(image_file, "%simg1/%06d.jpg", MOTDIR, k);
		IplImage* src = cvLoadImage(image_file, 1);
		cvNamedWindow("tracking", CV_WINDOW_AUTOSIZE);

		DETECTIONS detections;
		if (getFrameDetections_(M, k, detections) == 0) {
			return 0;
		}
		int num = int(detections.size());
		if (num>0) {
			track_input_t* input_c = (track_input_t*)malloc(sizeof(track_input_t)*num);
			for (int i = 0; i < num; i++)
			{
				input_c[i].dete_confidence = 1;
				input_c[i].dete_index = i;
				memcpy(input_c->TimeStamp, "00:00:00", 50);
				input_c->FrameIdx = k;
				input_c[i].dete_tlwh.up_left_x = detections[i].tlwh.data[0];
				input_c[i].dete_tlwh.up_left_y = detections[i].tlwh.data[1];
				input_c[i].dete_tlwh.box_w = detections[i].tlwh.data[2];
				input_c[i].dete_tlwh.box_h = detections[i].tlwh.data[3];				
				for (int j = 0; j < 128; j++)
				{
					input_c[i].feature.data[j] = 0;
				}
				cvRectangle(src, cvPoint(detections[i].tlwh.data[0], detections[i].tlwh.data[1]),
					cvPoint(detections[i].tlwh.data[0] + detections[i].tlwh.data[2], detections[i].tlwh.data[1] + detections[i].tlwh.data[3]),
					cvScalar(255, 0, 0, 0), 1, 4, 0);
				sprintf(ssd, "%d", input_c[i].dete_index);
				cvPutText(src, ssd, cvPoint(detections[i].tlwh.data[0], detections[i].tlwh.data[1]), &font, CV_RGB(0, 255, 0));
			}
			input_c->dete_size = num;

			ret = Track_Process(input_c, handle);
			tracking_out_t* out = &handle->trackout->obj->Tracking;

			for (int i=0;i<out->track_size;i++)
			{
				if ((out->track_type[i] != 2) || (out->track_update[i] > 0)) {						
					continue;				
				}		
				cvRectangle(src, cvPoint((out->track_box)[i].up_left_x, (out->track_box)[i].up_left_y), 
			    cvPoint((out->track_box)[i].up_left_x+(out->track_box)[i].box_w, (out->track_box)[i].up_left_y + (out->track_box)[i].box_h),
					cvScalar(0, 255, 0, 0), 1, 4, 0);	
				sprintf(sstr, "%d", out->track_id[i]);		
				cvPutText(src, sstr, cvPoint((out->track_box)[i].up_left_x, (out->track_box)[i].up_left_y), &font, CV_RGB(0,255, 0));	
			}
			cvShowImage("tracking", src);
			cvWaitKey(300);
			cvReleaseImage(&src);
			free(input_c);
		}
	}
	ret = Tracking_Free(handle);
	system("pause");
	return 0;
}

```


#### python

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import libsunergy as libtrack
import numpy as np
import cv2
def deepsort():
    MOTDIR ="../../model/tracking/MOT16/test/MOT16-06/det/"

    min_frame_idx = 1;
    max_frame_idx = 1194;

    init_para = []
    list1 = init_para
    dic1 = dict()
    dic1.setdefault("nn_budget",100)
    dic1.setdefault("max_feat_distance", 0.2)
    dic1.setdefault("dete_threshold", 0.3)
    dic1.setdefault("nms_max_overlap", 1.0)
    dic1.setdefault("max_distance", 0.7)
    dic1.setdefault("metric", 2)
    dic1.setdefault("max_age", 30)
    dic1.setdefault("n_init", 3)
    dic1.setdefault("kalman_weight_velocity", 1./160)
    list1.append(dic1)
    track_handle = libtrack.track_init(init_para)

    with open(MOTDIR+'det.txt','r') as f:
        lines = f.readlines()
    track_data = [[]]
    frame_id = 1
    objid=0
    k  = 0
    for line in lines:
        line = line.split(',')

        if int(line[0]) > frame_id:
            objid=0
        track_data.append([])
        list_ = track_data[int(line[0])]
        dic = dict()
        dic.setdefault("leftx",float(line[2]))
        dic.setdefault("width", float(line[4]))
        dic.setdefault("lefty", float(line[3]))
        dic.setdefault("height", float(line[5]))
        dic.setdefault("confident", float(line[6]))
        dic.setdefault("objindex", objid)
        dic.setdefault("frameidx", float(line[0]))
        dic.setdefault("timestamp", "00:00:00")
        dic.setdefault("feature", np.zeros(128, np.float32))
        for j in range(0,128):
            dic['feature'][j] = float(line[6])
        objid+=1
        #line_num=1
        list_.append(dic)
        k += 1

    for i in range(1,max_frame_idx):

        input = track_data[i]
        
        output = libtrack.track_run(track_handle,input)

        image_file = "../../model/tracking/MOT16/test/MOT16-06/img1/%06d.jpg" % i
        image = cv2.imread(image_file)
        print(output)
        for key0 in output.keys():
            for key1 in output[key0].keys():
                if (int(output[key0][key1]['state'])) != 2 or (int(output[key0][key1]['update_times'])) > 0:
                    continue               
                #cv2.rectangle(image, int(output[key0][key1]['track_box'][0]), int(output[key0][key1]['track_box'][1]), int output[key0][key1]['track_box'][0] + int(output[key0][key1]['track_box'][2]), int(output[key0][key1]['track_box'][1]) + int(output[key0][key1]['track_box'[3]], (255,255,0), 2)
                cv2.rectangle(image, (int(output[key0][key1]['track_box'][0]), int(output[key0][key1]['track_box'][1])), (int(output[key0][key1]['track_box'][0])+int(output[key0][key1]['track_box'][2]), int(output[key0][key1]['track_box'][1])+int(output[key0][key1]['track_box'][3])), (255,255,0), 2)
                a = str(key0)
                cv2.putText(image, a, (int(output[key0][key1]['track_box'][0]), int(output[key0][key1]['track_box'][1])), 0, 0.8, (255, 255, 0), 2)
                #cv2.rectangle(image, (int(output[key0][key1]['det_box'][0]), int(output[key0][key1]['det_box'][1])), (int(output[key0][key1]['det_box'][0])+int(output[key0][key1]['det_box'][2]), int(output[key0][key1]['det_box'][1])+int(output[key0][key1]['det_box'][3])), (0,0,255), 2)

           # a = str(track['DeteID'])
                #cv2.putText(image, a, ((int(track['X']), int(track['Y']))), 0, 0.8, (0, 0, 255), 2)
        size = len(input)
        print(size)
        for det in range(size):            
            cv2.rectangle(image, (int(input[det]['leftx']), int(input[det]['lefty'])), (int(input[det]['leftx'])+int(input[det]['width']), \
            int(input[det]['lefty'])+int(input[det]['height'])), (0,0,255), 2)
        cv2.imshow("tracking",image)
        cv2.waitKey(100)
            #print output
            #print(output)
    libtrack.track_uninit(track_handle)





if __name__ == "__main__":
    #openpose()
   # detect()
    #extract()
    deepsort()
	
```
