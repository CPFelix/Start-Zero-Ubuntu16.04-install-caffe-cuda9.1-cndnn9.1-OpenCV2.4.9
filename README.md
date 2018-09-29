# Start-Zero-Ubuntu16.04-install-caffe-cuda9.1-cndnn9.1-OpenCV2.4.9
install caffe in ubuntu

安装步骤（从零开始）

1.Ubuntu16.04安装
    
    Ubuntu系统及U盘启动工具下载：链接：http://pan.baidu.com/s/1c74mXg密码：fii4
    
    制作U盘启动工具及安装系统参考：http://jingyan.baidu.com/article/eb9f7b6d8536a8869364e813.html
   
   
   Att: 
        1.制作U盘启动工具时注意备份U盘数据，制作过程会格式化 
         
        2.安装Ubuntu系统时候启动BIOS设置按F11或F12（我的服务器是F11）
         
        3.联网的话选择“安装第三方软件”，不联网就不需要了
               
        4.安装好之后重启时，要在电脑黑屏时候拔掉U盘，至于不拔可能会还从U盘启动

2.搜狗输入法安装

安装步骤参考：http://jingyan.baidu.com/article/ad310e80ae6d971849f49ed3.html

如果安装失败请结合参考这篇博客：
     
     http://blog.csdn.net/u013894834/article/details/60357071

3. Caffe 

安装
 
3.1安装NVIDIA显卡驱动

官网地址：http://www.nvidia.cn/Download/index.aspx?lang=cn 

点击搜索可以看到合适的驱动：
  
.deb安装过程官方也给出了代码，如上：

dpkg -invidia-diag-driver-local-repo-ubuntu1404-384.66_1.0-1_amd64.deb

sudo apt-get update

sudo apt-get install cuda-drivers

sudo reboot

Att:这里重启后会出现输入密码进入不到桌面循环输入密码的情况，因为这个我上次重装了好几次，在这里也希望大家遇到问题不要只会用重装去解决，不仅仅麻烦，还不利于你去了解和解决问题。这里的问题应该是刚刚安装的英伟达显卡驱动和英特尔驱动产生了冲突，所以需要先用INTEL驱动进入桌面，然后再启用NVIDIA驱动。注意我们在安装CAFFE过程中使用的都应该是NVIDIA驱动。

sudo service lightdm stop

sudo prime-select intel

sudo service lightdm restart

然后输入密码就可以进入桌面了，进入桌面记得切换显卡到NVIDIA：

sudo prime-select nvidia

如果下载的显卡驱动不是.deb文件而是.run，则安装方式不太一样，卸载方式也不一样。

1、 打开终端，先删除旧的驱动：

sudo apt-get purge nvidia*

2、禁用自带的nouveau nvidia驱动

创建一个文件通过命令 

sudo vim /etc/modprobe.d/blacklist-nouveau.conf

并添加如下内容：

blacklist nouveau

options nouveau modeset=0

再更新一下

sudo update-initramfs –u

修改后需要重启系统。

确认下Nouveau是已经被你干掉，使用命令： 

lsmod| grep nouveau

sudo servicelightdm stop

然后切换到tty1控制台：Ctrl+Alt+F1即可 

4接下来就是最关键的一步了：

sudo  ./NVIDIA.run

开始安装，安装过程比较快，根据提示选择即可

最后安装完毕后，重新启动图形界面：

sudo service  lightdm start

然后Ctrl+Alt+F7进入图形界面；如果安装后驱动程序工作不正常，使用下面的命令进行卸载：

sudo sh   ~/NVIDIA-Linux-x86_64-367.44.run   --uninstall

也可以直接在Ubuntu更新中自动安装。

安装完成之后输入以下指令进行验证：

sudo nvidia-smi

若列出了GPU的信息列表则表示显卡驱动安装成功。

3.2安装Cuda9.1

你可以去英伟达官网下载https://developer.nvidia.com/cuda-downloads

下载好后拷贝到Downloads这个目录，并用终端（ctrl+alt+t）在此目录输入代码：

sudo dpkg -i cuda-repo-ubuntu1604-9-1-local_9.1.85-1_amd64.deb   ---------等待完成

sudo apt-get update                                                             ---------等待完成
  
sudo apt-get install cuda                                                      --------等待完成

设置环境变量（必须）

加在/etc/bash.bashrc或者/etc/profile的文件末尾，我选择的是前一种

sudo gedit /etc/bash.bashrc

在末尾处添加（看自己的系统是64还是32）

a.    64位

export PATH=/usr/local/cuda-9.1/bin:$PATH  

export LD_LIBRARY_PATH=/usr/local/cuda-9.1/lib64:$LD_LIBRARY_PATH

b.   32位

export PATH=/usr/local/cuda-9.1/bin:$PATH  

export LD_LIBRARY_PATH=/usr/local/cuda-9.1/lib:$LD_LIBRARY_PATH

然后source一下：

source /etc/bash.bashrc

接下来设置动态链接用到的路径：

sudo gedit /etc/ld.so.conf.d/cuda.conf

这是个空白文档，添加：

/usr/local/cuda-9.1/lib64  

保存后使其立即生效，键入下边命令：

sudo ldconfig

CUDA Toolkit 版本检查：

nvcc –V

显示出来应该是9.1版本。

测试CUDA的sammples：

cuda-install-samples-9.1.sh ~

cd ~/NVIDIA_CUDA-9.1_Samples

make

需要一段时间，

cd~/NVIDIA_CUDA-9.1_Samples/bin/x86_64/linux/release

sudo ./deviceQuery

出现PASS则安装成功！

这里有可能报错，no CUDA-capable devices are present, 这有可能意味着 /dev/nvidia*文件不见了或者是权限不够 。解决办法：

setenforce 0 

bandwidthTest 

之后关机重启继续执行sudo ./deviceQuery



3.3安装MKL

MKL下载：链接：http://pan.baidu.com/s/1qYuQxDa 密码：kepq

下载好后拷贝到“主文件夹”下，即/home/Downloads这个目录下，鼠标右键选择“提取到此处”，并用终端（ctrl+alt+t）在此目录输入代码：

sudo chmod a+xparallel_studio_xe_2016 -R      ------（加权限）等待完成

cd  parallel_studio_xe_2016                         ------（进入目录）

sudo sh install_GUI.sh                                  ------（GUI安装）

系统打开安装界面：界面出现welcome，依次点击next-->next-->next-->Iaccept-->next-->输入激活码（进入网址https://registrationcenter.intel.com/en/forms/?licensetype=2&productid=2486，全选后accept，输入邮箱，此邮箱必须是教育机构的邮箱，@后面是edu的邮箱，输入后全选accept，之后会出现一个下载页面，点击download，中间会有序列号，要得就是这个激活码）-->next-->no
 Idon'twant...-->next-->next-->install-->next-->finish。

安装成功!

3.4mkl和cuda的环境设置

(ctrl+alt+t)打开终端：

sudo gedit /etc/ld.so.conf.d/intel_mkl.conf

出现编辑界面，输入：

/opt/intel/lib/intel64

/opt/intel/mkl/lib/intel64

保存退出，再次编辑，终端输入：

sudo gedit /etc/ld.so.conf.d/cuda.conf

出现编辑界面，输入：

/usr/local/cuda/lib64

/lib

保存退出，最后终端输入：

sudo ldconfig -v

链接完成！



3.5安装opencv

这个尽量不要手动安装，Github上有人已经写好了完整的安装脚本：https://github.com/jayrambhia/Install-OpenCV，下载该脚本，进入Ubuntu/2.4目录,给所有
shell脚本加上可执行权限：

sudo chmod +x *.sh

然后安装2.4.9版本：

sudo ./opencv2_4_9.sh

脚本会自动安装依赖项，下载安装包，编译并安装OpenCV。



3.6 安装依赖项

安装google-glog ：

将glog‐0.3.3.tar.gz这个文件拷贝到主文件夹下，点击鼠标右键，选择提取到此处，解压后终端输入：

cd /home/Downloads/glog-0.3.3

./configure

make

sudo make install

安装成功！

安装其他依赖项：

sudo apt-get install -ylibprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-devlibhdf5-serial-dev

libgflags-dev libgoogle-glog-dev liblmdb-devprotobuf-compiler protobuf-c-compiler protobuf-compiler

安装python所需依赖项：

sudo apt-get install -y python-numpypython-scipy python-matplotlib python-sklearn python-skimage python-h5pypython-protobuf python-
leveldb python-networkx python-nosepython-pandaspython-gflags Cython ipython


3.7. 配置cudnn9.1

首先去官网(https://developer.nvidia.com/rdp/cudnn-download)下载cuDNN,可能需要注册一个账号才能下载。

下载解压，进入解压之后cuda的include目录：

sudo cp cudnn.h  /usr/local/cuda/include/   

再将cd进入lib64目录下的动态文件进行复制和链接：

sudo cp lib*  /usr/local/cuda/lib64/    #复制动态链接库

cd /usr/local/cuda/lib64/

sudo rm -rf libcudnn.so libcudnn.so.7    #删除原有动态文件

sudo ln -s libcudnn.so.7.0.5 libcudnn.so.7  #生成软衔接（注意这里要和自己下载的cudnn版本对应，可以在/usr/local/cuda/lib64下查看自己libcudnn的
版本）

sudo ln -s libcudnn.so.7 libcudnn.so     #生成软链接



3.8 配置caffe-master

如果使用最新的CUDA和CUDNN那么caffe也需要去GitHub上下载最新版本支持，要不可能出现编译错误。 

将caffe-master.zip这个文件拷贝到主文件夹下，即/home/Downloads文件夹下，点击鼠标右键，选择提取到此处，解压后终端输入：

cd /home/Downloads/caffe-master

cp  ./Makefile.config.example   ./Makefile.config

在caffe-sds里打开刚刚新建的Makefile.config文件，做如下修改（把原有的注释掉，如果没有这一项就粘贴进去）：

1. 启用CUDNN,去掉"#"

USE_CUDNN := 1


2. 配置一些引用文件(增加部分主要是解决新版本下,HDF5的路径问题)如果出现libcudnn

.so.1.1的问题，就是这里链接路径不对。

INCLUDE_DIRS:= $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial

LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib/usr/lib/x86_64-linux-gnu/hdf5/serial

3. 启用Intel Parallel Studio XE 2016

BLAS := mkl

4. 配置路径,实现caffe对Python和Matlab接口的支持

PYTHON_LIB :=/usr/local/lib

5. 启用OpenCV 2.4.9,去掉"#"

OPENCV_VERSION =2.4.9

6. 启用WITH_PYTHON_LAYER := 1,去掉"#"

到此修改结束，保存退出。

然后编译caffe-master，终端输入：

cd /home/Downloads/caffe-master

make all -j16                 -------（"‐j16"是使用CPU的多核进行编译,可以极大地加速编译的速度）

make test -j16

make runtest -j16



等待编译完成，完成后终端输入：

make pycaffe -j16           -------（编译Python用到的caffe文件）

配置caffe-master成功！



3.9 Matlab安装

Matlab2014A下载地址:(链接：http://pan.baidu.com/s/1hqRQh6k 密码：syd7)（由于该软件为商业软件,安装学习,并确保不使用做商业目的,下载24小时删
除......）

在主文件夹下(我的是Downloads)新建文件夹Matlab，选择刚下载的matlab文件（Mathworks.Matlab.R2014a.Unix.iso）单击右键，选择使用磁盘映像挂载器打开,进入文件夹,拷贝全部文件至home/Downloads/Matlab文件夹

crack激活文件下载：链接：http://pan.baidu.com/s/1pKTiixl密码：7wwi

下载后在主文件夹下终端输入：

cd home/Downloads/Matlab/java/jar

sudo rm -rf install.jar

sudo cp /home/Downloads/Crack/install.jar home/Downloads/Matlab/java/jar

再次进入主文件夹目录的终端输入：

chmod a+x Matlab -R

cd Matlab

sudo ./install

出现安装界面：选择不需要internet链接-->下一步-->是-->下一步-->我已有...-->12345-67890-12345-67890-->下一步-->下一步-->下一步-->下一步-->安装（等待）-->下一步-->下一步-->不使用internet...-->下一步-->输入许可证路径：/home/Downloads/Crack/license_405329_R2014a.lic-->下一步-->完成
  安装完成后拷贝 Crack/Linux文件夹中的libmwservices.so至 /usr/local/MATLAB/R2014a/bin/glnxa64，即在终端输入：

sudo rm -rf/usr/local/MATLAB/R2014a/bin/glnxa64/libmwservices.so

sudo cp /home/Downloads/Crack/Linux/libwmservices.so/usr/local/MATLAB/R2014a/bin/glnxa64

到此matlab安装成功，测试打开matlab，终端输入：

cd /usr/local/MATLAB/R2014a/bin

./matlab      ------（若需要权限则输入：sudo ./matlab）

如果出现matlab运行界面，则说明安装成功!

如果需要链接caffe，则需要降级gcc和g++版本（不然后面编译matcaffe时出错），即在终端输入：

sudo apt-get install -y gcc-4.7

sudo apt-get install -y g++-4.7

cd /usr/bin

sudo rm gcc

sudo ln -s gcc-4.7 gcc

sudo rm g++

sudo ln -s g++-4.7 g++

降级链接完成!

重新编译caffe-sds，终端输入：

cd /home/Downloads/caffe-master

make clean           ------（重新编译）

打开Makefile.config这个文件修改其中一项：

MATLAB_DIR := /usr/local/MATLAB/R2014a         ------（去掉前面#）

保存退出，然后终端输入：

cd /home/Downloads/caffe-master

make all -j16

make test -j16

make runtest -j16

编译Matlab用到的caffe文件,即终端输入：

cd /home/Downloads/caffe-sds

make matcaffe -j16          -------(若需要权限则输入sudo make matcaffe -j16)

等待编译结果，到此matlab安装编译成功!
 

---------------------

本文来自 CPFelix 的CSDN 博客 ，全文地址请点击：https://blog.csdn.net/CPFelix/article/details/79086580?utm_source=copy
