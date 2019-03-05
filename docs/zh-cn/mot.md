## 多目标跟踪

这个例子展示了如何使用Sunergy上训练好的深度神经网络对视频进行多目标跟踪.


## 在**Linux**上使用:
## 在 **Windows**上使用:  &nbsp;

#### 1. 启动MSVS, 打开项目 tracking.sln, 解决方案配置选择 x64 和 Release .

#### 2. 在 main 函数中如下代码处，修改 MOTDIR 为你需要进行多目标跟踪的视频的路径.
```C++
#define MOTDIR "D:/flora/Sunergy-master/Sunergy-master/example/model/tracking/MOT16/test/MOT16-06/"
```
#### 3. 选择项目tracking，右击鼠标选择生成解决方案 .
#### 4. 再次右击鼠标将其设为启动项目，并运行 .
&nbsp;
#### *参考代码:*  

```C++
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
#define MOTDIR "D:/flora/Sunergy-master/Sunergy-master/example/model/tracking/MOT16/test/MOT16-06/"
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
