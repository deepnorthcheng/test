# Sunergy Python Interface
## 1 import libsunergy

## 2 sunergy image 
make python-dictionary about sunergy image before use sunergy interface 

```
image={

    'width': 			image width
    'height': 			image height
    'step': 			image step
    'channel': 		    	image channel
    'batch':			image batch
    'location':		    	location of image data 0:CPU 1:GPU
    'preprocessing':		image data run in network directly,0:image with no preprocessing 1: image have preprocessed
    'format':			format of image data 0:RGB 1:BGR 2: RGP 3:BGRP
    'type':			type of image data 0: char 1: float
    'data':			data of image

}
```

## 3 sunergy initialize

```
net = sun.init(names_file,cfg_file,weight_file,gpu_id,interface)
```

This function initializes the sunergy

**Parameters**


```
names_file:
<input> the path to the classifer name file

cfg_file:
<input> the path to the cfg file about network

model_file:
<input>the path to the model file.

gpu_idx:
<input>the index of GPU device. this is an optional parameter 

inference:
<input> the label about network running state. this is an optional parameter,defalut is true

```
**Returns**

```
net:
<output>network of sunergy
```


## 4 releases resources sunergy

```
sun.free(net)
```

This function releases resources used by the sunergy network.

**Parameters**


```
net:
<input>network of sunergy
```


## 5 sunergy object detection
This function running object detection

```
out = sun.detect(net,img)
```


**Parameters**


```
net:
<input>network of sunergy

img:
<input> python-dictionary about sunergy image
```



**Returns**


```
out:
<output>list-python about detecion result
[{'name':},{'condident':},{'left':},{'right':},{'top':},{'bottom':},
{'name':},{'condident':},{'left':},{'right':},{'top':},{'bottom':}
......
]
```


## 6 sunergy pose detection

This function running pose detection


```
out = sun.pose(net,img)
```


**Parameters**



```
net:
<input>network of sunergy

img:
<input> python-dictionary about sunergy image
```


**Returns**



```
out:
<output>list-python about pose detecion result
[{'X':[lenth =18]} ,{'Y':[ lenth =18]},{'condident':[ lenth =18]} ,
{'X':[ lenth =18]} ,{'Y':[ lenth =18]},{'condident':[ lenth =18]}
......
]
```


## 7 sunergy feature extract

This function running object extract


```
out = sun.extract(net,img)
```



**Parameters**



```
net:
<input>network of sunergy

img:
<input> python-dictionary about sunergy image
```



**Returns**



```
out:
<output>list-python about feature result
```


## Records

Version | Data | Author | Notes
---|--- | --- | ---
1.0.0 | 2018.8.16 | Mingzhe Pan | Original version



























