## Pose estimation


This example shows how to use the deep neural network models which trained on Sunergy to do pose estimation.
## How to use on **Linux**:
## How to use on **Windows**:

#### 1. Start MSVS, open Sunergy.sln, set x64 and Debug/Release.

#### 2. Rename the image you want to predict and put it in this path："../../model/pose/person.jpg".
```C++
char image_file[] = "../../model/pose/person.jpg";
```
#### &nbsp;&nbsp;&nbsp; Check if the following four files' locations and names are consistent with  the following code.


```C++
char names[] = "../../model/pose/coco.names";
char cfg_file[] = "../../model/pose/pose.cfg";
char weight_file[] = "../../model/pose/pose.weights";
char image_file[] = "../../model/pose/person.jpg";
```
&nbsp;&nbsp;**coco.names** --- Predicted value ？？？？   X
&nbsp;&nbsp;**pose.cfg** --- The structure of the deep neural network.  
&nbsp;&nbsp;**pose.weights** --- Trained weight.  
&nbsp;&nbsp;**person.jpg** --- The image you want to estimation.

#### 3. Do the: Build -> Build Sunergy.
#### 4. Do the: Build -> Build pose_estimation. 
####  &nbsp;&nbsp;&nbsp;&nbsp;Set *pose_estimation* as the startup project and run it.
&nbsp;
#### Code:
