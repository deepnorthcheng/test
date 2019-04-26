# GPU Driver

## 查看内核版本
```bash
uname -r 
```
4.10.0-42-generic 
版本号要小于4.13.0才可以使用

关闭内核更新
```bash
sudo apt-mark hold linux-image-extra-4.10.0-28-generic
sudo apt-mark hold linux-image-4.10.0-28-generic
```

系统设置-> 软件和更新->更新
修改成如图形式
![](https://github.com/VMaxxInc/Sunergy_multiPlatform/blob/master/docs/screenshot/system_config.png)

## 禁用 nouveau 驱动
以sudo权限打开 
```bash
sudo gedit /etc/modprobe.d/blacklist-nouveau.conf 
```

在文件的末尾添加以下内容 
```bash
blacklist nouveau  
options nouveau modset=0
```

保存退出
然后执行
```bash
sudo update-initramfs -u
```

重启电脑
```bash
reboot
```

检查是否禁用成功
```bash
lspci | grep nouveau 
```

如果没有内容 ，说明禁用成功，如果有内容，就重启一下再查看

## 下载安装驱动

下载1080显卡稳定版驱动384.98
```bash
wget https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/NVIDIA-Linux-x86_64-384.98.run
```
设置驱动的权限
```bash
sudo chmod +x NVIDIA-Linux-x86_64-384.98.run
```

使用 ctrl+alt+f1 进如tty1终端模式

关闭 Display Manager
```bash
sudo service lightdm stop
```

使用sudo命令安装驱动
检查显示器的数据线与主机连接方式
若数据线从独立显卡上连接
```bash
sudo ./NVIDIA-Linux-x86_64-384.98.run
```
若数据线从集成显卡上连接 
```bash
sudo ./NVIDIA-Linux-x86_64-384.98.run --no-opengl-files
```


>please read the following LICENSE and then select either "Accept" to license 
>and continue with the installation,or select "Do Not Accept" tp abort the installation
> 
> **Accept**
> 
>The distribution-provided pre-install script failed! Are you sure you want to continue?
> 
> **Continue installation**
> 
>Would you like to run the nvidia-xconfig utility to automatically update 
>your X configuration file so that the NVIDIA X driver will be used when 
>you restart X ? Any pre-existing X configuration file will be backed up.
> 
> **No**
> 


重启电脑，然后检查驱动是否安装成功
```bash
reboot
```

使用Nvidia 系统管理接口程序查看驱动安装情况
```bash
nvidia-smi
```
如果有gpu的信息输出说明驱动安装成功，请注意查看Driver Version 
![http://yicun.vmaxx.tech:3000/mingzhe_pan/sunergy_multiplatform/src/master/docs/screenshot/smi.png](https://github.com/VMaxxInc/Sunergy_multiPlatform/blob/master/docs/screenshot/smi.png)

