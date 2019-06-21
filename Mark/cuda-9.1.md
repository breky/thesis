### 操作系统及配置

操作系统： ubuntu 18.04.02 LTS

显卡：GeForce 940MX

![os](https://i.loli.net/2019/06/21/5d0c3ad7328ca59163.png)

**PS: 那啥，不要关注硬件，重点是记录安装过程。**

### 安装英伟达驱动
1. 查看驱动程序

   终端输入：

   ```shell
   ubuntu-drivers devices
   ```

   显示结果：

   ```shell
   == /sys/devices/pci0000:00/0000:00:1c.0/0000:01:00.0 ==
   modalias : pci:v000010DEd0000134Bsv00001D72sd00001602bc03sc02i00
   vendor   : NVIDIA Corporation
   model    : GM108M [GeForce 940MX]
   driver   : nvidia-driver-390 - distro non-free recommended
   driver   : xserver-xorg-video-nouveau - distro free builtin
   ```

   选择闭源的驱动（non-free recommended）。

2. 安装驱动

   终端输入：

   ```shell
   sudo ubuntun-drivers autoinstall
   ```

### 安装 cuda 和 cudnn 

1. 版本选择

   1. cuda_9.1.85_387.26_linux
   2. cuda_9.1.85.1_linux
   3. cuda_9.1.85.2_linux
   4. cuda_9.1.85.2_linux

   是 runfile 文件

   ![first](https://i.loli.net/2019/06/21/5d0c45d1e4d6f89942.png)

   ![second](https://i.loli.net/2019/06/21/5d0c45d1350a693129.png)

   

   [Download CUDA 9.1](https://developer.nvidia.com/cuda-91-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1604&target_type=runfilelocal)

   安装的 cuDNN 版本是 v7.0.5 [Dec 11, 2017], for CUDA 9.1 ( cuDNN v7.0.5 Library for Linux)

   ![cuDNN](https://i.loli.net/2019/06/21/5d0c48ea0c24174269.png)

   下载需要注册

   [Download cuDNN v7.0.5 [Dec 11, 2017], for CUDA 9.1](https://developer.nvidia.com/rdp/cudnn-archive)

2. 安装

   1. gcc 和 g++ 降级

      ```shell
      sudo apt-get install gcc-4.8  # 安装 gcc 4.8 版本
      sudo apt-get install g++-4.8  # 安装 g++ 4.8 版本
      cd /usr/bin/  # 进入 /usr/bin 目录下 
      sudo mv gcc gcc.bak # 备份原 gcc 软链
      sudo ln -s gcc-4.8 gcc # 将 gcc 链接到 gcc-4.8
      sudo mv g++ g++.bak # 同上
      sudo ln -s g++-4.8 g++ 
      ```

   2. 安装 CUDA

      ```shell
      # cd 到下载目录，执行以下命令
      sudo sh cuda_9.1.85_387.26_linux.run  #  安装过程中会询问是否安装显卡驱动，选择否。之前已经安装过了。
      sudo cuda_9.1.85.1_linux.run
      sudo cuda_9.1.85.2_linux.run
      sudo cuda_9.1.85.2_linux.run
      ```

      安装完成后，在 ~/.bashrc 文件中添加两个环境变量

      ```shell
      sudo vim ~/.bashrc
      export PATH=/usr/local/cuda-9.1/bin${PATH:+:$PATH}}  # 多半个右大括号
      export LD_LIBRARY_PATH=/usr/local/cuda-9.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}} 
      ```

   3. 安装 cuDNN

      解压下载的 cuDNN 文件，进入该目录下（~/Download/cuda，解压后的文件名是 duca）

      ```shell
      cd ~/Download/cuda
      sudo cp include/cudnn.h /usr/local/cuda/include
      sudo cp lib64/libcudnn* /usr/local/cuda/lib64
      sudo chmod a+r /usr/local/cuda/include/cudnn.h 
      sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
      ```

   4. 测式

      输入命令

      ```shell
      sudo cp /usr/local/cuda/samples /usr/local/cuda/samples.bak  # 备份 samples 文件夹
      cd /usr/local/cuda/samples/1_Utilities/deviceQuery
      sudo make
      ./deviceQuery
      ```

      输出如下结果，即为通过。

      ![third](https://i.loli.net/2019/06/21/5d0c87cd8a1d264874.png)

   5. 安装 TensorFlow 

      新建个虚拟环境，在虚拟环境中测试安装。

      ```python
      pip install --upgrade tensorflow-gpu 
      ```

      我安装的日期是：2019 年 06 月 21 日，最新的 tensorflow-gpu 版本是 1.14.0 版本，使用的是清华源。

      ![forth](https://i.loli.net/2019/06/21/5d0c8a266eb5b15774.png)


### 报错

```python
ImportError: libcublas.so.9.0: cannot open shared object file: No such file or directory
```

提示说需要 libculas.so.9.0 文件，这是 CUDA 9.0 需要的文件，升级 tensorflow-gpu。

我顺序安装了 1.9.0 到 1.12.0，最后用 pip install --upgrade tensorflow-gpu 命令安装

### 参考文献

1. [Ubuntu 18.04安装GPU +CUDA+cuDNN 以及其版本选择](https://www.linuxidc.com/Linux/2019-02/157168.htm)
2. [Ubuntu 16.04下安装CUDA8.0+Caffe+TensorFlow-GPU+Pycharm过程（Simple）](https://www.linuxidc.com/Linux/2017-11/148629.htm)