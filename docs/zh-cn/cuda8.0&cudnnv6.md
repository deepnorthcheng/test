# cuda8.0 and cudnn v6


## 安装cuda8.0
使用S3连接下载cuda8.0
```bash
wget https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/cuda/cuda_8.0.61_375.26_linux.run
```

设置访问权限
```bash
chmod 777 cuda_8.0.61_375.26_linux.run 
```

执行安装
```bash
sudo ./cuda_8.0.61_375.26_linux.run
```

ctrl+c跳过文字

安装过程按如下选项
```bash
Do you accept the previously read EULA?
accept/decline/quit: accept

Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 375.26?
(y)es/(n)o/(q)uit: n

Install the CUDA 8.0 Toolkit?
(y)es/(n)o/(q)uit: y

Enter Toolkit Location
 [ default is /usr/local/cuda-8.0 ]: enter

Do you want to install a symbolic link at /usr/local/cuda?
(y)es/(n)o/(q)uit: y

Install the CUDA 8.0 Samples?
(y)es/(n)o/(q)uit: y

Enter CUDA Samples Location
 [ default is /home/ubuntu ]: enter
```

设置cuda路径
```bash
sudo gedit ~/.bashrc
```

末尾加入
```bash
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

保存关闭
```bash
source ~/.bashrc 
```

## 安装cudnn-v6
使用S3连接下载cudnnv6版本
```bash
wget https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/cudnn/cudnn-8.0-linux-x64-v6.0.tgz
```

设置访问权限并解压
```bash
chmod 777 cudnn-8.0-linux-x64-v6.0.tgz 
tar -xvf cudnn-8.0-linux-x64-v6.0.tgz 
```

拷贝文件至cuda目录
```bash
sudo cp cuda/include/cudnn.h /usr/local/cuda-8.0/include/cudnn.h 
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
```

编译smaple并执行测试安装结果
```bash
cd ~/NVIDIA_CUDA-8.0_Samples/1_Utilities/deviceQuery/
make
./deviceQuery 
```

执行后最终显示PASS则说明安装成功
