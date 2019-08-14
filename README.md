# Ubuntu 16.04 Deep Learning Environment Setup
## __0. 查看可安装驱动列表 - Update and Upgrade__
	
    $ sudo apt-get update
    $ sudo apt-get upgrade
    $ sudo apt-cache search nvidia*

<div align="right">
  <img src="https://github.com/chenyeheng/Ubuntu_16.04_Deep_Learning_Environment_Setup/blob/master/img/driver.png" width="920">
</div>

#### (Kernel Version 4.10.09)
#### Install Version: NVIDIA-Linux-x86_64-390.87.run
#### Download Nvidia Drivers: [[Nvidia Link]](https://www.geforce.com/drivers/results/137276)




## __1. 卸载所有原驱动 - uninstall all original drivers for safety__

    $ sudo apt-get purge nvidia*


## __2. 禁用nouveau - Disabling Nouvea__

#### • 安装vim指令 
    sudo apt-get install vim

#### • 新建-blacklist-nouveau.conf 输⼊指令:(Create New Text) 

    $ sudo vim /etc/modprobe.d/blacklist-nouveau.conf

#### • 往文件中写⼊-input (edit:input 'i'    save and exit vim: 'Esc' + ':wq'):
    blacklist nouveau

#### 重新生成 kernel initramfs

    sudo update-initramfs –u

#### 禁 Ubuntu自带开源驱动nouveau，写入后 After above 重启系统 - Disabling Nouveau
    
    $ sudo reboot 

#### 在终端执行行命令:(Terminal) 
    
    $ lsmod | grep nouveau

#### 查看nouveau模块是否被加载，若无输出，则执行下一步 - If no output go to the next step.


## __3. 安装Nvidia驱动 - Install the Drivers__

    Ctrl + Alt + F1-( Enter virtual consoles )进入tty1命令行界面 
    Ctrl + Alt + F7-( Return back to GUI )回到桌面系统界面

#### 禁⽤X服务 - Kill your current X server session by typing

    $ sudo service lightdm stop

#### 给驱动run文件赋予执行权限(根据版本执行 “___” check version)

    $ sudo chmod a+x NVIDIA-Linux-x86_64-390.87.run
    $ sudo ./NVIDIA-Linux-x86_64-390.87.run -no-opengl-files -no-x-check -no-nouveau-check

#### -重点-(登录界面循环问题)
__-no-opengl-files 只安装驱动文件，不安装OpenGL文件 -no-x-check 安装驱动时不检查X服务 -no-nouveau-check 安装驱动时不检查nouveau模块__
  
#### -安装驱动时-
    
    “Would you like to run the nvidia-xconfig utility to automatically update your X configuration file...”
    Choose No，安装完 After above 
    $sudo reboot

## __4. Nvidia驱动安装完成 - Check Driver was successfully installed__

    $ nvidia-smi

![gpu_setting](https://github.com/chenyeheng/Ubuntu_16.04_Deep_Learning_Environment_Setup/blob/master/img/gpu_setting.png)

## __5. 安装CUDA - CUDA Toolkit 9.0 Downloads__
#### Download Installer for Linux Ubuntu 16.04 x86_64
#### Download CUDA: cuda_9.0.176_384.81_linux.run [[CUDA Link]](https://developer.nvidia.com/cuda-toolkit-archive)

<div align="left">
  <img src="https://github.com/chenyeheng/Ubuntu_16.04_Deep_Learning_Environment_Setup/blob/master/img/cuda9.0.png" width="680">
</div>
<div align="left">
  <img src="https://github.com/chenyeheng/Ubuntu_16.04_Deep_Learning_Environment_Setup/blob/master/img/cuda9.0_1.png" width="640"><br><br>
</div>

### Install CUDA

    $ sudo ./cuda_9.0.176_384.81_linux.run --no-opengl-libs  (or   sudo sh ./cuda_9.0.176_384.81_linux.run --no-opengl-libs)
    ...
    accept #同意安装
    n #不安装Driver，因为已安装Nvidia驱动-already installed Nvidia
    y #安装CUDA Toolkit install
    <Enter> #安装到默认目录
    y #创建安装目录的软链接
    n #不复制Samples，因为在安装目录下有/samples
##### 打开.bashrc
sudo gedit ~/.bashrc

##### A. 打开.bashrc 在末行加⼊以下命令 - Add following lines to .bashrc
    
    export PATH="/usr/local/cuda/bin:$PATH"
    export LD_LIBRARY_PATH=“/usr/local/cuda/ lib64:$LD_LIBRARY_PATH”

##### 执行指令更新 .bashrc 文件 - Reload .bashrc with 
    
    $ source .bashrc

##### B. 安装及路径测试，查看CUDA版本 CUDA Sample Testing:

    $ nvcc -V

#### 编译并测试设备 deviceQuery: 
    
    $ cd /usr/local/cuda-9.0/samples/1_Utilities/deviceQuery
    $ sudo make
    $ ./deviceQuery

#### 编译并测试带宽 bandwidthTest:
    
    $ cd ../bandwidthTest
    $ sudo make
    $ ./bandwidthTest
    
#### 如果两个测试的结果都是 *Result = PASS CUDA* 安装成功 - Install successed 
     
     $ cd /usr/local/cuda-9.0/samples/1_Utilities/deviceQuery
     $ sudo make
     $ ./deviceQuery
     $ cd ../bandwidthTest
     $ sudo make
     $ ./bandwidthTest
 
## 6. 安装cuDNN - Download cuDNN (.solitairetheme8)
#### Download Version: cuDNN v7.1.4 (May 16, 2018), for CUDA 9.0 [[cuDNN Link]](https://developer.nvidia.com/rdp/cudnn-archive)
![cudnn](https://github.com/chenyeheng/Ubuntu_16.04_Deep_Learning_Environment_Setup/blob/master/img/cudnn.png)

##### 解压后的 cudnn-9.0-linux-x64-v7.5.0.56.tgz ⽂文件cuda，执行以下指令安装:-Install cudnn

    $ cp  cudnn-9.0-linux-x64-v7.5.0.56.solitairetheme8 cudnn-9.0-linux-x64-v7.5.0.56.tgz
    $ tar -zxvf cudnn-9.0-linux-x64-v7.5.0.56.tgz
    $ cd cuda 
    $ sudo cp lib64/lib* /usr/local/cuda/lib64/
    $ sudo cp include/cudnn.h /usr/local/cuda/include/

##### 然后更新网络连接:-Updata Network Connection

    $ cd /usr/local/cuda/lib64/ 
    $ sudo chmod +r libcudnn.so.7.5.0
    $ sudo ln -sf libcudnn.so.7.5.0 libcudnn.so.7
    $ sudo ln -sf libcudnn.so.7 libcudnn.so 
    $ sudo ldconfig


## 安装cudnn (.dev)  three .deb files

##### step 1:

sudo dpkg -i libcudnn7_7.1.4.18-1+cuda8.0_amd64.deb

sudo dpkg -i libcudnn7-dev_7.1.4.18-1+cuda8.0_amd64.deb

sudo dpkg -i libcudnn7-doc_7.1.4.18-1+cuda8.0_amd64.deb

##### step 2 (test): cudnn 版本
cat /usr/include/x86_64-linux-gnu/cudnn_v7.h | grep CUDNN_MAJOR -A 2


## __7. 安装Tensorflow-gpu - Install Tensorflow-gpu__

     $ pip install --user tensorflow-gpu

![tensorflow-gpu](https://github.com/chenyeheng/Ubuntu_16.04_Deep_Learning_Environment_Setup/blob/master/img/tensorflow-gpu.png)
## 8. 测试 - Testing
![tensorflow](https://github.com/chenyeheng/Ubuntu_16.04_Deep_Learning_Environment_Setup/blob/master/img/tensorflow.png)

## 9. 常见配置问题 - Problem
### 登⼊界⾯死循环问题: Login loop problem
#### 1. 进入文本界面: CTRL+ALT+F1
#### 2. Uninstall any previous drivers:
    
    $ sudo apt-get remove nvidia-*
    $ sudo apt-get autoremove       

#### 3. Uninstall the drivers from the .run file:

    $ sudo nvidia-uninstall

#### 4. 此时，重启 - Reboot -> login normally
#### 5. 驱动重新安装 - Driver reinstall

### 卸载Cuda - uninstall Cuda:
    
    $ cd /usr/local/cuda/bin 
    $ sudo ./uninstall_cuda_7.5.pl
    
### 卸载Cudnn - uninstall Cudnn:

    $ sudo rm -rf /usr/local/cuda/include/cudnn.h sudo rm -rf /usr/
    $ sudo rm -rf /usr/local/cuda/lib64/libcudnn
    
### 调整屏幕分辨率: change the display resolution


#### 添加 /etc/X11/xorg.conf 文件，将此模式保存为默认分辨率。 
    
    $ sudo gedit /etc/X11/xorg.conf

#### 粘贴以下内容: - copy below -> paste to xorg.conf
    
    Section "Monitor"
    Identifier "Configured Monitor"
    Modeline "1920x1080_60.00" 173.00 1920 2048 2248 2576 1080
    1083 1088 1120 -hsync +vsync
    Option "PreferredMode" "1920x1080_60.00"
    EndSection
    Section "Screen"
    Identifier "Default Screen"
    Monitor "Configured Monitor"
    Device "Configured Video Device"
    EndSection
    Section "Device"
    Identifier "Configured Video Device"
    EndSection

# Note

按照指导参照以下方法 https://github.com/yehengchen/Ubuntu-16.04-Deep-Learning-Environment-Setup
