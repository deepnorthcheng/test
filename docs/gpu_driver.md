# GPU Driver

## View the kernel version
```bash
uname -r 
```
4.10.0-42-generic 
Version number should be less than 4.13.0 before it can be used

Turn off kernel updates
```bash
sudo apt-mark hold linux-image-extra-4.10.0-28-generic
sudo apt-mark hold linux-image-4.10.0-28-generic
```

System Settings - > Software and Updates - > Updates


Modify form as below
![](https://github.com/DeepNorthAI/Sunergy_multiPlatform/tree/master/docs/screenshot/system_config.png)

## Disable the nouveau

Open with sudo privileges

```bash
sudo gedit /etc/modprobe.d/blacklist-nouveau.conf 
```

Add the following at the end of the file
```bash
blacklist nouveau  
options nouveau modset=0
```



Save exit
Execute
```bash
sudo update-initramfs -u
```

Reboot
```bash
reboot
```


Check for successful disablement

```bash
lspci | grep nouveau 
```

If there is no content, the disablement is successful. If there is content, restart it and review it again

## Download Nvidia Driver



Download GTX 1080 graphics stable driver 384.98

```bash
wget https://s3-us-west-2.amazonaws.com/vmaxx0/pyEvent_release/NVIDIA-Linux-x86_64-384.98.run
```

Setting Driven Permissions

```bash
sudo chmod +x NVIDIA-Linux-x86_64-384.98.run
```

Use ctrl+alt+f1 access tty1 Backstage mode


Close Display Manager
```bash
sudo service lightdm stop
```

Install drivers with sudo commands

Check the connection mode between the data line of the display and the host computer

If the data line is connected from an independent graphics card
```bash
sudo ./NVIDIA-Linux-x86_64-384.98.run
```
If the data line is connected from the integrated graphics card

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


Restart the computer and check the driver installed successfully
```bash
reboot
```

Use Nvidia System Management Interface Program to View Driver Installation
```bash
nvidia-smi
```
If the information output of the GPU indicates that the driver has been installed successfully, please check Driver Version
![http://yicun.vmaxx.tech:3000/mingzhe_pan/sunergy_multiplatform/src/master/docs/screenshot/smi.png](https://github.com/DeepNorthAI/Sunergy_multiPlatform/tree/master/docs/screenshot/smi.png)

