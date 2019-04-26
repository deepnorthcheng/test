# cuda8.0 and cudnn v6


## Install cuda8.0
Using S3 download cuda8.0
```bash
wget https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/cuda/cuda_8.0.61_375.26_linux.run
```



Setting Access Permissions

```bash
chmod 777 cuda_8.0.61_375.26_linux.run 
```

Install
```bash
sudo ./cuda_8.0.61_375.26_linux.run
```

ctrl+c skip the words



Installation proceeds with the following options

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

Set cuda path
```bash
sudo gedit ~/.bashrc
```

Insert
```bash
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

Save and exit
```bash
source ~/.bashrc 
```

## Install cudnn-v6
Using S3 download cudnnv6
```bash
wget https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/cudnn/cudnn-8.0-linux-x64-v6.0.tgz
```


Set access permissions and Unzip

```bash
chmod 777 cudnn-8.0-linux-x64-v6.0.tgz 
tar -xvf cudnn-8.0-linux-x64-v6.0.tgz 
```

Copy files to CUDA directory

```bash
sudo cp cuda/include/cudnn.h /usr/local/cuda-8.0/include/cudnn.h 
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
```

Compile smaple and execute sample
```bash
cd ~/NVIDIA_CUDA-8.0_Samples/1_Utilities/deviceQuery/
make
./deviceQuery 
```

Final display of PASS after execution indicates successful installation
