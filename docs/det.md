## Object detection

This example shows how to use the deep neural network models which trained on Sunergy to do object detection.
## How to use on **Linux**:
## How to use on **Windows**:

#### 1. Start MSVS, open Sunergy.sln, set x64 and Release.

#### 2. Rename the image you want to do object detection and put it in this path："../../model/detect/dog.jpg"
```C++
        char image_file[] = "../../model/detect/dog.jpg";
```
#### &nbsp;&nbsp;&nbsp; Check if the following four files' locations and names are consistent with  the following code.


```C++
	char names[] = "../../model/detect/coco.names";
	char cfg_file[] = "../../model/detect/yolov3.cfg";
	char weight_file[] = "../../model/detect/yolov3.weights";
	char image_file[] = "../../model/detect/dog.jpg";
```
&nbsp;&nbsp;**age1.1.names** ---The name of object detection's result can be, such as: dog, car...
&nbsp;&nbsp;**age1.1.cfg** ---The structure of the deep neural network.  
&nbsp;&nbsp;**age1.1.weights** ---Trained weight.  
&nbsp;&nbsp; **en.jpg** --- The image you want to do object detection

#### 3. Do the: Build -> Build Sunergy.
#### 4. Do the: Build -> Build object_detection. 
####  &nbsp;&nbsp;&nbsp;&nbsp;Set *object_detection* as the startup project and run it.
&nbsp;
#### Code:

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
		cvRectangle(src, cvPoint(out->info[i].left*src->width, out->info[i].top*src->height),
		cvPoint(out->info[i].right*src->width, out->info[i].bottom*src->height), cvScalar(255, 0, 0, 0), 3, 4, 0);
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
	set_sunergy_param(sun, 0, 0, 255, 0.25);
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
