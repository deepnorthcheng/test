## 目标检测

这个例子展示了如何使用Sunergy上训练好的深度神经网络进行目标检测，如：


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
