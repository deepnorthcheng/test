## Face attribute prediction

This example shows how to use the deep neural network models which trained on Sunergy to predict face attributes such as age, gender, and mood. Here we take the age prediction as an example.
## How to use on **Linux**:
## How to use on **Windows**:

#### 1. Start MSVS, open Sunergy.sln, set x64 and Release.

#### 2. Rename the image you want to predict and put it in this path："../../model/classifier/en.jpg"
```C++
char image_file[] = "../../model/classifier/en.jpg";
```
#### &nbsp;&nbsp;&nbsp; Check if the following four files' locations and names are consistent with  the following code.


```C++
char names[] = "../../model/classifier/age1.1.names";
char cfg_file[] = "../../model/classifier/age1.1.cfg";
char weight_file[] = "../../model/classifier/age1.1.weights";
char image_file[] = "../../model/classifier/en.jpg";
```
&nbsp;&nbsp;**age1.1.names** ---The value of the attribute to be predicted, as in this case,  age.  
&nbsp;&nbsp;**age1.1.cfg** ---The structure of the deep neural network.  
&nbsp;&nbsp;**age1.1.weights** ---Trained weight.  
&nbsp;&nbsp; **en.jpg** --- The image you want to predict

#### 3. Do the: Build -> Build Sunergy.
#### 4. Do the: Build -> Build face_attribute_prediction. 
####  &nbsp;&nbsp;&nbsp;&nbsp;Set *Build face_attribute_prediction* as the startup project and run it.
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
	data->step = src->width * 3;
	data->batch = 1;
	data->channel = 3;
	data->data_location = CPU_DATA;
	data->format = BGR;
	data->preprocessing = false;
	data->type = DATA_UCHAR;
	data->data = src->imageData;
}

void show__faceimage(char* file, detect_out_t* out)
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
	set_sunergy_param(sun, 0, 0, 255, 0.25);
	set_data(image_file, &data);
	printf("start\n");
	
    //Running object classifier
    /* The meaning of the value of ret：
     0x0000     SUNERGY_SUCCESS
     0x0101     SUNERGY_HANDLE_ERROR	 
     0x0201     SUNERGY_BAD_PARAM		
     0x0301     SUNERGY_BAD_IMGPARAM	 
    */
	ret = sunergy_classifier(sun, data, top, &out);

    //print 
	for (int i = 0; i < top; i++)
		//printf("%s\n", i, out[i].names);					
	{
		printf("%d", i); 
		printf("%s", out[i].names);
		printf("%f\n", out[i].confident);					
	}

	show_image(image_file, &out);
	ret = sunergy_free(sun);
}
```
