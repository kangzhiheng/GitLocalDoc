# Lab532服务器环境(Ubuntu18.04LTS)须知
Author | kangzhiheng
:-: | :-:
E-mail | kangzhiheng@sjtu.edu.cn
### Contents
* [安装SSH和FileZilla](#安装ssh和filezilla)
  * [SSH](#ssh)
    * [SSH安装](#ssh安装)
    * [SSH登录](#ssh登录)
    * [修改密码](#修改密码)
  * [FileZilla](#filezilla)
    * [安装FileZilla](#安装filezilla)
    * [登录服务器](#登录服务器)
    * [文件传输](#文件传输)
    * [总结](#总结)
* [服务器环境检查](#服务器环境检查)
* [安装tensorflow的GPU版本](#安装tensorflow的gpu版本)
  * [硬件支持](#硬件支持)
  * [系统要求](#系统要求)
  * [软件需求](#软件需求)
  * [安装tensorflow-gpu](#安装tensorflow-gpu)
    * [virtualenv安装tensorflow-gpu](#virtualenv安装tensorflow-gpu)
    * [Anaconda安装tensorflow-gpu](#anaconda安装tensorflow-gpu)
    * [测试tensorflow-gpu版本的正确性](#测试tensorflow-gpu版本的正确性)
* [问题汇总](#问题汇总)
* [在服务器上利用GPU跑程序的简化步骤](#在服务器上利用gpu跑程序的简化步骤)

## 安装SSH和FileZilla

- **SSH**是用来**登录**远程服务器的安全协议；
- **FileZilla**是一个免费开源的FTP软件，支持SSH文件传输协议(SFTP)(可进行本地计算机与远程服务器之间的**文件传输**)

SSH和FileZilla都安装在**本地计算机系统**中

### SSH

> [SSH](https://en.wikipedia.org/wiki/Secure_Shell)(Secure Shell)由 IETF 的网络小组（Network Working Group）所制定；SSH 为建立在应用层基础上的安全协议。SSH 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。

### SSH安装 

在**本地计算机系统**中，`UNIX`、`Window`以及`Mac`系统都可运行SSH,安装方法如下：

 - **_CentOS 7_**

需要普通管理员权限(sudo)，用快捷键`ctrl`+`alt`+`t`打开终端Terminal窗口，运行：

```python
sudo yum install openssh-server
```

然后回车,输入`y`，即可安装完成。

- **_Windows10_**

下载[openssh for windows](https://www.mls-software.com/files/setupssh-7.9p1-1.exe)，进行默认安装即可。

- **_Mac_**

由于Mac本身安装了ssh服务，默认情况下不会开机自启，所以打开终端，输入命令启动`sshd服务`：

```bash
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
```

输入Mac密码后即可开启。

### SSH登录

在任一平台上打开终端(Windows上推荐[**Xshell**]( https://www.netsarang.com/zh/xshell-download/ )、[**FinalShell**]( http://www.hostbuf.com/t/988.html )，Linux系统和Mac直接打开终端即可)，输入命令

```bash
ssh username@IPaddress -p PORT
```

其中，
`username`为服务器的用户名
`IPaddress`为服务器的IP地址
`PORT`为服务器端口号，默认为22，已禁止。已设置实验室专用端口，请联系管理员

输入密码后即可进入自己账户的主目录（普通用户，无root权限）

### 修改密码

登录到服务器之后，修改原始密码保证账户安全，直接输入

```bash
passwd
```

`username`为您在服务器上的用户名，根据提示修改新的密码。

至此，您已经安全登录到服务器上。

### FileZilla

> FileZilla 客户端是一个快速可靠的、跨平台的FTP,FTPS和SFTP客户端。具有图形用户界面(GUI)和很多有用的特性，比如支持FTP, FTP并支持SSL/TLS (FTPS)协议，支持SSH文件传输协议(SFTP)等。

![FileZilla](https://github.com/kangzhiheng/GitLocalDoc/blob/master/img/FileZilla_Client.png)

**FileZilla**可以进行本地文件与服务器文件的相互传输，具体操作如下：

**FileZilla**可以进行本地文件与服务器文件的相互传输，具体操作如下：

### 安装FileZilla

以windows 10为例，下载[FileZilla](https://download.filezilla-project.org/client/FileZilla_3.38.1_win64-setup_bundled.exe)，选择安装位置，正常安装即可。

### 登录服务器

打开FileZilla软件，

* 1. 点击"**文件**"——"**站点管理器**"

* 2. 点击"**新站点**"，在"**我的站点**"下重命名，此处设为“Lab532”

* 3. 继停留在"**站点管理器**"界面，在"**常规**"选项卡中，设置如下：

  * 主机：**IPaddress**
  * 端口：**22**或者**8888**
  * 协议：**SFTP - SSH File transfer Protocol**
  * 登录类型：
    - 1）若选"**正常**"
      - 用户名：username
      - 密码：password
    - 2）若选"**询问密码**"，只能填写用户名：username，然后点击右下角的"**连接**"，再输入密码。

* 4. 之后一直ok即可。

### 文件传输  

以window 10为例，利用FileZilla登录到服务器后，可以看到左边是**本地站点**，右边是**远程站点**(服务器上的目录)，如下图所示，直接将本地文件`test.py`用鼠标拖拽到服务器上的指定目录即可（**上传过程**），这个过程是可逆的（**下载过程**），非常方便。

![FileZilla_file](https://github.com/kangzhiheng/GitLocalDoc/blob/master/img/FileZilla_file.png)

### 总结

利用**FileZilla**软件配合**SSH**协议，即可完成文件传输及程序运行等必要工作。


## 服务器环境检查

登录到服务器后，检查是否可以检测到GPU1080信息(管理员已全局安装)，打开终端，输入命令

```bash
nvidia-smi
```

若输出以下信息，即证明显卡驱动已安装。

![nvidia](https://github.com/kangzhiheng/GitLocalDoc/blob/master/img/nvidia.png)

若有错误提示，请及时联系管理员**adoredee**(kangzhiheng@sjtu.edu.cn)

**实验室GPU信息如下：**

|      规格      |        核心参数        |
| :------------: | :--------------------: |
|    显卡核心    | NVIDIA GeForce GTX1080 |
|    显存频率    |        10010MHz        |
|    显存大小    |           8G           |
|    显存类型    |         GDDR5X         |
|    显存位宽    |         256位          |
|    总线类型    |     PCI-E 3.0 x 16     |
| 数字最高分辨率 |      7680 x 4320       |
|      SLI       |          支持          |
|   散热器类型   |          风冷          |
|    额定电源    |          500W          |
|  供电外接需求  |        8pin x 1        |
| OpneGL支持版本 |       OpenGL 4.5       |

## 安装tensorflow的GPU版本

配置TensorFlow GPU环境需要各种库和包支持，为了简化和避免库之间的冲突，建议使用[virtualenv](https://tensorflow.google.cn/install/pip)、[pip](https://tensorflow.google.cn/install/pip)或者[Docker](https://tensorflow.google.cn/install/docker)安装，本篇教程介绍[virtualenv](https://tensorflow.google.cn/install/pip)和[pip](https://tensorflow.google.cn/install/pip)两种方法安装`tensorflow-gpu-1.6.0`版本。

### 硬件支持

* 具有CUDA®计算能力3.5或更高版本的NVIDIA®GPU卡,请参阅支持[CUDA的GPU卡列表](https://developer.nvidia.com/cuda-gpus)。

### 系统要求

* CentOS 7.x 更高版本(64-bit)
* macOS 10.12.6 (Sierra)更高版本(64-bit)(*不支持 tensorflow GPU版本*)
* Windows 7或更高版本 (64-bit)(只支持Python3)
* Raspbian 9.0或更高版本

### 软件需求

根据[官网](https://tensorflow.google.cn/install/gpu) 以及我个人经验，**_Tensorflow1.6.0的GPU版本_** 要求**在系统上安装以下NVIDIA®软件**

- [NVIDIA® GPU驱动](https://www.nvidia.com/drivers) —— CUDA 9.0要求NVIDIA驱动384.x或者更高版本
- [CUDA® Toolkit](https://developer.nvidia.com/cuda-zone) —— TensorFlow1.6.0支持CUDA 9.0.
- [cuDNN](https://developer.nvidia.com/cudnn) —— 大于等于cuDNN 7.0，这里选择cuDNN 7.0.5


在服务器上管理员已经提前安装好了NVIDIA驱动、CUDA及cuDNN，具体信息如下：

|      所需环境       | 版本信息  |    备注    |
| :-----------------: | :-------: | :--------: |
|     NVIDIA驱动      |  390.77   |  *已安装*  |
|        CUDA         |    9.0    |  *已安装*  |
|        cuDNN        |   7.0.5   |  *已安装*  |
|       gcc/g++       |   4.8.5   |  *已降级*  |
| Tensorflow(GPU版本) | 建议`1.6` | **未安装** |

### 安装tensorflow-gpu

下面将介绍两种方法安装tensorflow-gpu：

1. [virtualenv](https://tensorflow.google.cn/install/pip)
2. [pip](https://tensorflow.google.cn/install/pip)

### virtualenv安装tensorflow-gpu

* **_安装Python包管理工具——pip_**

在终端利用SSH登录到服务器系统(ubuntu18.04LTS)，由于**普通用户**无法使用`sudo`，故不能直接使用`sudo apt-get install virtualenv`安装virtualenv虚拟环境，故使用以下方法，**先安装pip**，再安装**virtualenv**，在终端中运行

```bash
wget https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py --user
```

**注意**:参数`--user`一定要记得使用，否则会提示权限不足。

安装完成后会在家目录下生成 .local 文件夹，里面有bin文件夹和lib文件夹，加入环境变量

```bash
export PATH=~/.local/bin:$PATH
```

此时已在非root用户环境中安装好通用的Python包管理工具`pip`，故利用pip安装**virtualenv**

```bash
pip3 install -U virtualenv --user
```

* **_创建虚拟环境——tensorflow-gpu_**

在终端继续运行

```bash
virtualenv --system-site-packages -p python3 ~/virtual-envs/tensorflow-gpu
```

即在 **~/virtual-envs** 文件夹里创建了名为 **_tensorflow-gpu_** 的虚拟环境，再输入以下命令**激活环境**

```bash
$ source ~/virtual-envs/tensorflow-gpu/bin/activate
```

此时终端会变成如下模样

```bash
(tensorflow-gpu) username@devices:~$
```

即为正常。

上述命令解释

|       name       |      意义      |
| :--------------: | :------------: |
|     username     |     用户名     |
|     devices      |     主机名     |
|        ~         | 当前用户主目录 |
|        $         |    普通用户    |
| (tensorflow-gpu) |  系统指定环境  |

>为什么要激活环境？
>
>因为独立的环境利于管理，不激活不会对系统环境造成不必要的影响，若虚拟环境遇到了无法期望的错误，即可删除环境并重新创建即可，不需要重装系统；而且virtualenv可用于创建独立的Python环境，多个Python相互独立，互不影响，不同应用可以使用不同的版本，环境内的包升级不影响其他应用。

若退出当前环境，有命令:

```bash
deactivate
```

* **_安装tensorflow-gpu 1.6.0_**

在**激活**的环境中输入

```bash
pip install tensorflow-gpu==1.6.0
```

安装过程中，有`yes`输入 **_yes_** ,等待tensorflow 1.6.0的GPU版本安装完成。

若想安装**CPU版本**，无需CUDA和cuDNN，激活虚拟环境后，输入`pip install tensorflow==1.6.0`即可。

是否正确安装，请参照章节 **_测试tensorflow-gpu版本的正确性_** 里的方法验证。

### Anaconda安装tensorflow-gpu

* **_安装conda_**

管理员已经在各位用户的主目录里备份了文件夹`backup`，里面有***Anaconda***安装包，在终端利用SSH登录到服务器系统(CentOS 7.x)，运行

```bash
cd ~/backup
ls
```

查看是否有*Anaconda3-5.2.0-Linux-x86_64.sh*文件，这是 **_python3.6_** 版本的Anaconda安装包，继续运行

```bash
bash Anaconda3-5.2.0-Linux-x86_64.sh
```

按`Enter`键继续，出现以下的界面

![Anaconda_1](https://github.com/kangzhiheng/GitLocalDoc/blob/master/img/Anaconda_1.png)
此时按一下`space`(空格)键，有如下提示，在`[no] >>>` 后面输入`yes`，如下所示：

```bash
Do you accept the license terms? [yes|no]
[no] >>> yes
```
紧接着有提示
```bash
Anaconda3 will now be installed into this location:
/home/test/anaconda3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below

[/home/test/anaconda3] >>>
```
按下`Enter`键即可开始安装，安装完成后，提示如下
```bash
installation finished.
Do you wish the installer to prepend the Anaconda3 install location
to PATH in your /home/test/.bashrc ? [yes|no]
[no] >>> yes
```
在`[no] >>>` 后面输入`yes`后出现Anaconda安装成功的消息，此时出现*是否安装Microsoft VSCode*的消息，输入`no`即可。

python3.6版本的Anaconda安装完成，此时在终端输入`conda`，会提示`conda:未找到命令`，不要慌，反手输入
```bash
source ~/.bashrc
```
刷新环境，再输入`conda`即有消息提示，表明conda安装成功。

* **_创建虚拟环境——tensorflow-gpu_**
在终端输入
```bash
conda create -n tensorflow-gpu pip python=3.6
```
在 **~/anaconda/env** 文件夹下创建名为 **_tensorflow-gpu_** 的虚拟环境

激活环境
```bash
source activate tensorflow-gpu
```
若要退出当前环境，输入
```bash
source deactivate tensorflow-gpu
```
* **_安装tensorflow-gpu 1.6.0_**

在激活的环境中输入
```bash
pip install tensorflow-gpu==1.6.0
```
是否正确安装，请参照章节 **_测试tensorflow-gpu版本的正确性_** 里的方法验证。

### 测试tensorflow-gpu版本的正确性

在激活的环境中，输入`python`，进入到python3.6环境中，依次输入命令
```python
>>> import tensorflow as tf
>>> tf.test.is_gpu_available()
```
若输出类似的内容，且最后一行为`True`，则表示tensorflow-gpu 1.6 版本安装成功！
```python
2018-11-10 01:32:45.539907: I tensorflow/core/platform/cpu_feature_guard.cc:140] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 FMA
2018-11-10 01:32:46.425789: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1212] Found device 0 with properties:
name: GeForce GTX 1080 major: 6 minor: 1 memoryClockRate(GHz): 1.7715
pciBusID: 0000:04:00.0
totalMemory: 7.93GiB freeMemory: 7.81GiB
2018-11-10 01:32:46.425831: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1312] Adding visible gpu devices: 0
2018-11-10 01:32:46.794084: I tensorflow/core/common_runtime/gpu/gpu_device.cc:993] Creating TensorFlow device (/device:GPU:0 with 7545 MB memory) -> physical GPU (device: 0, name: GeForce GTX 1080, pci bus id: 0000:04:00.0, compute capability: 6.1)
True
```
查看tensorflow的版本的命令为
```python
>>> import tensorflow as tf
>>> print(tf.__version__)
```
注意，`__`为两个`_`组合而成。

## 问题汇总

#### 情况1
**错误**——激活环境后，导入`import tensorflow as tf`包后，出现错误
> ImportError: libcublas.so.9.0: cannot open shared object file: No such file or directory

**原因**——cuda版本问题，cuda动态链接库没有与系统共享，可能是cuda安装过程中，环境变量`LD_LIBRARY_PATH`未能配置正确

**解决**——`libcublas.so.9.0`文件在`/usr/local/cuda/lib64`路径下，将其动态链接库为系统共享即可，针对普通管理员用户，在终端运行
```python
sudo ldconfig /usr/local/cuda/lib64
```
问题就会被解决。

（未完待续，随时更新）

## 在服务器上利用GPU跑程序的简化步骤
1. 利用FileZilla软件登录远程服务器上传所需程序文件
2. 在终端利用SSH登录远程服务器
```bash
ssh username@IPaddress -p 22      # 登录服务器
source activate tensorflow-gpu    # 激活tensorflow-gpu环境
cd xxx/xxx                        # 程序文件所在路径
ls                                # 查看文件
python abc.py                     # 运行程序
```


- [x] 1. 实验室服务器用户名清单
- [x] 2. 安装ssh和FileZilla
- [x] 3. 服务器环境检查
- [x] 4. 安装tensorflow的GPU版本
- [x] 5. 在服务器上利用GPU跑程序的简化步骤
