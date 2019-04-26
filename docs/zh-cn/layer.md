## 定义新的层

### 首要条件
* 分析自定义层所要实现的功能
* 该层在整个网络中的位置
* 数据的变化
* 与上下层之间的连接关系

### 1. 先设计想要创建的层
```
[mylayer]
param1=3
param2=2
param3=1
```

### 2. src中新建mylayer.c 和mylayer.h，在makefile 中 obj依赖添加mylayer.o

### 3. layer.h 中 LAYER_TYPE 添加 MYLAYER

### 4. parser.c中
```
include “mylayer.h”
string_to_layer_type函数中添加 if(strcmp(type, “[mylayer]”) ==0) return MYLAYER
添加parse_mylayer函数
parse_network_cfg中加入
else if(lt == MYLAYER){
	l = parse_mylayer(arg);
}
```

### 5. network.c 中
```
include “mylayer.h”, 
get_layer_string函数中添加 
case MYLAYER:  return “MYLAYER”
resize_network函数中添加：
}else if(l.type == MYLAYER){
   mylayer_layer(&l, w, h);}
```

### 6. 完善mylayer.c
```
layer make_my_layer(param, ...)
forward_my_layer(const layer l, network_state state)
```

