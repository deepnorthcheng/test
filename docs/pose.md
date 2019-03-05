## Pose estimation


This example shows how to use the deep neural network models which trained on Sunergy to do pose estimation.
## How to use on **Linux**:
## How to use on **Windows**:

#### 1. Start MSVS, open Sunergy.sln, set x64 and Release.

#### 2. Rename the image you want to predict and put it in this path："../../model/pose/person.jpg".
```C++
char image_file[] = "../../model/pose/person.jpg";
```
#### &nbsp;&nbsp;&nbsp; Check if the following three files' locations and names are consistent with  the following code.


```C++
char cfg_file[] = "../../model/pose/pose.cfg";
char weight_file[] = "../../model/pose/pose.weights";
char image_file[] = "../../model/pose/person.jpg";
``` 
&nbsp;&nbsp;**pose.cfg** --- The structure of the deep neural network.  
&nbsp;&nbsp;**pose.weights** --- Trained weight.  
&nbsp;&nbsp;**person.jpg** --- The image you want to estimation.

#### 3. Do the: Build -> Build Sunergy.
#### 4. Do the: Build -> Build pose_estimation. 
####  &nbsp;&nbsp;&nbsp;&nbsp;Set *pose_estimation* as the startup project and run it.
&nbsp;
#### *Code:*

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
	char cfg_file[] = "../../model/pose/pose.cfg";
	char weight_file[] = "../../model/pose/pose.weights";
	char image_file[] = "../../model/pose/new.png";
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
	set_sunergy_param(sun, 0, 0, 255, 0.25);
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
