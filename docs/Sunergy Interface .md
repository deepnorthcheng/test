# **Sunergy** **Interface**
## 1 Sunergy Datatypes Reference
### 1.1 sunergy_t
**sunergy_t** is a pointer to an opaque structure holding the sunergy library context.The sunergy library context must be created using **sunergy_init**() and the returned handle must be passed to all subsequent library function calls. The context should be destroyed at the end using **sunergy_free**().
## 1.2 data_format_t
**data_format_t** is an enumerated type indicating the data type to **sunergy_data_t** refers

**value**

```
- RGB: 
the image format is RGB with three channels like [RGB, RGB, RGB, RGB...]

- BGR: 
the image format is BGR with three channels like [BGR, BGR, BGR, BGR...]

- RGBP: 
the image format is RGBP with three plate like [RRRRR..., GGGGG..., BBBBB...]

- BGRP: 
the image format is RGBP with three plate like [BBBBB..., GGGGG..., RRRRR...]
```

### 1.3 data_type_t
**data_type_t** is an enumerated type indicating the data type to **sunergy_data_t** refers.

**value:**
```
- DATA_FLOAT: 
the data is 32-bit single-precision floating point (float).

- DATA_CHAR: 
the data is 8-bit unsigned integer(unsigned char).
```

### 1.4 sunergy_data_t
**sunergy_data_t** is  an structure about the description of sunergy image data

**value:**


```
- width:   			    width of image 
- height:			    height of image
- step: 				step of image
- channel: 			    number of feature maps per image. 
- batch: 			    number of images. 
- data_location:	    the location stored in the image data 0:CPU 1:GPU preprocessing;	Whether the image is preprocessed 	can directly use network computing operations. 
- format: 			    format of image refer data_format_t
- type:				    type of image refer data_type_t
- data: 				data pointer to memory associated with image
```


### 1.5 classifier_out_t
**classifier_out_t** is  an structure about the description of classifier information

**value:**

```
- index:			the number of classification
- names:			name of classification
- confident: 	    confidence of classification
```

### 1.6 pose_point_t
**pose_point_t** is an structure about the description of one human pose point

**value:**

```
- X:
it is an array of 18 length, which describes the X coordinates of 18 pose point of each human body.

- Y: 
it is an array of 18 length, which describes the Y coordinates of 18 pose point of each human body.
- confident:
it is an array of 18 length, which describes the confidence of 18 pose point of each human body.
```
### 1.7 pose_out_t
**pose_out_t** is an structure about the result of **sunergy_pose**() API

**value:**

```
- human_num : 	    total human number of pose detection
- pose: 			information about each human refer to pose_point_t
```

### 1.8 detect_info_t 
**detect_info_t** is an structure about the description of one object detect

**value:**

```
- name: 			name of detect and classifer
- confident:		confidence of detect
- left: 			left of detect bounding box 
- right:			right of detect bounding box 
- top: 			    top of detect bounding box 
- bottom:		    bottom of detect bounding box
```

### 1.9 detect_out_t
**detect_out_t** is an structure about the result of **sunergy_detect**() API

**value:**

```
- total_num : 	    total number of object detection
- info: 			information about each object refer to detect_info_t
```

### 1.10 extract_out_t
**extract_out_t** is an structure about the result of **sunergy_extract**() API

**value:**


```
- size: 		the size of feature array
- feat: 		the floating point array about image feature
```

### 1.11 net_information_t
**net_information_t** is basic information of network layer refer to result of **sunergy_get_network_infomation**()API

**value:**

```
- in_width: 		width of current layer input feature map
- in_height: 	    height of current layer input feature map
- in_channel: 	    channel of current layer input feature map
- out_width: 	    width of current layer output feature map
- out_height: 	    height of current layer output feature map
- out_channel: 	    channel of current layer output feature map
```

## 2 Sunergy API Reference
### 2.1 sunergy_init()

```
sunergy_t sunergy_init(
char *names_file,
char *cfg_file,
char *model_file,
int gpu_idx, 
bool inference);
```
This function initializes the sunergy library and creates a handle to an opaque structure holding the sunergy library context. The sunergy library handle is tied to the one network. To use the library on multiple network, one sunergy handle needs to be created for each network

**Parameters**

```
- names_file:
<input> the path to the classifer name file

- cfg_file:
<input> the path to the cfg file about network

- model_file:
<input>the path to the model file.

- gpu_idx:
<input>the index of GPU device.

- inference:
<input> the label about network running state
```

**Returns**

```
- handle:
<output>sunergy handle this greater than 0

- SUNERGY_BAD_PARAM
One of the point names_file, cfg_file, model_file, is NULL
```

### 2.2 sunergy_free()

```
int	 sunergy_free(sunergy_t handle);
```


This function releases resources used by the sunergy handle. This function is usually the last call with the sunergy handle.

**Parameters**

```
- handle:
<input> handle to a previously created sunergy library descriptor.
```
**Returns**

```
- SUNERGY_SUCCESS
The operation was launched successfully.

- SUNERGY_BAD_PARAM
Invalid (NULL) pointer supplied of handle
```

### 2.3 sunergy_detect()

```
int sunergy_detect(
sunergy_t handle, sunergy_data_t data, detect_out_t* out);
```

This function running object detection

**Parameters**

```
- handle:
<input> handle to a previously created sunergy library descriptor.

- data:
<input> image of sunergy data

- out:
<output>result of detection
```

**Returns**

```
- SUNERGY_SUCCESS
The operation was launched successfully.

SUNERGY_HANDLE_ERROR 
Invalid (NULL) pointer supplied of handle

- SUNERGY_BAD_PARAM

At least one of the following conditions are met:
 	one of the point data.data out is NULL
	data.batch is not 1
	one of the variable  data.weight data.height data.step data.channel is fewer than 1

- SUNERGY_BAD_IMGPARAM

At least one of the following conditions are met:
	function set_sunergy_param() is not running
	parameter of function set_sunergy_param(),width or height if fewer than 1
```
### 2.4 sunergy_extract()

```
int sunergy_extract(
sunergy_t handle, sunergy_data_t data, extract_out_t* out);
```


This function running object extract

**Parameters**

```
- handle:
<input> handle to a previously created sunergy library descriptor.

- data:
<input> image of sunergy data

- out:
<output>result of extract
```

**Returns**

```
- SUNERGY_SUCCESS
The operation was launched successfully.

- SUNERGY_HANDLE_ERROR
Invalid (NULL) pointer supplied of handle

- SUNERGY_BAD_PARAM
At least one of the following conditions are met:
one of the point data.data out is NULL
one of the variable  data.weight data.height data.step data.channel is fewer than 1
```
### 2.5 sunergy_pose()

```
int sunergy_pose(
sunergy_t handle, sunergy_data_t data, pose_out_t* out);
```

This function running pose detection

**Parameters**

```
- handle:
<input> handle to a previously created sunergy library descriptor.

- data:
<input> image of sunergy data

- out:
<output>result of pose detection
```

**Returns**

```
- SUNERGY_SUCCESS
The operation was launched successfully.

- SUNERGY_HANDLE_ERROR
Invalid (NULL) pointer supplied of handle

- SUNERGY_BAD_PARAM
At least one of the following conditions are met:
 	one of the point data.data out is NULL
	data.batch is not 1
	one of the variable  data.weight data.height data.step data.channel is fewer than 1

- SUNERGY_BAD_IMGPARAM
At least one of the following conditions are met:
	function set_sunergy_param() is not running
	parameter of function set_sunergy_param(),width or height if fewer than 1
```
### 2.6 sunergy_classifier()

```
int sunergy_classifier(
sunergy_t handle, sunergy_data_t data, int top, classifier_out_t** out);
```

This function running object classifier

**Parameters**

```
- handle:
<input> handle to a previously created sunergy library descriptor.

- data:
<input> image of sunergy data

- top:
<input> max number of return classifer

- out:
<output>result of classifer
```

**Returns**

```
- SUNERGY_SUCCESS
The operation was launched successfully.

- SUNERGY_HANDLE_ERROR
Invalid (NULL) pointer supplied of handle

- SUNERGY_BAD_PARAM
At least one of the following conditions are met:
 	one of the point data.data out is NULL
	data.batch is not 1
	one of the variable  data.weight data.height data.step data.channel is fewer than 1
```

### 2.7 sunergy_get_network_depth()

```
int sunergy_get_network_depth(sunergy_t handle);
```
This function get the depth of sunergy network.
**Parameters**

```
- handle:
<input> handle to a previously created sunergy library descriptor.
```

**Returns**
```
- num:
<output> the depth of sunergy network

- SUNERGY_SUCCESS
The operation was launched successfully.

- SUNERGY_HANDLE_ERROR
Invalid (NULL) pointer supplied of handle
```

###  2.8 sunergy_get_network_infomation()

```
int sunergy_get_network_infomation(
sunergy_t handle, int num, net_information_t* info);
```
This function get the information of sunergy network layer.
**Parameters**
```
- handle:
<input> handle to a previously created sunergy library descriptor.
```

**Returns**
```
- SUNERGY_SUCCESS
The operation was launched successfully.

- SUNERGY_HANDLE_ERROR
Invalid (NULL) pointer supplied of handle

- SUNERGY_BAD_PARAM
At least one of the following conditions are met:
 	one of the point info out is NULL
	num if fewer than zero
```

### 2.9 set_sunergy_param()


```
int set_sunergy_param(sunergy_t handle, float nms, float means, float normalization,float threshold, int width, int height);
```


This function set parameter of sunergy network ,it must running before sunergy_detect()sunergy_pose()

**Parameters**

```
- handle:
<input> handle to a previously created sunergy library descriptor.

- nms:
<input> nms of detection

- means:
<input> image preprocessing means if this parameter is no useful please set zero

- normalization:
<input> image preprocessing normalization if this parameter is no useful please set zero

- threshold:
<input> threshold about detection if this parameter is no useful please set zero

- width:
<input> image width about detection or pose if this parameter is no useful please set zero

- height:
<input> image height about detection if this parameter is no useful please set zero
```

**Returns**
```
- SUNERGY_SUCCESS
The operation was launched successfully.

- SUNERGY_HANDLE_ERROR
Invalid (NULL) pointer supplied of handle
```
