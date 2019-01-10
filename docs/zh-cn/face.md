## 人脸属性预测

这个例子展示了如何使用 Sunergy上训练好的深度神经网络进行人脸属性预测，如：年龄，性别，情绪等。本文以年龄预测为例。


## 在**Linux**上使用:
## 在 **Windows**上使用:  &nbsp;

#### 1. 启动MSVS, 打开项目 Sunergy.sln, 解决方案配置选择 x64 和 Debug/Release .

### 2. 将你需要预测的图片重命名并放入路径：  "../../model/classifier/en.jpg".
```python
	char image_file[] = "../../model/classifier/en.jpg";
```
###  &nbsp;&nbsp;&nbsp;检查以下三个文件是否在相应位置，名称是否与代码中一致 .

```python
    char names[] = "../../model/classifier/age1.1.names";
	char cfg_file[] = "../../model/classifier/age1.1.cfg";
	char weight_file[] = "../../model/classifier/age1.1.weights";
	char image_file[] = "../../model/classifier/en.jpg";
```
 &nbsp;&nbsp; **age1.1.names** 文件所要预测属性的值，如本例中要预测年龄，则文件内容为年龄值 0，1，2，3 ... 100
 &nbsp;&nbsp; **age1.1.cfg** 文件是所采用的深度神经网络的结构
 &nbsp;&nbsp; **age1.1.weights** 文件是已训练好的权重
 &nbsp;&nbsp; **en.jpg** 文件是您需要预测人脸属性的图片

### 3. 选择项目Sunergy，右击鼠标选择生成解决方案 .
### 4. 选择项目face_attribute_prediction，右击鼠标选择生成解决方案 .
###  &nbsp;&nbsp;&nbsp;  再次右击鼠标将其设为启动项目，并运行 .


*参考代码:*  

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
		printf("%f\n", out[i].confident);					//add it?
	}

	show_image(image_file, &out);
	ret = sunergy_free(sun);
}
```
