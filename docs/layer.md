## Define your own layers

### Essential condition:
* Analyzing the Functions of Custom Layer
* Location of this layer in the whole network
* Data flow
* Connection with upper and lower layers	

### 1. Design the layer you want to create first
```
[mylayer]
param1=3
param2=2
param3=1
```

### 2. Create mylayer.c and mylayer.h in src, And modify Makefile (obj: mylayer.o)

### 3. Modify layer.h: enem LAYER_TYPE add MYLAYER

### 4. parser.c
```
include “mylayer.h”,
Funtion string_to_layer_type Add:  if(strcmp(type, “[mylayer]”) ==0) return MYLAYER
Add Function: parse_mylayer
Modify Function: parse_network_cfg (Add the following code: 
else if(lt == MYLAYER){  l = parse_mylayer(arg);} )
```

### 5. network.c 
```
include “mylayer.h”
Function : get_layer_string.  Add(case MYLAYER:  return “MYLAYER”)
Function : resize_network.  Add( else if(l.type == MYLAYER){mylayer_layer(&l, w, h);} )
```

### 6. mylayer.c
```
layer make_my_layer(param, ...)
forward_my_layer(const layer l, network_state state)
```
