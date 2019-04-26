# C 接口
## 1 Sunergy 数据格式
### 1.1 sunergy_t
**sunergy_t** 是一个隐藏结构体指针，它可以获取sunergy库的信息。sunergy库必须使用 **sunergy_init**() 创建，返回的句柄必须传递给所有后续库函数调用。使用 **sunergy_free**() 在末尾销毁该指针。
## 1.2 data_format_t
**data_format_t** 是一个枚举类型，表示**sunergy_data_t**引用的数据类型

**value**

```
- RGB: 
图像格式为RGB，有三个通道，如 [RGB, RGB, RGB, RGB...]

- BGR: 
图像格式为BGR，有三个通道，如 [BGR, BGR, BGR, BGR...]

- RGBP: 
图像格式为RGBP，有三个平面 [RRRRR..., GGGGG..., BBBBB...]

- BGRP: 
图像格式为BGRP，有三个平面 [BBBBB..., GGGGG..., RRRRR...]
```

### 1.3 data_type_t
**data_type_t**是一个枚举类型，表示指向**sunergy_data_t** *引用的数据类型。

**value:**
```
- DATA_FLOAT: 
数据为32位单精度浮点数(float)。

- DATA_CHAR: 
数据是8位无符号整数(unsigned char)。
```

### 1.4 sunergy_data_t
**sunergy_data_t** 是一种描述sunergy图像数据的结构

**value:**


```
- width:   			    图像的宽度
- height:			    图像的高度
- step: 			    图像的步长
- channel: 			    图像的通道数
- batch: 			    图片数量 
- data_location:	            图像数据存储的位置 0:CPU 1:GPU
- preprocessing：             	    图像是否预处理可以直接使用网络计算操作
- format: 			    图像格式参考data_format_t
- type:				    图像类型参考data_type_t
- data: 			    数据指针，指向与图像相关的内存
```


### 1.5 classifier_out_t
**classifier_out_t**是一个关于分类器信息描述的结构

**value:**

```
- index:			分类的序号
- names:			分类的名称
- confident: 	    		分类置信度
```

### 1.6 pose_point_t
**pose_point_t** 是一个关于描述人体姿态的结构

**value:**

```
- X:
它是一个长度为18的数组，它描述了人体18个位点的X坐标。

- Y: 
它是一个长度为18的数组，描述了人体18个位点的Y坐标。

- confident:
它是一个长度为18的数组，它描述了人体18个位点的置信度。
```
### 1.7 pose_out_t
是一个关于**sunergy_pose** *() API结果的结构

**value:**

```
- human_num : 	    姿态检测的总人数

- pose: 			关于每个人的姿态信息参考pose_point_t
```

### 1.8 detect_info_t 
**detect_info_t**是一个描述目标检测的结构

**value:**

```
- name: 			检测和分类的名称
- confident:		检测置信度
- left: 			检测边框的左边位置
- right:			检测边框的右边位置 
- top: 			    检测边框的上部位置
- bottom:		    检测边框的底部位置
```

### 1.9 detect_out_t
**detect_out_t**是一个关于**sunergy_detect** *() API结果的结构

**value:**

```
- total_num : 	    目标检测总数
- info: 			关于每个对象的信息参考detect_info_t
```

### 1.10 extract_out_t
**extract_out_t**是一个关于**sunergy_extract** *() API结果的结构

**value:**


```
- size: 		特征数组的大小
- feat: 		图像特征的浮点数组
```

### 1.11 net_information_t
**net_information_t** *是网络层的基本信息，参考**sunergy_get_network_infomation** *()API的结果

**value:**

```
- in_width: 		当前层输入特征图的宽度
- in_height: 	    当前层输入特征图的高度
- in_channel: 	    当前层输入特征图通道
- out_width: 	    当前层输出特征图宽度
- out_height: 	    当前层输出特征图高度
- out_channel: 	    当前层输出特征图通道数
```

## 2 Sunergy API 接口
### 2.1 sunergy_init()

```
sunergy_t sunergy_init(
char *names_file,
char *cfg_file,
char *model_file,
int gpu_idx, 
bool inference);
```
初始化sunergy库，并创建一个用于保存sunergy库的隐藏结构的句柄。sunergy库句柄与一个网络相连。要在多个网络上使用库，需要为每个网络创建一个sunergy句柄

**Parameters**

```
- names_file:
<input> 分类器名称文件的路径

- cfg_file:
<input> 关于网络的cfg文件的路径

- model_file:
<input>模型文件的路径

- gpu_idx:
<input>GPU的ID

- inference:
<input> 关于网络运行状态的标志
```

**Returns**

```
- handle:
<output>返回sunergy句柄不能为空

- SUNERGY_BAD_PARAM
names_file, cfg_file, model_file,中某一个指向为空
```

### 2.2 sunergy_free()

```
int	 sunergy_free(sunergy_t handle);
```

释放sunergy句柄使用的资源。这个函数通常是sunergy句柄的最后一个调用。

**Parameters**

```
- handle:
<input> 先前创建的sunergy库描述符的句柄。
```
**Returns**

```
- SUNERGY_SUCCESS
函数成功运行

- SUNERGY_BAD_PARAM
句柄提供的指针无效(空)
```

### 2.3 sunergy_detect()

```
int sunergy_detect(
sunergy_t handle, sunergy_data_t data, detect_out_t* out);
```

运行目标检测主程序

**Parameters**

```
- handle:
<input> 先前创建的sunergy库描述符的句柄。

- data:
<input> sunergy图像数据

- out:
<output>检测结果
```

**Returns**

```
- SUNERGY_SUCCESS
函数成功运行

SUNERGY_HANDLE_ERROR 
句柄提供的指针无效(空)

- SUNERGY_BAD_PARAM
其中一个点的数据data.data为空 或 data.batch不是1 或 变量 data.width data.height data.step data.channel其中一个小于1

- SUNERGY_BAD_IMGPARAM
函数 set_sunergy_param() 没有运行 或  函数set_sunergy_param()的参数width，height小于1
```
### 2.4 sunergy_extract()

```
int sunergy_extract(
sunergy_t handle, sunergy_data_t data, extract_out_t* out);
```

运行目标提取函数

**Parameters**

```
- handle:
<input> 先前创建的sunergy库描述符的句柄

- data:
<input> sunergy图像数据

- out:
<output>提取结果
```

**Returns**

```
- SUNERGY_SUCCESS
函数成功运行

- SUNERGY_HANDLE_ERROR
句柄提供的指针无效(空)

- SUNERGY_BAD_PARAM
其中一个点的数据data.data是空的 或 变量 data.weight data.height data.step data.channel其中一个小于1
```
### 2.5 sunergy_pose()

```
int sunergy_pose(
sunergy_t handle, sunergy_data_t data, pose_out_t* out);
```

运行人体姿态检测函数

**Parameters**

```
- handle:
<input> 先前创建的sunergy库描述符的句柄

- data:
<input> sunergy图像数据

- out:
<output>人体姿态检测结果
```

**Returns**

```
- SUNERGY_SUCCESS
函数成功运行

- SUNERGY_HANDLE_ERROR
句柄提供的指针无效(空)

- SUNERGY_BAD_PARAM
 其中一个点的数据data.data是空的 或 data.batch不是1 或 变量data.weight data.height data.step data.channel其中一个小于1

- SUNERGY_BAD_IMGPARAM
函数 set_sunergy_param()没有运行 或 函数set_sunergy_param()的参数width或height小于 1
```
### 2.6 sunergy_classifier()

```
int sunergy_classifier(
sunergy_t handle, sunergy_data_t data, int top, classifier_out_t** out);
```

运行目标分类函数

**Parameters**

```
- handle:
<input> 先前创建的sunergy库描述符的句柄

- data:
<input> sunergy图像数据

- top:
<input> 返回分类结果的最大值

- out:
<output>分类结果
```

**Returns**

```
- SUNERGY_SUCCESS
函数成功运行

- SUNERGY_HANDLE_ERROR
句柄提供的指针无效(空)

- SUNERGY_BAD_PARAM
其中一个点的数据data.data是空的 或 data.batch不是1 或 变量data.weight data.height data.step data.channel其中一个小于1
```

### 2.7 sunergy_get_network_depth()

```
int sunergy_get_network_depth(sunergy_t handle);
```
获取sunergy网络的深度。

**Parameters**

```
- handle:
<input> 先前创建的sunergy库描述符的句柄
```

**Returns**
```
- num:
<output> sunergy网络的深度

- SUNERGY_SUCCESS
函数成功运行

- SUNERGY_HANDLE_ERROR
句柄提供的指针无效(空)
```

###  2.8 sunergy_get_network_infomation()

```
int sunergy_get_network_infomation(
sunergy_t handle, int num, net_information_t* info);
```
获取sunergy网络层的信息
**Parameters**
```
- handle:
<input> 先前创建的sunergy库描述符的句柄
```

**Returns**
```
- SUNERGY_SUCCESS
函数成功运行

- SUNERGY_HANDLE_ERROR
句柄提供的指针无效(空)

- SUNERGY_BAD_PARAM
其中某一点的信息为空或num为0
```

### 2.9 set_sunergy_param()


```
int set_sunergy_param(sunergy_t handle, float nms, float means, float normalization,float threshold, int width, int height);
```

该函数设置了sunergy网络的参数，它必须在sunergy_detect()sunergy_pose()之前运行

**Parameters**

```
- handle:
<input> 先前创建的sunergy库描述符的句柄

- nms:
<input> 检测的nms

- means:
<input> 图像预处理均值参数，如果这个参数没有用，请设置为零

- normalization:
<input> 图像预处理归一化参数，如果这个参数没有用，请设置为零

- threshold:
<input> 检测阈值，如果这个参数没有用，请设置为零

- width:
<input> 目标或人体姿态检测图像宽度，如果这个参数没用，请设置为零

- height:
<input> 目标或人体姿态检测图像高度，如果这个参数没用，请设置为零
```

**Returns**
```
- SUNERGY_SUCCESS
函数成功运行

- SUNERGY_HANDLE_ERROR
句柄提供的指针无效(空)

```



