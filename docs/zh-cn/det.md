## 目标检测

这个例子展示了如何使用Sunergy上训练好的深度神经网络进行目标检测.


## 在**Linux**上使用:
## 在 **Windows**上使用:  &nbsp;

#### 1. 启动MSVS, 打开项目 Sunergy.sln, 解决方案配置选择 x64 和 Debug/Release .

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

#### 3. 选择项目Sunergy，右击鼠标选择生成解决方案 .
#### 4. 选择项目object_detection，右击鼠标选择生成解决方案 .
####  &nbsp;&nbsp;&nbsp;&nbsp; 再次右击鼠标将其设为启动项目，并运行 .
&nbsp;
#### *参考代码:*  

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
	for (int i = 0; i < out.total_num; i++)
		printf("%d,%s %f,[%f,%f,%f,%f]\n", i, out.info[i].name, out.info[i].confident, out.info[i].left, out.info[i].right,
		out.info[i].top, out.info[i].bottom);
	show_image_dete(image_file,&out);					
	ret = sunergy_free(sun);
	system("pause");								   
}

```
