## Python 接口

# Python 接口

## 载入Sunergy库

```bash
import  libpysunergy
```

## 获取当前sunergy库的版本
```bash
libpysunergy.version()
```
调用此接口可以在终端打印当前sunergy的版本号，例如1.1.0

## 加载深度学习网络
sunergy框架使用.cfg文件和.weights文件组成的网络配置文件进行加载
```bash
net,names=libpysunergy.load(data_file,cfg_file,weight_file)
```
输入参数：
- `data_file` ：分类的类别文件，比如 "data/coco.data"
- `cfg_file` ：网络的配置文件，比如 "model/yolo.cfg"
- `weight_file` ：网络的模型文件，比如 "medel/yolo.weight"

输出参数:
- `net` ：定义网络的句柄
- `names` ：分类的具体类别

## 预测分类
sunergy框架处理分类问题
```bash
dst=libpysunergy.predict(img_data, w, h, c, top, net, names)
```

输入参数：
- `img_data` ：进行分类的BGR图像数据
- `w` ：输入图像的宽度
- `h` ：输入图像的高度
- `c` ：输入图像的通道数
- `top` ：输出结果中推荐分类个数
- `net` ：sunergy网络句柄
- `names` ：分类的具体类别名称

输出参数:
- `dst` ：结果为python的列表
		比如：	dst[0][0]——top 1 name
				dst[0][1]——top 1 confident
				dst[1][0]——top 2 name
				dst[1][1]——top 2 confident

## 预测分类增强版
比普通分类兼容更多内容，比如骨架检测等。
```bash
output=libpysunergy.predict_expand(img_data,w,h,c,top,net,names,type,normalization,batch,means)
```
输入参数：
- `img_data` ：进行分类的RGB图像数据,图像需要重采样至网络的输入层大小
- `w` ：输入图像重采样后的宽度
- `h` ：输入图像重采样后的高度
- `c` ：输入图像重采样后的通道数
- `top` ：输出结果中推荐分类个数
- `net` ：sunergy网络句柄
- `names` ：分类的具体类别名称
- `type` ：分类的扩展类别（支持：骨架检测[‘openpose‘]）
- `normalization` ：归一化参数，若不需要归一化则为 1.0
- `batch` ：批量处理图像个数
- `means` ：预处理进网络前的均值差。若不需要则为0

输出参数:
	骨架检测->输出参数: human_num, point_data
		- `human_num` ：检测到对应人数
		- `point_data` ：检测的骨架节点和置信度，总大小为3*18*human_num （3：每个节点，左坐标，右坐标，置信度。18：每个人有18个节点）

## 多通道提取特征
```bash
feat = libpysunergy.extract_batch(img_data, w, h, c, batch, net)
```
输入参数：
- `img_data` ：进行分类的RGB图像数据,图像需要重采样至网络的输入层大小
- `w` ：输入图像重采样后的宽度
- `h` ：输入图像重采样后的高度
- `c` ：输入图像重采样后的通道数
- `batch` ：批量处理图像个数
- `net` ：sunergy网络句柄
输出参数:
- `feat` ：提取特征的结果


## 获取网络深度
```bash
n = libpysunergy.get_net_depth(net)
```
输入参数：
- `net` ：sunergy网络句柄
输出参数:
- `n` : 返回网络对应的深度

## 获取网络层宽度
```bash
w = libpysunergy.get_net_width(net, n , type)
```
输入参数：
- `net` ：sunergy网络句柄
- `n` ：获取网络的层号
- `type`：获取输入或者输出层的属性（’in‘或者’out‘） 
输出参数:
- `w` : 返回网络对应层的宽度

## 获取网络层高度
```bash
h = libpysunergy.get_net_height(net, n , type)
```
输入参数：
- `net` ：sunergy网络句柄
- `n` ：获取网络的层号
- `type`：获取输入或者输出层的属性（’in‘或者’out‘） 
输出参数:
- `h` : 返回网络对应层的高度

## 获取网络层通道数
```bash
c = libpysunergy.get_net_channel(net, n , type)
```
输入参数：
- `net` ：sunergy网络句柄
- `n` ：获取网络的层号
- `type`：获取输入或者输出层的属性（’in‘或者’out‘） 
输出参数:
- `c` : 返回网络对应层的通道数

## 释放网络
```bash
libpysunergy.free(net)
```
输入参数：
- `net` ：sunergy网络句柄
