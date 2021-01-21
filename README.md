# 服务器（CentOS 7.7）管理、深度学习环境搭建、配置及使用

![](https://gitee.com/kangzhiheng/ImageHosting/raw/master/Img/adoredee.png)

Author | kangzhiheng
:-: | :-:
E-mail | kangzhiheng@sjtu.edu.cn



### 谨以此文献给在交大的那段时光~



本文大约27320字，根据需求选择阅读，本文链接：[https://github.com/kangzhiheng/GitLocalDoc](https://github.com/kangzhiheng/GitLocalDoc)



本文主要介绍了自己使用服务器进行管理和科研的一点心得。

本文主要介绍如何搭建和配置**深度学习环境**、**Matlab环境**，普通用户可以自由切换多版本CUDA、cuDNN版本，自由组合创建不同版本的Tensorflow、PyTorch等深度学习环境。

注意：标题右上角带有"*****"标记的，意为这一部分内容需要**管理员**操作。



Table of Contents
=================

* [安装计划](#安装计划)
* [第一章：服务器情况简介](#第一章服务器情况简介)
   * [查看系统硬件设备信息](#查看系统硬件设备信息)
   * [查看系统CPU](#查看系统cpu)
   * [查看内存](#查看内存)
   * [磁盘信息](#磁盘信息)
   * [查看服务器系统信息](#查看服务器系统信息)
   * [查看网卡](#查看网卡)
   * [进程](#进程)
   * [资源统计](#资源统计)
   * [系统常用指令集合](#系统常用指令集合)
      * [cpu.sh — CPU统计信息指令](#cpush--cpu统计信息指令)
      * [disk.sh — 磁盘相关统计指令](#disksh--磁盘相关统计指令)
      * [net.sh  — 网络信息统计指令](#netsh---网络信息统计指令)
      * [system.sh — 系统信息相关指令](#systemsh--系统信息相关指令)
      * [process.sh — 进程统计相关指令](#processsh--进程统计相关指令)
   * [服务器存在的问题](#服务器存在的问题)
* [第二章：深度学习必备基础环境安装](#第二章深度学习必备基础环境安装)
   * [显卡、NVIDIA驱动、CUDA、cuDNN和Tensorflow、Pytorch之间的对应关系](#显卡nvidia驱动cudacudnn和tensorflowpytorch之间的对应关系)
      * [GTX 1080信息](#gtx-1080信息)
      * [Tensorflow建议<a href="https://www.tensorflow.org/install/source#linux" rel="nofollow">配置</a>](#tensorflow建议配置)
      * [PyTorch建议<a href="https://pytorch.org/get-started/previous-versions/" rel="nofollow">配置</a>](#pytorch建议配置)
      * [NVIDIA驱动选型](#nvidia驱动选型)
   * [NVIDIA驱动安装（CentOS、Ubuntu）*](#nvidia驱动安装centosubuntu)
      * [关闭桌面服务](#关闭桌面服务)
      * [系统检查](#系统检查)
      * [禁用第三方开源驱动nouveau](#禁用第三方开源驱动nouveau)
      * [卸载原有驱动](#卸载原有驱动)
      * [NVIDIA官方驱动安装](#nvidia官方驱动安装)
      * [查看当前NVIDIA驱动版本](#查看当前nvidia驱动版本)
      * [开启桌面服务](#开启桌面服务)
   * [CUDA安装*](#cuda安装)
      * [CUDA文件下载](#cuda文件下载)
         * [下载<a href="https://developer.nvidia.com/cuda-90-download-archive" rel="nofollow">CUDA 9.0</a>](#下载cuda-90)
         * [下载<a href="https://developer.nvidia.com/cuda-91-download-archive-new" rel="nofollow">CUDA 9.1</a>](#下载cuda-91)
         * [下载<a href="https://developer.nvidia.com/cuda-92-download-archive" rel="nofollow">CUDA 9.2</a>](#下载cuda-92)
         * [下载<a href="https://developer.nvidia.com/cuda-10.0-download-archive" rel="nofollow">CUDA 10.0</a>](#下载cuda-100)
         * [下载<a href="https://developer.nvidia.com/cuda-10.1-download-archive-base" rel="nofollow">CUDA 10.1 update2</a>](#下载cuda-101-update2)
         * [下载<a href="https://developer.nvidia.com/cuda-downloads?target_os=Linux&amp;target_arch=x86_64&amp;target_distro=CentOS&amp;target_version=7&amp;target_type=runfilelocal" rel="nofollow">CUDA 10.2</a>](#下载cuda-102)
      * [安装CUDA](#安装cuda)
         * [CUDA 10.0及其之前的版本的安装方法](#cuda-100及其之前的版本的安装方法)
         * [CUDA 10.1及其之后的版本的安装方法](#cuda-101及其之后的版本的安装方法)
      * [卸载CUDA](#卸载cuda)
      * [查看当前CUDA版本的符号链接](#查看当前cuda版本的符号链接)
      * [CUDA环境变量配置](#cuda环境变量配置)
      * [验证CUDA安装](#验证cuda安装)
      * [查看CUDA当前版本](#查看cuda当前版本)
   * [cuDNN安装*](#cudnn安装)
      * [cuDNN文件下载](#cudnn文件下载)
      * [cuDNN文件解压](#cudnn文件解压)
      * [cuDNN配置](#cudnn配置)
      * [查看cuDNN版本](#查看cudnn版本)
   * [Anaconda3安装](#anaconda3安装)
      * [安装Anaconda3](#安装anaconda3)
      * [配置Anaconda国内开源镜像](#配置anaconda国内开源镜像)
      * [卸载Anaconda3](#卸载anaconda3)
      * [conda常用命令](#conda常用命令)
         * [关于环境](#关于环境)
         * [包管理](#包管理)
   * [pip安装](#pip安装)
      * [安装](#安装)
      * [更换国内镜像源](#更换国内镜像源)
      * [常用命令](#常用命令)
   * [OpenCV安装](#opencv安装)
      * [Opencv for Python](#opencv-for-python)
         * [1. FFMPEG安装](#1-ffmpeg安装)
         * [3. gtk3](#3-gtk3)
         * [4. v4l（Video for Linux）](#4-v4lvideo-for-linux)
         * [5. gstreamer](#5-gstreamer)
   * [GCC多版本切换*](#gcc多版本切换)
      * [添加centos-release-scl包](#添加centos-release-scl包)
      * [启用RHSCL库](#启用rhscl库)
      * [安装GCC](#安装gcc)
         * [GCC 4.9.2](#gcc-492)
         * [GCC 5.3.1](#gcc-531)
         * [GCC 6.3.1](#gcc-631)
         * [GCC 7.3.1](#gcc-731)
         * [GCC 8.3.1](#gcc-831)
      * [临时生效](#临时生效)
      * [全局生效](#全局生效)
   * [MatLab R2020a安装*](#matlab-r2020a安装)
* [第三章：深度学习环境搭建](#第三章深度学习环境搭建)
   * [SSH远程登录和文件传输](#ssh远程登录和文件传输)
      * [SSH安装](#ssh安装)
      * [SSH登录](#ssh登录)
      * [修改账户密码](#修改账户密码)
      * [FileZilla在Windows和Linux下互传文件](#filezilla在windows和linux下互传文件)
      * [FileZilla连接服务器](#filezilla连接服务器)
      * [文件传输](#文件传输)
      * [总结](#总结)
   * [CUDA、cuDNN多版本切换之：修改符号链接和覆盖cuDNN文件*](#cudacudnn多版本切换之修改符号链接和覆盖cudnn文件)
      * [CUDA切换](#cuda切换)
      * [cuDNN切换](#cudnn切换)
      * [总结](#总结-1)
   * [（推荐）CUDA、cuDNN多版本切换之：Anaconda下安装不同版本的CUDA、cuDNN](#推荐cudacudnn多版本切换之anaconda下安装不同版本的cudacudnn)
      * [conda创建虚拟环境](#conda创建虚拟环境)
      * [示例：安装Tensorflow-GPU 1.6（CUDA 9和cuDNN 7）](#示例安装tensorflow-gpu-16cuda-9和cudnn-7)
      * [测试tensorflow-gpu版本的正确性](#测试tensorflow-gpu版本的正确性)
   * [Anaconda下安装Tensorflow的GPU各个版本（从1.6到2.1）](#anaconda下安装tensorflow的gpu各个版本从16到21)
      * [Tensorflow 1.6.0](#tensorflow-160)
      * [Tensorflow 1.11.0](#tensorflow-1110)
      * [Tensorflow 2.1.0](#tensorflow-210)
   * [Anaconda下安装PyTorch的GPU各个版本（从0.4到1.4）](#anaconda下安装pytorch的gpu各个版本从04到14)
      * [PyTorch 0.4.1](#pytorch-041)
      * [PyTorch 1.1.0](#pytorch-110)
      * [PyTorch 1.4](#pytorch-14)
      * [总结](#总结-2)
   * [NVim/Vim简单使用](#nvimvim简单使用)
      * [什么是Vim？](#什么是vim)
      * [Vim哲学](#vim哲学)
      * [升级支持python3的Vim8*](#升级支持python3的vim8)
      * [Vim8 —&gt; NeoVim](#vim8--neovim)
      * [心理准备](#心理准备)
      * [配置文件](#配置文件)
      * [几种模式](#几种模式)
      * [常用命令](#常用命令-1)
   * [Tmux简单使用](#tmux简单使用)
      * [简介](#简介)
      * [作用](#作用)
      * [几个概念](#几个概念)
      * [安装](#安装-1)
      * [启动和退出](#启动和退出)
      * [前缀键](#前缀键)
      * [会话管理](#会话管理)
         * [新建会话](#新建会话)
         * [分离会话](#分离会话)
         * [会话查看](#会话查看)
         * [进入会话](#进入会话)
         * [杀死会话](#杀死会话)
         * [切换会话](#切换会话)
         * [重命名会话](#重命名会话)
      * [窗格操作](#窗格操作)
* [第四章：在服务器上利用GPU跑程序的简化步骤](#第四章在服务器上利用gpu跑程序的简化步骤)
* [第五章：服务器管理*](#第五章服务器管理)
   * [添加/删除账户](#添加删除账户)
      * [添加用户](#添加用户)
      * [删除用户](#删除用户)
   * [用户组](#用户组)
      * [查看用户的UID和GID](#查看用户的uid和gid)
      * [修改用户的用户组](#修改用户的用户组)
      * [查看某一用户组下的所有成员](#查看某一用户组下的所有成员)
      * [修改用户组名称](#修改用户组名称)
   * [将普通用户赋予管理员权限](#将普通用户赋予管理员权限)
   * [修改登录界面显示](#修改登录界面显示)
      * [SJTU](#sjtu)
      * [Lab532](#lab532)
      * [佛祖保佑](#佛祖保佑)
      * [佛祖瘫痪](#佛祖瘫痪)
      * [神兽草泥马](#神兽草泥马)
      * [兔子](#兔子)
      * [顶](#顶)
      * [妹子](#妹子)
      * [神兽小龙](#神兽小龙)
      * [电脑](#电脑)
      * [书](#书)
      * [键盘](#键盘)
      * [剪刀手](#剪刀手)
      * [龙图腾](#龙图腾)
* [第六章：问题汇总](#第六章问题汇总)
   * [1. 禁止使用sudo yum update](#1-禁止使用sudo-yum-update)
   * [2. ImportError: libcublas.so.9.0](#2-importerror-libcublasso90)
   * [3. libcublas.so.9.0: cannot open shared object file](#3-libcublasso90-cannot-open-shared-object-file)
   * [4. 安装PyTorch时中断安装](#4-安装pytorch时中断安装)
* [参考](#参考)

## 安装计划

- [ ] 服务器系统
- [x] NVIDIA驱动安装
- [x] CUDA安装：9.0、9.1、9.2、10.0、10.1、10.2
- [x] cuDNN安装：7.0.5、7.4、7.6
- [x] SSH远程登录及文件传输
- [x] Aanconda安装
- [x] OpenCV安装（pip）
- [ ] Opencv源码编译
- [ ] GCC安装：4.8.5、7.3.1
- [ ] MatLab安装
- [x] Anaconda安装
- [x] Tensorflow各个版本的安装
- [x] PyTorch各个版本的安装
- [ ] Torch各个版本的安装
- [ ] Caffe安装
- [ ] 使用 **Environment Module** 管理不同版本的软件

## 第一章：服务器情况简介

### 查看系统硬件设备信息

运行

```bash
sudo dmidecode | more
```

查看服务器硬件设备信息，包括**BIOS信息（BIOS Information）**、**服务器信息（System Information）**、**CPU信息（Processor Information）**、**缓存信息（Cache Information）**、**内存信息（Physical Memory Array）**、**电源信息（System Power Supply）**、**内存设备（Memory Device）**等。

服务器型号为[Dell R730](https://www.dell.com/zh-cn/work/shop/povw/poweredge-r730)，有两个显卡插槽，原机无显卡。2018年7月，在服务器上安装了一块GPU，型号为GTX 1080（`lspci | grep -i nvidia`），显存8G。

### 查看系统CPU

**查看CPU的统计信息**

```bash
lscpu
```

输出

```
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                6
On-line CPU(s) list:   0-5
Thread(s) per core:    1
Core(s) per socket:    6
座：                 1
NUMA 节点：         1
厂商 ID：           GenuineIntel
CPU 系列：          6
型号：              79
型号名称：        Intel(R) Xeon(R) CPU E5-2603 v4 @ 1.70GHz
步进：              1
CPU MHz：             1200.085
CPU max MHz:           1700.0000
CPU min MHz:           1200.0000
BogoMIPS：            3400.03
虚拟化：           VT-x
L1d 缓存：          32K
L1i 缓存：          32K
L2 缓存：           256K
L3 缓存：           15360K
NUMA 节点0 CPU：    0-5
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf eagerfpu pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch epb cat_l3 cdp_l3 intel_pt tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm cqm rdt_a rdseed adx smap xsaveopt cqm_llc cqm_occup_llc cqm_mbm_total cqm_mbm_local dtherm arat pln pts
```

查看每个CPU信息，如每个CPU的型号，主频等。

```
cat /proc/cpuinfo
```

运行命令查看CPU型号，为**Intel(R) Xeon(R) CPU E5-2603 v4 @ 1.70GHz**。

### 查看内存

查看内存使用量和交换区使用量

```bash
free -m
```

查看内存详细情况

```bash
cat /proc/meminfo    // 或者vmstat -s
```

输出

```bash
MemTotal:       40779120 kB
MemFree:          323944 kB
MemAvailable:   38162436 kB
Buffers:         1284976 kB
Cached:         36028884 kB
SwapCached:            0 kB
Active:         19560888 kB
Inactive:       18538364 kB
Active(anon):    1214532 kB
Inactive(anon):   270932 kB
Active(file):   18346356 kB
Inactive(file): 18267432 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:             0 kB
SwapFree:              0 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:        785432 kB
Mapped:           139012 kB
Shmem:            700072 kB
Slab:            1861424 kB
SReclaimable:    1663764 kB
SUnreclaim:       197660 kB
KernelStack:        9504 kB
PageTables:        17908 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:    20389560 kB
Committed_AS:    3160860 kB
VmallocTotal:   34359738367 kB
VmallocUsed:      443080 kB
VmallocChunk:   34358900732 kB
HardwareCorrupted:     0 kB
AnonHugePages:     36864 kB
CmaTotal:              0 kB
CmaFree:               0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:    38408912 kB
DirectMap2M:     3305472 kB
DirectMap1G:     2097152 kB
```

原机内存大小为8G，在2018年7月，扩展到40G，实际上不到40G，为38.8G左右。

### 磁盘信息

**查看硬盘和分区分布**

```bash
lsblk
```

输出

```bash
NAME                   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                      8:0    0   5.5T  0 disk 
├─sda1                   8:1    0   476M  0 part /boot
├─sda2                   8:2    0    61M  0 part 
├─sda3                   8:3    0    64M  0 part /boot/efi
├─sda4                   8:4    0   1.4T  0 part /home
├─sda5                   8:5    0   3.6T  0 part 
│ ├─VolGroup0-datavol0 253:2    0     1T  0 lvm  /mnt/vdisk0
│ ├─VolGroup0-datavol1 253:3    0     1T  0 lvm  /mnt/vdisk1
│ ├─VolGroup0-datavol2 253:4    0     1T  0 lvm  /mnt/vdisk2
│ └─VolGroup0-datavol3 253:5    0   540G  0 lvm  /mnt/vdisk3
└─sda6                   8:6    0 474.5G  0 part 
  ├─centos-root        253:0    0   400G  0 lvm  /
  └─centos-swap        253:1    0  74.5G  0 lvm  
sr0                     11:0    1  1024M  0 rom  
loop0                    7:0    0  14.4G  0 loop /mnt/iso
```

可以看到目前服务器上的磁盘结构。**如果重装系统，`/home`（sda4）可以保留，新系统在安装时，不必要再分/home区。直接将旧系统的/home挂载到新系统上即可。**

**目前磁盘已采用LVM技术**。

> LVM是（Logical Volume Manager) 逻辑卷管理的简写，它是Linux环境下对磁盘分区进行管理的一种机制。LVM是建立在硬盘和分区之上的一个逻辑层，屏蔽了底层磁盘布局，便于动态调整磁盘容量来提高磁盘分区管理的灵活性。它可以将一个或多个底层块设备组织成一个逻辑设备的卷组，管理员可以在卷组上随意创建逻辑卷组（logical volumes），并进一步在逻辑卷组上创建文件系统，管理员通过LVM可以方便的调整存储卷组的大小，并且可以对磁盘存储按照组的方式进行命名、管理和分配 。

**查看更详细的信息**

```bash
sudo fdisk -l
```

**查看各分区使用情况**

```bash
df -h 
```

### 查看服务器系统信息

```bash
cat /etc/*release
```

输出

```bash
CentOS Linux release 7.7.1908 (Core)
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

CentOS Linux release 7.7.1908 (Core)
CentOS Linux release 7.7.1908 (Core)
```

系统为CentOS 7.7。

**查看系统主机名**

```bash
hostname
```

### 查看网卡

**查看网卡硬件信息**

```bash
lspci | grep -i 'eth'
```

输出

```
01:00.0 Ethernet controller: Broadcom Inc. and subsidiaries NetXtreme BCM5720 2-port Gigabit Ethernet PCIe
01:00.1 Ethernet controller: Broadcom Inc. and subsidiaries NetXtreme BCM5720 2-port Gigabit Ethernet PCIe
02:00.0 Ethernet controller: Broadcom Inc. and subsidiaries NetXtreme BCM5720 2-port Gigabit Ethernet PCIe
02:00.1 Ethernet controller: Broadcom Inc. and subsidiaries NetXtreme BCM5720 2-port Gigabit Ethernet PCIe

```

**查看系统的所有网络接口**

```bash
ifconfig -a
```

可以看到连接网络的那个端口，假设是em2，可以查看该端口的IPV4、IPV6、子网掩码等信息。如果要详细查看如果某个网络接口的详细信息，比如em2的详细参数和指标

```bash
ethtool em2
```

输出

```bash
Settings for em2:
    Supported ports: [ TP MII ]
    Supported link modes:   10baseT/Half 10baseT/Full 
                            100baseT/Half 100baseT/Full 
                            1000baseT/Half 1000baseT/Full # 支持千兆半双工，全双工模式
    Supported pause frame use: No 
    Supports auto-negotiation: Yes # 支持自适应模式，一般都支持
    Advertised link modes:  10baseT/Half 10baseT/Full 
                            100baseT/Half 100baseT/Full 
                            1000baseT/Half 1000baseT/Full
    Advertised pause frame use: Symmetric
    Advertised auto-negotiation: Yes # 默认使用自适应模式
    Advertised FEC modes: Not reported
    Link partner advertised link modes:  10baseT/Half 10baseT/Full 
                                         100baseT/Half 100baseT/Full 
                                         1000baseT/Full
    Link partner advertised pause frame use: No
	Link partner advertised auto-negotiation: Yes
	Link partner advertised FEC modes: Not reported
    Speed: 1000Mb/s # 现在网卡的速度是1000Mb
    Duplex: Full   # 全双工
    .....
    Link detected: yes    # 表示有网线连接，和路由是通的
```

**查看显示去往192.168.1.102的所有HTTP会话信息**

```bash
sudo tcpdump -ni em1 'dst 192.168.1.102 and tcp and port 8080'
```

**显示来源IP或者目的IP为192.168.1.102的网络通信**

```bash
sudo tcpdump -i eth0 host 192.168.1.102
```

**查看路由器列表**

```bash
route -n
```

**查看所有监听端口**

```bash
netstat -lntp
```

**查看所有已经建立的连接**。

```bash
netstat -antp
```

**查看网络统计信息**

```bash
netstat -s
```

### 进程

**查看系统所有进程**

```bash
ps -ef
```

**显示所有进程的详细信息**

```bash
ps aux
```

**显示某一用户的进程列表**

```bash
 ps -f -u username
```

**实时显示进程状态**

```bash
top
```

按`q`退出

**列出当前的进程，以及它们的树状结构**

```bash
pstree
```

**结束进程**，PID为Process ID

```bash
kill PID
```

**查看系统信号变量**

```bash
kill -l
```

事实上，还有一个比较酷炫的命令是`htop`，安装方法是`sudo yum install htop`，在终端输入：

```bash
htop
```

**遇到顽固的进程，使出必杀技**

```bash
kill -9 PID
```

### 资源统计

**查看当前目录大小**

```bash
du -sh
```

**查看系统运行时间、用户数、负载**

```bash
uptime                 
```

### 系统常用指令集合

#### cpu.sh — CPU统计信息指令

```bash
#/bin/bash

# 限制某个线程的cpu使用率
sudo cpulimit -p pid -l 50
ps -eo %cpu,args | grep -m1 PROCESS | awk '{print $1}'

# 将当前进程按照memory和cpu排序
ps aux --sort=%mem,%cpu

# 按照cpu使用率排序
ps -e -o pcpu,cpu,nice,state,cputime,args --sort pcpu | sed "/^ 0.0 /d"

# 查看当前系统的物理cpu个数
grep "processor" /proc/cpuinfo | wc -l	
grep -c -e '^cpu[0-9]\+' /proc/stat

# 查看当前cpu型号
grep "model name" /proc/cpuinfo

# 查看当前cpu信息
cat /proc/cpuinfo

# 查看当前系统的位数
grep -q '\<lm\>' /proc/cpuinfo && echo 64 bits || echo 32 bits
getconf LONG_BIT | grep '64'
java -version

# 查看当前系统的cpu频率
awk -F": " '/cpu MHz\ */ { print "Processor (or core) running speed is: " $2 }' /proc/cpuinfo ; dmidecode | awk -F": " '/Current Speed/ { print "Processor real speed is: " $2 }'

# 查看每个cpu每个进程的cpu使用率
ps ax -L -o pid,tid,psr,pcpu,args | sort -nr -k4| head -15 | cut -c 1-90

# 查看当前中断
cat /proc/interrupts

# 查看多个处理器的使用率相关信息
mpstat –P ALL 1

# 每个物理CPU中Core的个数：
cat /proc/cpuinfo | grep "cpu cores" | uniq | awk -F: '{print $2}'

# 是否为超线程？
# 如果有两个逻辑CPU具有相同的”core id”，那么超线程是打开的。
# 每个物理CPU中逻辑CPU(可能是core, threads或both)的个数：
cat /proc/cpuinfo | grep "siblings"

# /proc/stat 文件中有一行记录的机器从启动依赖，各个中断序号发生中断的次数。
# 这一行以intr开头，接下来的第一个数字是总的中断数目，之后就是分别的中断数目，从0开始。
cat /proc/stat  | grep intr
```

#### disk.sh — 磁盘相关统计指令

```bash
#/bin/bash

# 按照目录大小排序战士最前面15个目录或者文件
du -xB M --max-depth=2 /var | sort -rn | head -n 15

# 列出当前所有子目录的文件大小
du -h --max-depth=1

# 列出当前文件或者目录最大的10个
du -s * | sort -n | tail

# 按照目录大小从大到小排序
du -b --max-depth 1 | sort -nr | perl -pe 's{([0-9]+)}{sprintf "%.1f%s", 1>=2∗∗30?(1/2**30, "G"): 1>=2∗∗20?(1/2**20, "M"): 1>=2∗∗10?(1/2**10, "K"): ($1, "")}e'

# 列出path这个目录的文件树
du -h /path | sort -h

# 每隔60s监控对应目录的文件大小变化
watch -n60 du /var/log/messages


# 递归删除当前目录下所有子目录中的.svn目录
find . -type d -name '.svn' -print0 | xargs -0 rm -rdf

# 列出当前磁盘的使用情况
df -P | column -t

# 监控磁盘的使用情况
watch -d -n 5 df

# 列出当前inode的使用情况
df -i  <partition>

# 按照每个磁盘使用量从高到低排序
df -h | grep -v ^none | ( read header ; echo "$header" ; sort -rn -k 5)


# 查看物理磁盘的使用情况
df -x tmpfs | grep -vE "(gvfs|procbususb|rootfs)"

# 查看当前所有磁盘的大小和使用量
df -H

# 查看所有分区使用情况
fdisk -l /dev/sda

# 显示系统所有的分区或给定的分区
fdisk -l       
# 显示时，显示的是扇区数不是柱面数           
fdisk -u           
# 显示指定partition的block数       
fdisk -s partition       

# 查看磁盘的读写容量
iostat -m -d /dev/sda1

# 测试磁盘的读写速度
hdparm -t /dev/sda

# 查看某个文件的所有链接
find -L / -samefile /path/to/file -exec ls -ld {} +

# 查看最大的5个文件
find . -type f -exec ls -s {} \; | sort -n -r | head -5

# 查看365天前的文件并删除
find ./ -type f -mtime +365 -exec rm -f {} \;

# 查看大于100M的文件
find . -type f -size +100M
```

#### net.sh  — 网络信息统计指令

```bash
#/bin/bash

# 查看http请求的header
tcpdump -s 1024 -l -A -n host 192.168.9.56
tcpdump -s 1024 -l -A src 192.168.9.56 or dst 192.168.9.56
sudo tcpdump -A -s 1492 dst port 80

# 本地网络中IP地址为192.168.0.5的主机现在与外界通信的情况
sudo tcpdump -i eth0 src host 192.168.0.5

# 查看网卡eth0的http请求的tcp包
tcpdump -i eth0 port http
tcpdump -i eth0 port http or port smtp or port imap or port pop3 -l -A | egrep -i 'pass=|pwd=|log=|login=|user=|username=|pw=|passw=|passwd=|password=|pass:|user:|userna me:|password:|login:|pass |user '

# 查看tcp，upd，icmp非ssh的包
tcpdump -n -v tcp or udp or icmp and not port 22

# 查看http请求的request 包
sudo tcpdump -i eth0 port 80 -w -


# 过滤http响应的get host头信息
sudo tcpdump -i en1 -n -s 0 -w - | grep -a -o -E "GET \/.*|Host\: .*"

# DNS查询请求响应包
sudo tcpdump -i en0 'udp port 53'

# nmap -sP指定使用Ping echo 进行扫描 /24查看目的网络有多少主机处于运行状态
nmap -sP 192.168.0.1
nmap -sP 192.168.0.0/24
nmap -O www.baidu.com

# Netstat主要用于Linux/Unix主机察看自身的网络状况，如开启的端口、在为哪些用户服务以及服务的状态
netstat -a
netstat -nlp

# netcat扫描机器开放的端口
nc -z -v -n 172.31.100.7 21-25

# netcat 命令会连接开放端口21并且打印运行在这个端口上服务的banner信息。
nc -v 172.31.100.7 21

# 输出网络的路由表
route -n

# 显示结果包括服务器已经运行了多长时间，有多少登陆用户和对服务器性能的总体评估（load average）。
uptime

# 监控 eth1 的网卡的流量 
iftop -i eth1 
# 以位元组(bytes 即字节)为单位显示流量(预设是位元 bits): 
iftop -B 
# 直接显示 IP, 不进行DNS 反解: 
iftop -n 
# 直接显示连接埠编号, 不显示服务名称: 
iftop -N 
# 显示某个网段进出封包流量 
iftop -F 192.168.1.0/24 or 192.168.1.0/255.255.255.0 

# 网络流量实时监控工具之nload,查看网卡eth0的流量
nload -n eth0

# 查看各个网卡的流量
nload -h

# IPTraf是一个网络监控工具,功能比nload更强大,可以监控所有的流量,IP流量,按协议分的流量,还可以设置过滤器
iptraf

# 查看网络接口当前配置与接口命名：
ifconfig
# 打开（up）或关闭（down）适配器
ifconfig <网络名> <up|down>
# 为适配器分配IP地址：
ifconfig <网络名> <ip地址>
# 为适配器分配第二个IP地址：
ifconfig <网络名:实例数> <ip地址>

# 显示某个网络适配器的驱动信息，适用于查询软件兼容性的情况：
ethtool -i eth0
# 显示网络数据：
ethtool -S
# 设置适配器连接速度（Mbps）
ethtool speed <10|100|1000>

# wget
wget -S --spider http://osswin.sourceforge.net/ 2>&1 | grep Mod

# 查看mac地址
cat /sys/class/net/*/address

# 查看eth0的ip
ifconfig eth0 | awk '/inet addr/ {split ($2,A,":"); print A[2]}'

# curl 查看页面对应的domail name
curl -s http://en.m.wikipedia.org/wiki/List_of_Internet_top-level_domains | sed -n '/<tr valign="top">/{s/<[^>]*>//g;p}'

# talnet
telnet localhost 6666

# 查看所有的网络接口
awk '{print $1}' /proc/net/dev|grep :|sed "s/:.*//g"

# 查看DNS server版本好
nslookup -q=txt -class=CHAOS version.bind NS.PHX5.NEARLYFREESPEECH.NET
```

#### system.sh — 系统信息相关指令

```bash
#/bin/bash

# 查看当前系统load
uptime

# 查看系统状态和每个进程的系统资源使用状况
top

# 可视化显示CPU的使用状况
htop

# 查看每个CPU的负载信息
mpstat -P ALL 1

# 每隔1秒查看磁盘IO的统计信息
iostat -xkdz 1

# 每隔一秒查看虚拟内存的使用信息
vmstat 1

# 查看内存使用统计信息
free

# 查看网络使用信息
nicstat -z 1

# 类似vmstat的显示优化的工具
dstat 1

# 查看系统活动状态，比如系统分页统计，块设备IO统计等
sar

# 网络连接状态查看
netstat -s

# 进程资源使用信息查看
pidstat 1
pidstat -d 1

# 查看某个进程的系统调用信息 -p后面是进程id，-tttT 进程系统后的系统调用时间
strace -tttT -p 12670
# 统计IO设备输入输出的系统调用信息
strace -c dd if=/dev/zero of=/dev/null bs=512 count=1024k


# tcpdump 查看网络数据包
tcpdump -nr /tmp/out.tcpdump

# 块设备的读写事件信息统计
btrace /dev/sdb 

# iotop查看某个进程的IO操作统计信息
iotop -bod5

# slabtop 查看内核 slab内存分配器的使用信息
slabtop -sc

# 系统参数设置
sysctl -a

# 系统性能指标统计信息
perf stat gzip file1
# 系统cpu活动状态查看
perf record -a -g -F 997 sleep 10
```

#### process.sh — 进程统计相关指令

```bash
## ps查看当前系统执行的线程列表，进行瞬间状态，不是连续状态，连续状态需要使用top名称查看  更多常用参数请使用 man ps查看
ps

## 显示所有进程详细信息
ps aux

## -u 显示某个用户的进程列表
ps -f -u www-data 

## -C 通过名字或者命令搜索进程
ps -C apache2

## --sort  根据进程cpu使用率降序排列，查看前5个进程  -pcpu表示降序  pcpu升序
ps aux --sort=-pcpu | head -5 

## -f 用树结构显示进程的层次关系，父子进程情况下
ps -f --forest -C apache2 

## 显示一个父进程的所有子进程
ps -o pid,uname,comm -C apache2
ps --ppid 2359 

## 显示一个进程的所有线程  -L 参数
ps -p 3150 -L 

##显示进程的执行时间 -o参数
ps -e -o pid,comm,etime 

## 查看系统的线程数
nproc

## watch命令可以用来实时捕捉ps显示进程
watch -n 1 'ps -e -o pid,uname,cmd,pmem,pcpu --sort=-pmem,-pcpu | head -15' 

## jobs 查看后台运行的进程  jobs命令执行的结果，＋表示是一个当前的作业，减号表是是一个当前作业之后的一个作业，jobs -l选项可显示所有任务的PID,jobs的状态可以是running, stopped, Terminated,但是如果任务被终止了（kill），shell 从当前的shell环境已知的列表中删除任务的进程标识；也就是说，jobs命令显示的是当前shell环境中所起的后台正在运行或者被挂起的任务信息
jobs

## 查看后台运营的进程号
jobs -p

## 查看现在被终止或者退出的进程号
jobs -n

## kill命令 终止一个前台进程可以使用Ctrl+C键   kill  通过top或者ps获取进程id号  kill [-s 信号 | -p ] [ -a ] 进程号 ...
## 发送指定的信号到相应进程。不指定型号将发送SIGTERM（15）终止指定进程。 关闭进程号12的进程
kill 12

## 等同于在前台运行PID为123的进程时按下Ctrl+C键
kill -2 123

## 如果任无法终止该程序可用“-KILL” 参数，其发送的信号为SIGKILL(9) ，将强制结束进程  
kill -9 123

## 列出所有信号名称
## HUP    1    终端断线
## INT     2    中断（同 Ctrl + C）
## QUIT    3    退出（同 Ctrl + \）
## TERM   15    终止
## KILL    9    强制终止
## CONT   18    继续（与STOP相反， fg/bg命令）
## STOP    19    暂停（同 Ctrl + Z）
kill -l

## 得到指定信号的数值
kill -l KILL

## 杀死指定用户所有进程
kill -u peidalinux
kill -9 $(ps -ef | grep peidalinux) 

## 将后台中的命令调至前台继续运行  将进程123调至前台执行
fg 123

## 将一个在后台暂停的命令，变成继续执行
bg  123

## 该命令可以在你退出帐户/关闭终端之后继续运行相应的进程。nohup就是不挂起的意思  下面输出被重定向到myout.file文件中
nohup command > myout.file 2>&1 &

## at：计划任务，在特定的时间执行某项工作，在特定的时间执行一次。
## 格式：at HH:MM YYYY-MM-DD //HH（小时）:MM（分钟） YYYY（年）-MM（月份）-DD（日）
## HH[am pm]+D(天) days //HH（小时）[am（上午）pm（下午）]+days（天）
at 12:00（时间） //at命令设定12:00执行一项操作
# at>useradd aaa //在at命令里设定添加用户aaa
# ctrl+d //退出at命令
# tail -f /etc/passwd //查看/etc/passwd文件后十行是否增加了一个用户aaa

## 计划任务设定后，在没有执行之前我们可以用atq命令来查看系统没有执行工作任务。
atq

## 启动计划任务后，如果不想启动设定好的计划任务可以使用atrm命令删除。
atrm 1 //删除计划任务1

## pstree命令：列出当前的进程，以及它们的树状结构  格式：pstree [选项] [pid|user]
pstree

## nice命令：改变程序执行的优先权等级 应用程序优先权值的范围从-20～19，数字越小，优先权就越高。一般情况下，普通应用程序的优先权值（CPU使用权值）都是0，如果让常用程序拥有较高的优先权等级，自然启动和运行速度都会快些。需要注意的是普通用户只能在0～19之间调整应用程序的优先权值，只有超级用户有权调整更高的优先权值（从-20～19）。
nice [-n <优先等级>][--help][--version][命令]
nice -n 5 ls

## sleep命令：使进程暂停执行一段时间
date;sleep 1m;date


## renice命令 renice命令允许用户修改一个正在运行进程的优先权。 利用renice命令可以在命令执行时调整其优先权。
## 其中，参数number与nice命令的number意义相同。（1） 用户只能对自己所有的进程使用renice命令。（2） root用户可以在任何进程上使用renice命令。（3） 只有root用户才能提高进程的优先权
renice -5 -p 5200  #PID为5200的进程nice设为-5 

## pmap命令用于显示一个或多个进程的内存状态。其报告进程的地址空间和内存状态信息 #pmap PID 
pmap 20367

## top 动态查看进程的变化。
top
```

### 服务器存在的问题

1. **电源供电不足**

   目前服务器运行良好，需要注意的是，服务器目前的电源功率是495W（`sudo dmidecode | grep Power`），GPU显卡额定功率是500W，服务器供电不足导致电源灯一闪一闪，为了保险起见，尽快补一块[750W服务器电源](https://item.jd.com/28405788107.html)。



## 第二章：深度学习必备基础环境安装

**NVIDIA驱动、CUDA、cuDNN三部分尽可能在现场安装**，因为涉及到重启，服务器不能远程重启。

另外，本部分所有指令，建议在`bash`环境下运行，使用`zsh`的shell请切换为`bash`，避免不必要的麻烦。

### 显卡、NVIDIA驱动、CUDA、cuDNN和Tensorflow、Pytorch之间的对应关系

不同版本的Tensorflow和PyTorch环境对NVIDIA驱动、CUDA和cuDNN的有着不一样的需求。

系统能否检测到GPU：

```bash
lspci | grep -i nvidia
```

输出

```bash
04:00.0 VGA compatible controller: NVIDIA Corporation GP104 [GeForce GTX 1080] (rev a1)
04:00.1 Audio device: NVIDIA Corporation GP104 High Definition Audio Controller (rev a1)
```

**GTX 1080为Tensorflow、PyTorch等框架提供GPU支持。**

#### GTX 1080信息

**服务器上GTX 1080信息如下：**

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

#### Tensorflow建议[配置](https://www.tensorflow.org/install/source#linux)

数据来源于[官网](https://www.tensorflow.org/install/source#linux)，截止2020年4月11号，`tensorflow-2.1.0`GPU版本需要配合`CUDA 10.1`和`cuDNN 7.6`配合使用。

![Tensorflow版本](https://pic.downk.cc/item/5e9137b1504f4bcb04541d52.png)

#### PyTorch建议[配置](https://pytorch.org/get-started/previous-versions/)

数据来源于[官网](https://pytorch.org/get-started/previous-versions/)，截止2020年4月11号，`PyTorch 1.4`版本需要配合`CUDA 10.1`或`CUDA9.2`配合使用。

![PyTorch](https://pic.downk.cc/item/5e91390d504f4bcb04552f6c.png)

#### NVIDIA驱动选型

在Linux系统下，[NVIDIA驱动型号](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)根据需求来安装，`CUDA10.2`需求`NVIDIA >= 440.33`，所以NVIDIA驱动型号至少是`440.33`。

![CUDA版本与NVIDIA驱动之间的关系](https://pic.downk.cc/item/5e913b69504f4bcb04574a5e.png)

### NVIDIA驱动安装（CentOS、Ubuntu）*

通过前面的分析，目前Tensorflow 最新版本2.1.0需求CUDA 10.1，索性安装CUDA 10.2所需求的最低要求的NVIDIA版本`440.33`。

另外NVIDIA驱动不要轻易更新，在CentOS下，尤其是不到万不得已的地步，千万千万不要运行`sudo yum update`指令！Ubuntu对应的指令是`sudo apt-get update`。

**本小节主要介绍在CentOS 7.7系统下的驱动安装**，顺便介绍在Ubuntu系统的驱动检索及安装方法。

NVIDIA驱动安装完成后，需要到现场重启服务器。

#### 关闭桌面服务

如果在现场安装NVIDIA驱动，首先要按住`Alt + Ctrl + F1`进入终端（F1到F6都行），输入账户密码获取root权限

```bash
sudo su
```

然后关闭桌面模式

```bash
sudo service lightdm stop
```

然后通过`Ctrl + Alt + F7`发现已无法成功返回图形化模式，说明桌面服务已成功关闭，注意此步对接下来的 NVIDIA 显卡驱动安装尤为重要，必需确保桌面服务已关闭。

驱动安装完成后，按住`Alt + Ctrl + F7`可开启桌面模式。

#### 系统检查

查看支持CUDA的GPU列表：https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#system-requirements，根据[官网](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#pre-installation-actions)建议，**安装NVIDIA驱动和CUDA之前需要查看系统是否满足要求**：

1. 验证系统是否具有支持CUDA的GPU

2. 确认系统是受支持的Linux发行版本
3. 验证系统是否有GCC编译环境

4. 验证系统是否安装了正确的内核头文件和开发包

一步一步检测

1. **验证系统是否具有支持CUDA的GPU**

   ```bash
   lspci | grep -i nvidia
   ```

   输出

   ```bash
   04:00.0 VGA compatible controller: NVIDIA Corporation GP104 [GeForce GTX 1080] (rev a1)
   04:00.1 Audio device: NVIDIA Corporation GP104 High Definition Audio Controller (rev a1)
   ```

   服务器上的显卡是GTX 1080，支持CUDA

2. **确认系统是受支持的Linux发行版本**

   CUDA开发工具仅在某些特定的Linux发行版上受支持，运行命令

   ```bash
   uname -m && cat /etc/*release
   ```

   输出信息

   ```bash
   x86_64
   CentOS Linux release 7.7.1908 (Core)
   NAME="CentOS Linux"
   VERSION="7 (Core)"
   ID="centos"
   ID_LIKE="rhel fedora"
   VERSION_ID="7"
   PRETTY_NAME="CentOS Linux 7 (Core)"
   ANSI_COLOR="0;31"
   CPE_NAME="cpe:/o:centos:centos:7"
   HOME_URL="https://www.centos.org/"
   BUG_REPORT_URL="https://bugs.centos.org/"
   
   CENTOS_MANTISBT_PROJECT="CentOS-7"
   CENTOS_MANTISBT_PROJECT_VERSION="7"
   REDHAT_SUPPORT_PRODUCT="centos"
   REDHAT_SUPPORT_PRODUCT_VERSION="7"
   
   CentOS Linux release 7.7.1908 (Core)
   CentOS Linux release 7.7.1908 (Core)
   ```

   `x86_64`表示当前系统是64位系统，运行的Linux发行版是CentOS7.7版本。

3. **验证系统是否有GCC编译环境**

   ```bash
   gcc --version
   ```

   一般情况下都会有。

4. **验证系统是否安装了正确的内核头文件和开发包**

   运行以下命令找到系统正在运行的内核版本：

   ```bash
   uname -r
   ```

   当前运行的内核的头文件和开发包可以通过以下方式安装：

   ```bash
   sudo yum install kernel-devel-$(uname -r) kernel-headers-$(uname -r)
   ```

#### 禁用第三方开源驱动nouveau

&emsp;&emsp;Nouveau是由第三方为NVIDIA显卡开发的一个开源3D驱动，不过没有得到NVIDIA的认可与支持。Nouveau让Linux更容易的应对各种复杂的NVIDIA显卡环境，让用户安装完系统即可进入桌面并且有不错的显示效果，所以，**很多Linux发行版默认集成了Nouveau驱动。**企业版的Linux更是如此，几乎所有支持图形界面的企业Linux发行版都将Nouveau收入其中。

&emsp;&emsp;不过对于个人桌面用户来说，处于成长阶段的Nouveau并不完美，与企业版不一样，个人用户除了想让正常显示图形界面外很多时候还需要一些3D特效，Nouveau多数时候并不能完成，而用户在安装NVIDIA官方私有驱动的时候Nouveau又成为了阻碍，如果不干掉Nouveau，NVIDIA驱动安装时总是报错。

![](https://pic.downk.cc/item/5e93446cc2a9a83be5684112.jpg)

**NVIDIA驱动和nouveau驱动最好二选一**，一般情况下，**深度学习环境配置需禁用nouveau驱动**，查看nouveau驱动信息：

```bash
lsmod | grep nouveau
```

若有输出，需要禁止该nvidia第三方驱动，若无，OK， **Linux哲学之一：没有消息就是最好的消息。** 可忽略该步骤。

**禁用nouveau驱动，需要配置相关文件**

```bash
sudo nvim /etc/modprobe.d/blacklist-nouveau.conf
```

[nvim](https://github.com/neovim/neovim)，即NeoVIm，在生态和功能上比`vim`要领先一些，在CentOS系统下的[nvim的安装](https://github.com/neovim/neovim/wiki/Installing-Neovim#centos-7--rhel-7)命令为：

```bash
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install -y neovim python3-neovim
```

没有安装[nvim](https://github.com/neovim/neovim)可以用`vim`来代替（`sudo yum install vim`）。

文件可能没有任何内容，在打开的文件中，进入Vim的**插入模式**（`i`键），**写入命令：**

```bash
blacklist nouveau  
options nouveau modeset=0
```

按下`Eac`键进入普通模式，输入命令`:wq`保存并退出文件。

更新initramfs映像使禁用 `nouveau` 生效：

```bash
sudo update-initramfs -u
```

输入

```bash
lsmod | grep nouveau
```

若无输出，则表示禁用nouveau生效了。

当然也可以重启系统使禁用 `nouveau` 真正生效，但**重启系统有可能会出现黑屏，原因是禁用了集成显卡，系统没有显卡驱动。**

#### 卸载原有驱动

查看当前系统驱动型号：

```bash
nvidia-smi
```

或者

```
ls /usr/src
```

或者

```bash
cat /proc/driver/nvidia/version
```

或者

```bash
lsmod | grep nvidia
```

总之，可以得知当前NVIDIA版本信息（就算使用了`sudo yum update`），使用上述四条命令之一也可以查到，如果真的真的忘记了，跳过这一步吧……

假设知道了目前的NVIDIA驱动型号，假设为390.77，去[该网站](https://download.nvidia.com/XFree86/Linux-x86_64/)上下载对应的驱动文件

![](https://pic.downk.cc/item/5e9340d4c2a9a83be565cc63.png)

类似于`NVIDIA-Linux-x86_64-390.77.run`这种文件，右键**复制链接**，使用**wget**工具下载

```bash
cd ~    // 回到主目录
wget -c https://download.nvidia.com/XFree86/Linux-x86_64/390.77/NVIDIA-Linux-x86_64-390.77.run
```

然后运行命令进行卸载

```bash
sudo sh NVIDIA-Linux-x86_64-390.77.run --uninstall
```

#### NVIDIA官方驱动安装

**确保已经关闭桌面服务。**

NVIDIA驱动分离线安装和在线安装，离线安装就是在[官网](https://www.nvidia.com/Download/index.aspx?lang=cn)下载对应的驱动，再进行安装，为了方便起见，采用**在线**的方法在**CentOS 7.7**系统进行安装。

**添加 ELRepo 源：**

```bash
sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
sudo rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
```

**查看显卡检测程序：**

```bash
yum info nvidia-detect    
```

如果安装，跳过下一步，否则

```bash
sudo yum install nvidia-detect
```

**检测显卡型号，查看对应的驱动**

```bash
nvidia-detect -v
```

输出

```bash
Probing for supported NVIDIA devices...
[102b:0534] Matrox Electronics Systems Ltd. G200eR2
[10de:1b80] NVIDIA Corporation GP104 [GeForce GTX 1080]
This device requires the current 440.64 NVIDIA driver kmod-nvidia
```

系统告诉我们，显示型号是GTX 1080，对应的NVIDIA驱动型号应为`440.64`，大于`440.33`，满足要求，继续安装，否则去[官网](https://www.nvidia.com/Download/index.aspx?lang=cn)下载对应的驱动手动离线安装。

输入命令进行安装：

```bash
sudo -i    // 获取root权限
yum install kmod-nvidia    // 或者这条命令yum install nvidia-x11-drv
```

安装完成后，或者输出下面的信息，可能要重启系统……

```bash
软件包 nvidia-x11-drv-440.82-1.el7_8.elrepo.x86_64 已安装并且是最新版本
```

重启系统

```bash
sudo reboot
```

安装完成，输入

```bash
nvidia-smi
```

看看显卡信息。

- 如果是**Ubuntu系统**，可使用下列方法进行安装

  ```bash
  sudo apt-get purge nvidia*					// 卸载之前的NVIDIA驱动
  sudo add-apt-repository ppa:graphics-drivers/ppa	// 添加源
  sudo apt-get update
  sudo apt-get install nvidia-440				// 安装NVIDIA-440系列驱动
  ```

  或者

  ```bash
  sudo apt-get update
  sudo ubuntu-drivers devices    // 或者运行apt-cache search nvidia*
  // 出现建议的NVIDIA驱动型号xxx
  sudo apt-get install nvidia-xxx
  ```

**重启服务器**。

#### 查看当前NVIDIA驱动版本

使用命令

```bash
nvidia-smi
```

或者

```bash
cat /proc/driver/nvidia/version
```

安装过程中可能会遇到比较多的问题，**请耐心解决**。

#### 开启桌面服务

NVIDIA驱动安装完成后，可开启桌面服务

```bash
sudo service lightdm stop
```

然后通过`Ctrl + Alt + F7`可返回图形化模式。

### CUDA安装*

在此介绍CUDA-9.0、CUDA-9.1、CUDA-9.2、CUDA-10.0、CUDA-10.1、CUDA-10.2共6种CUDA文件的安装（截止2020年4月11号），基本涵盖了当前流行深度学习框架的需求。

需要注意的是，如果你是个人用户，就是自己在电脑上装了Linux系统进行深度学习环境的设置，可以详细阅读这一小节的内容，这一小节主要介绍的是一般情况下的CUDA配置，因为如果是自己的系统，可以随意更改CUDA的**符号链接**，但是在服务器上，大部分用户都是普通用户，**无root权限**，切换CUDA时很麻烦，不切换CUDA，深度学习环境的配置就比较受限，所以在服务器环境下，**普通用户如何灵活切换CUDA和cuDNN版本呢？有两种方法：**

- 使用[Environment Modules](https://modules.readthedocs.io/en/latest/)来管理CUDA；
- 使用[Anaconda](https://anaconda.org/)来独立管理[CUDA和cuDNN](#（推荐）CUDA、cuDNN多版本切换之：Anaconda下安装不同版本的CUDA、cuDNN)；

我建议采用Anaconda来管理，理由很简单：操作方便上手快。具体请[跳转](#（推荐）CUDA、cuDNN多版本切换之：Anaconda下安装不同版本的CUDA、cuDNN)。下面介绍正常情况下的CUDA和cuDNN安装方法。

**推荐下载.run的CUDA文件格式。**

在`~/NVIDIA/CUDA`目录下依次建立`cuda-9.0`、`cuda-9.1、`、`cuda-9.2`、`cuda-10.0`、`cuda-10.1`、`cuda-10.2`6个目录，目录下是对应CUDA版本的安装文件，文件的后缀名为`.run`，不管是基础版本还是补丁，安装方法都是一模一样的。

**CUDA的安装，建议[关闭桌面服务](#关闭桌面服务)，通过`Alt + Ctrl + F1 `进入终端。**

#### CUDA文件下载

CUDA的官网下载链接为https://developer.nvidia.com/cuda-toolkit-archive，CUDA的下载不需要注册，cuDNN需要。以CUDA9.2下载为例，依次按图红色框框进行选择，需要注意的是图中的**黑色框框**，那个是当前CUDA版本的补丁，也要下载，安装方法与**基础版本一致**。

![cuda9.2](https://pic.downk.cc/item/5e9144da504f4bcb045fe855.png)

正常方法是点击Download下载，下载后还要拖入到服务器种比较麻烦，在这里给出直接将文件下载到服务器的方法。

创建不同CUDA版本的目录

```bash
cd ~
mkdir NVIDIA
cd NVIDIA
mkdir CUDA
cd CUDA
mkdir cuda-9.{0..2}     // 批量建立文件夹 cuda-9.0、cuda-9.1、cuda-9.2
mkdir cuda-10.{0..2}    // 批量建立文件夹 cuda-10.0、cuda-10.1、cuda-10.2
```

然后依次在对应目录下下载各个版本的**CUDA基础安装包及其补丁**（时间截止到2020.04.11）

为了直观起见，使用比较基础的命令

##### 下载[CUDA 9.0](https://developer.nvidia.com/cuda-90-download-archive)

```bash
cd ~/NVIDIA/CUDA/cuda-9.0

// 下载基础cuda9.0安装包
wget -c https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run -O cuda_9.0.176_384.81_linux.run

// 下载cuda9.0的4个补丁
wget -c https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/1/cuda_9.0.176.1_linux-run -O cuda_9.0.176.1_linux.run
wget -c https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/2/cuda_9.0.176.2_linux-run -O cuda_9.0.176.2_linux.run
wget -c https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/3/cuda_9.0.176.3_linux-run -O cuda_9.0.176.3_linux.run
wget -c https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/4/cuda_9.0.176.4_linux-run -O cuda_9.0.176.4_linux.run
```

##### 下载[CUDA 9.1](https://developer.nvidia.com/cuda-91-download-archive-new)

```bash
cd ~/NVIDIA/CUDA/cuda-9.1

// 下载基础cuda9.1安装包
wget -c https://developer.nvidia.com/compute/cuda/9.1/Prod/local_installers/cuda_9.1.85_387.26_linux -O cuda_9.1.85_387.26_linux.run

// 下载cuda9.1的3个补丁
wget -c https://developer.nvidia.com/compute/cuda/9.1/Prod/patches/1/cuda_9.1.85.1_linux -O cuda_9.1.85.1_linux.run
wget -c https://developer.nvidia.com/compute/cuda/9.1/Prod/patches/2/cuda_9.1.85.2_linux -O cuda_9.1.85.2_linux.run
wget -c https://developer.nvidia.com/compute/cuda/9.1/Prod/patches/3/cuda_9.1.85.3_linux -O cuda_9.1.85.3_linux.run
```

##### 下载[CUDA 9.2](https://developer.nvidia.com/cuda-92-download-archive)

```bash
cd ~/NVIDIA/CUDA/cuda-9.2

// 下载基础cuda9.2安装包
wget -c https://developer.nvidia.com/compute/cuda/9.2/Prod2/local_installers/cuda_9.2.148_396.37_linux -O cuda_9.2.148_396.37_linux.run

// 下载cuda9.2的1个补丁
wget -c https://developer.nvidia.com/compute/cuda/9.2/Prod2/patches/1/cuda_9.2.148.1_linux -O cuda_9.2.148.1_linux.run
```

##### 下载[CUDA 10.0](https://developer.nvidia.com/cuda-10.0-download-archive)

```bash
cd ~/NVIDIA/CUDA/cuda-10.0

// 下载基础cuda10.0安装包
wget -c https://developer.nvidia.com/compute/cuda/10.0/Prod/local_installers/cuda_10.0.130_410.48_linux -O cuda_10.0.130_410.48_linux.run

// 下载cuda10.0的1个补丁
wget -c http://developer.download.nvidia.com/compute/cuda/10.0/Prod/patches/1/cuda_10.0.130.1_linux.run
```

##### 下载[CUDA 10.1 update2](https://developer.nvidia.com/cuda-10.1-download-archive-base)

```bash
cd ~/NVIDIA/CUDA/cuda-10.1

// 下载基础cuda10.1安装包，无补丁
wget -c http://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda_10.1.243_418.87.00_linux.run
```

##### 下载[CUDA 10.2](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=CentOS&target_version=7&target_type=runfilelocal)

```bash
cd ~/NVIDIA/CUDA/cuda-10.2

// 下载基础cuda10.2安装包，无补丁
wget -c http://developer.download.nvidia.com/compute/cuda/10.2/Prod/local_installers/cuda_10.2.89_440.33.01_linux.run
```

#### 安装CUDA

以`cuda_9.1.85_387.26_linux.run`文件为例说明怎么安装的，所有的文件都是一样的安装方法（**CUDA 10.1以及后续版本，安装界面有所不一样，请仔细阅读**）。

##### CUDA 10.0及其之前的版本的安装方法

CUDA默认安装位置：`/usr/local`

先建立一个名为**CUDAInstall**的[tmux](https://github.com/tmux/tmux)窗口，这是为了在安装的时候，防止终端出现紧急情况，影响安装，这个习惯可以让你减少不必要的损失，运行

```bash
tmux new -s CUDAInstall     
sudo sh cuda_9.1.85_387.26_linux.run
```

如果遇到文件权限问题，可以赋予CUDA文件可执行权限：

```bash
sudo chmod a+x cuda_9.1.85_387.26_linux.run
```

运行CUDA安装文件后，紧接着在屏幕上出现了很长的说明，按住**空格**键至进度100%，然后按照下面的提示操作即可，需要注意的是，在提示

```bash
Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 387.26?
(y)es/(n)o/(q)uit: 
```

意思是： **安装NVIDIA驱动吗？** 输入`n`，NVIDIA驱动我们已单独安装。

安装过程中每一步的提示如下，请仔细阅读**翻译**，尤其是符号链接那部分：

```bash
-----------------
// 接受之前阅读的说明吗？必须接受啊
Do you accept the previously read EULA?
accept/decline/quit: accept

// 安装NVIDIA驱动吗？NVIDIA驱动我们单独安装
Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 387.26?
(y)es/(n)o/(q)uit: n

// 安装CUDA9.1的工具集吗？安装
Install the CUDA 9.1 Toolkit?
(y)es/(n)o/(q)uit: y

// CUDA的默认路径，按Enter键继续
Enter Toolkit Location
 [ default is /usr/local/cuda-9.1 ]: 
 
// 要为CUDA9.1建立符号链接吗？如果该CUDA是你想默认的，输入y；
// 如果不是但是以后想调用，输入n
// 这个可以在安装之后修改
Do you want to install a symbolic link at /usr/local/cuda?
(y)es/(n)o/(q)uit:y

// CUDA 9.1的示例，可以安装，方便测试
Install the CUDA 9.1 Samples?
(y)es/(n)o/(q)uit: y

// CUDA 9.1的示例位置，默认即可，测试完成后可以删掉，按Enter键表示默认
Enter CUDA Samples Location
 [ default is /home/unix ]: 
```

然后开始安装，终端上会显示

```
Installing the CUDA Toolkit in /usr/local/cuda-9.1 ...
```

表示`CUDA 9.1`会被安装到目录`/usr/local/cuda-9.1`文件夹下，大约1分钟左右，终端显示

```bash
Installing the CUDA Samples in /home/unix ...
Copying samples to /home/unix/NVIDIA_CUDA-9.1_Samples now...
Finished copying samples.

===========
= Summary =
===========

Driver:   Not Selected
Toolkit:  Installed in /usr/local/cuda-9.1
Samples:  Installed in /home/unix

Please make sure that
 -   PATH includes /usr/local/cuda-9.1/bin
 -   LD_LIBRARY_PATH includes /usr/local/cuda-9.1/lib64, or, add /usr/local/cuda-9.1/lib64 to /etc/ld.so.conf and run ldconfig as root

To uninstall the CUDA Toolkit, run the uninstall script in /usr/local/cuda-9.1/bin

Please see CUDA_Installation_Guide_Linux.pdf in /usr/local/cuda-9.1/doc/pdf for detailed information on setting up CUDA.

***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 384.00 is required for CUDA 9.1 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run -silent -driver

Logfile is /tmp/cuda_install_89370.log
```

终端输出的内容非常清楚，包含了这么几个意思：

- 关于NVIDIA驱动：NVIDIA驱动已经安装但是没有被检测出来，重启系统就好了，因为服务器在学校，必须到现场才能重启成功；

- 关于路径：CUDA 9.1被安装在`/usr/local/cuda-9.1`目录下

- 关于卸载CUDA：CUDA的卸载脚本在/usr/local/cuda-9.1/bin里

  ```bash
  cd /usr/local/
  sudo ./cuda-9.1/bin/uninstall_cuda_9.1.pl
  ```

- 安装CUDA的补丁包

大部分CUDA版本都有补丁包，补丁的安装比较容易，以CUDA 9.1的补丁1`cuda_9.1.85.1_linux.run`为例说明

```bash
sudo sh cuda_9.1.85.1_linux.run
```

屏幕上也会出现很长的说明，按住**空格**键至进度100%，出现了一些选项

```
-----------------
Do you accept the previously read EULA?
accept/decline/quit:  accept

Enter CUDA Toolkit installation directory
 [ default is /usr/local/cuda-9.1 ]: 
```

安装位置默认就行，按住Enter后，秒安装

```bash
Installation complete!
Installation directory: /usr/local/cuda-9.1
```

按照该方法，安装CUDA 9.1其它两个补丁包。

至此CUDA基础安装包及其补丁包的方法介绍完了，**按照此方法，依次安装其它CUDA 9.0 ~ CUDA10.0版本的基础安装包及其补丁。**

##### CUDA 10.1及其之后的版本的安装方法

从CUDA 10.1起，安装界面有所不同，比如安装**CUDA 10.1**版本`cuda_10.1.243_418.87.00_linux.run`

```bash
sudo sh cuda_10.1.243_418.87.00_linux.run
```

终端会出现一个框框

![](https://pic.downk.cc/item/5e919104504f4bcb04a646b2.png)

输入**accept**后，出现

![CUDA 10.1](https://pic.downk.cc/item/5e918ea5504f4bcb04a3d678.png)

注意，不要选择**Driver**！**如图所选**，移动**下键**至**Install**处，Enter一下立即安装，出现界面

![](https://pic.downk.cc/item/5e918f1e504f4bcb04a443d9.png)

选择**No**，安装过程中没有什么反应，耐心等待一会儿，终端打印出信息

```bash
===========
= Summary =
===========

Driver:   Not Selected
Toolkit:  Installed in /usr/local/cuda-10.1/
Samples:  Installed in /home/unix/

Please make sure that
 -   PATH includes /usr/local/cuda-10.1/bin
 -   LD_LIBRARY_PATH includes /usr/local/cuda-10.1/lib64, or, add /usr/local/cuda-10.1/lib64 to /etc/ld.so.conf and run ldconfig as root

To uninstall the CUDA Toolkit, run cuda-uninstaller in /usr/local/cuda-10.1/bin

Please see CUDA_Installation_Guide_Linux.pdf in /usr/local/cuda-10.1/doc/pdf for detailed information on setting up CUDA.
***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 418.00 is required for CUDA 10.1 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run --silent --driver

Logfile is /var/log/cuda-installer.log
```

与之前的信息差不多，具体请看之前的描述。CUDA 10.2安装方法与CUDA 10.1类似。

#### 卸载CUDA

- CUDA 10.0及其之前版本

  以CUDA 9.1为例，CUDA 9.1的卸载脚本在/usr/local/cuda-9.1/bin里

  ```bash
  cd /usr/local/
  sudo ./cuda-9.1/bin/uninstall_cuda_9.1.pl
  ```

- CUDA 10.1及其之后的版本

  卸载CUDA 10.1和10.2有所不同，需要运行

  ```bash
  cd /usr/local/
  sudo ./cuda-10.1/bin/cuda-uninstaller
  ```

#### 查看当前CUDA版本的符号链接

查看当前cuda对应的版本（符号链接）

```bash
cd /usr/local
stat cuda
```

输出

```bash
  文件："cuda" -> "/usr/local/cuda-9.0"
  大小：19        	块：0          IO 块：4096   符号链接
设备：fd00h/64768d	Inode：20341519    硬链接：1
权限：(0777/lrwxrwxrwx)  Uid：(    0/    root)   Gid：(    0/    root)
环境：unconfined_u:object_r:usr_t:s0
最近访问：2020-04-10 14:43:19.316455577 +0800
最近更改：2019-09-26 22:20:21.054175738 +0800
最近改动：2019-09-26 22:20:21.054175738 +0800
创建时间：-

```

之前为cuda-9.0建立了**符号链接**，所以cuda指向cuda-9.0，因为系统是根据这个**符号链接**来识别当前cuda版本，所以更改其指向即可切换cuda版本。

#### CUDA环境变量配置

建议在bash环境下运行，运行

```bash
bash
```

进入bash shell。

为了管理方便，管理员可以在文件`/etc/profile`里全局配置CUDA环境变量，如果是个人Linux系统或者普通用户，在文件`~/.bashrc`下配置CUDA环境变量。

- **全局添加CUDA环境变量（管理员操作）**

  这种修改方式使CUDA配置应用到每位普通用户的shell里

  打开全局环境变量

  ```bash
  sudo nvim /etc/profile    // 为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行. 
  ```

  然后在打开的文件里的末尾新增四条命令，具体操作是：按住大写`G`键跳转到文本的最后一行行首，再按住`$`跳转到最后一行的最后一个字符，按住`i`键进入插入模式，使光标右移一个字符，再回车换行后，添加以下变量

  ```bash
  # CUDA
  
  # cuda -> cuda9.1
  export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
  # include -> targets/x86_64-linux/include
  export CPATH=/usr/local/cuda/include:$CPATH 
  # lib64 -> targets/x86_64-linux/lib
  export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/cuda/lib64 
  # lib64 -> targets/x86_64-linux/lib
  # export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH 
  export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
  ```

  更新配置，使配置文件生效：

  ```bash
  source /etc/profile
  ```

- **普通用户添加CUDA环境变量**

  只对当前shell有效

  ```
  nvim ~/.bashrc
  ```

  同样的操作，在文件末尾新增四条命令

  ```bash
  # CUDA
  
  # cuda -> cuda9.1
  export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
  # include -> targets/x86_64-linux/include
  export CPATH=/usr/local/cuda/include:$CPATH 
  # lib64 -> targets/x86_64-linux/lib
  export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/cuda/lib64 
  # lib64 -> targets/x86_64-linux/lib
  # export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH 
  export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
  ```

  更新文件

  ```bash
  source ~/.bashrc
  ```

#### 验证CUDA安装

在安装CUDA时，比如在安装CUDA 9.1时，有这么一句提示

```bash
// CUDA 9.1的示例，可以安装，方便测试
Install the CUDA 9.1 Samples?
(y)es/(n)o/(q)uit: y

// CUDA 9.1的示例位置，默认即可，测试完成后可以删掉，按Enter键表示默认
Enter CUDA Samples Location
 [ default is /home/unix ]: 
```

为了验证CUDA 9.1是否被安装完成，选择安装示例，默认的安装位置是**根目录**，进入根目录

```bash
cd ~
cd NVIDIA_CUDA-9.1_Samples/1_Utilities/deviceQuery    // 选择其中一个示例进行测试
make
./deviceQuery
```

如果成功，会显示`Result = PASS`，否则根据错误提示解决问题。

#### 查看CUDA当前版本

```bash
nvcc --version
```

或者

```bash
cat /usr/local/cuda/version.txt
```

如果是在终端命令行下安装的CUDA，则需要安装成功后开启桌面服务：

```
sudo service lightdm start
```

然后再按通过`Ctrl + Alt + F7`可返回桌面模式。

### cuDNN安装*

首先登录[官网](https://developer.nvidia.com/rdp/cuDNN-archive)进行注册，然后才能选择对应版本的cuDNN进行下载。以cuDNN7.0.5为例进行说明，其它版本与此类似。

按照[官方指导教程](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installlinux)进行cuDNN配置。

#### cuDNN文件下载

首先要确定要下载的cuDNN版本，**仔细看清楚其对应的CUDA版本**，以cuDNN对应的CUDA 9.0为例，推荐下载**cuDNN v7.0.5 Library for Linux**类似的压缩包。

![](https://pic.downk.cc/item/5e9518fcc2a9a83be5720e10.png)

下载完成后，文件为cudnn-9.0-linux-x64-v7.solitairetheme8，这其实是一个压缩包，解压方法与`.tgz`格式的文件一致。建立cuDNN文件夹，

```bash
mkdir ~/NVIDIA/cuDNN
```

使用FileZilla、Xftp等文件传输工具。将其拖入到服务器中里的NVIDIA文件夹下的cuDNN文件夹下。

#### cuDNN文件解压

解压cudnn-9.0-linux-x64-v7.solitairetheme8

```bash
tar -xzvf cudnn-9.0-linux-x64-v7.solitairetheme8
```

解压后的文件都在名为`cuda`的文件夹下，如图

![](https://pic.downk.cc/item/5e958245c2a9a83be5b9b2a0.png)

`cudnn-9.0-linux-x64-v7.solitairetheme8`的文件组成如下

![](https://pic.downk.cc/item/5e958783c2a9a83be5bd2a6a.jpg)

查看`linudnn.so`、`libcudnn.so.7`、`libcudnn.so.7.0.5`的关系

```bash
ll ~/NVIDIA/cuDNN/cuda/lib64
```

输出

![](https://pic.downk.cc/item/5e95ada0c2a9a83be5d76131.png)

说明`linudnn.so`、`libcudnn.so.7`、`libcudnn.so.7.0.5`其实是同一个文件的不同扩展名。

#### cuDNN配置

cuDNN解压文件在`~/NVIDIA/cuDNN/cuda`下：

```bash
# 复制头文件
cd ~/NVIDIA/cuDNN/
sudo cp cuda/include/cudnn.h /usr/local/cuda/include 

# 复制动态链接库
cd ~/NVIDIA/cuDNN/
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
```

配置权限，使所有用户具有可读权限

```bash
sudo chmod a+r /usr/local/cuda/include/cudnn.h
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
```

#### 查看cuDNN版本

```bash
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
```

### Anaconda3安装

####  安装Anaconda3

在[官方repo](https://repo.anaconda.com/archive/)下载***Anaconda***安装包（`.sh`后缀名），如果下载速度过慢，访问[国内镜像源](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)下载，右键复制对应Anaconda包的链接，使用`wget`指令下载，注意下载时注意该Anaconda包是基于Python 3.6还是Python 3.7的，**推荐Python 3.6**，下载*Anaconda3-5.2.0-Linux-x86_64.sh*文件

```bash
cd ~
wget -c https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh
```

*Anaconda3-5.2.0-Linux-x86_64.sh*这是 **_python3.6_** 版本的Anaconda安装包，继续运行

```bash
bash Anaconda3-5.2.0-Linux-x86_64.sh
```

按`Enter`键继续，出现Anaconda版本的一些说明，此时按一下`space`(空格)键，有如下提示，在`[no] >>>` 后面输入`yes`，如下所示：

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

#### 配置Anaconda国内开源镜像

显示有关当前conda安装的信息：

```bash
conda info
```

输出

```bash
     active environment : None
       user config file : /home/adoredee/.condarc
 populated config files : 
          conda version : 4.8.3
    conda-build version : not installed
         python version : 3.7.0.final.0
       virtual packages : __glibc=2.17
       base environment : /home/adoredee/anaconda3  (writable)
           channel URLs : https://repo.anaconda.com/pkgs/main/linux-64
                          https://repo.anaconda.com/pkgs/main/noarch
                          https://repo.anaconda.com/pkgs/r/linux-64
                          https://repo.anaconda.com/pkgs/r/noarch
          package cache : /home/adoredee/anaconda3/pkgs
                          /home/adoredee/.conda/pkgs
       envs directories : /home/adoredee/anaconda3/envs
                          /home/adoredee/.conda/envs
               platform : linux-64
             user-agent : conda/4.8.3 requests/2.23.0 CPython/3.7.0 Linux/3.10.0-957.el7.x86_64 centos/7.7.1908 glibc/2.17
                UID:GID : 1000:1000
             netrc file : None
           offline mode : False
```

可以看到在conda安装好之后，默认的镜像是官方的（**channel URLs**），由于官网的镜像在境外，国内访问较慢，配置国内的镜像能够加快访问的速度。

上海交通大学镜像源：https://mirrors.sjtug.sjtu.edu.cn/anaconda/

清华大学镜像源：https://mirrors.tuna.tsinghua.edu.cn/anaconda/

**创建配置文件：**

```bash
nvim ~/.condarc
```

如果使用[上海交通大学镜像源](https://mirrors.sjtug.sjtu.edu.cn/#/)，将以下配置写入`~/.condarc`：

```bash
channels:
  - defaults
show_channel_urls: true
channel_alias: https://anaconda.mirrors.sjtug.sjtu.edu.cn
default_channels:
  - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/main
  - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/free
  - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/mro
  - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/r
  - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/msys2
custom_channels:
  conda-forge: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/
  bioconda: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/
  menpo: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/
  soumith: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/
  viscid-hub: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/
  atztogo: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/
```

如果使用[清华大学镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)，将以下配置写入`~/.bashrc`：

```bash
channels:
  - defaults
show_channel_urls: true
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

**清除索引缓存**，保证用的是镜像站提供的索引：

```
conda clean -i 
```

#### 卸载Anaconda3

按照[官方卸载指导](https://docs.anaconda.com/anaconda/install/uninstall/)，Anaconda3安装在`~/anaconda3`文件夹下，首先删除目录

```bash
cd ~
rm -rf anaconda3
```

清理`.bashrc`里的Anaconda环境变量

```bash
nvim ~/.bashrc
source ~/.bashrc
```

清理隐藏文件夹

```bash
cd ~
rm .condarc
rm -rf ~/.condarc ~/.conda ~/.continuum 
```

#### conda常用命令

##### 关于环境

**创建一个名为myenv的环境**

```bash
conda create -n myenv pip python=3.6
```

**激活myenv环境**

```bash
source activate myenv
```

**退出环境**

```bash
conda deactivate myenv    # for Windows
source deactivate myenv    # for Linux & Mac
```

**查看Anaconda环境**

```bash
conda info
```

**查看已安装的虚拟环境**

```bash
conda info -e
```

或者

```bash
conda envs
```

**删除虚拟环境**

输出虚拟环境myenv

```bash
conda remove -n myenv --all
```

##### 包管理

**安装**

对于Python的包，尽可能使用`pip`安装

```bash
pip install packages
```

`pip`安装不了的，使用conda

```bash
conda install packages			# 安装最新版本
conda install packsges==xxx		# 安装指定版本
```

**查看当前环境下已安装的包**

python环境下的包

```bash
pip list
```

conda环境下的包

```bash
conda list
```

查看某一conda环境下的包，比如查看myenv环境下的安装的包：

```bash
conda list -n myenv
```

**查找package信息**

**不同镜像源的包的信息不一样，尽量使用`pkgs/main`channels的包**

```bash
conda search packages
```

**升级conda**

```bash
conda update conda
```

**卸载某一个环境下的包**

在myenv虚拟环境下卸载某一packsges

```bash
conda remove -n myenv packages
```

### pip安装

pip 是 Python 包管理工具，该工具提供了对Python 包的查找、下载、安装、卸载的功能。目前如果在 [python.org](https://www.python.org/) 下载最新版本的安装包，则是已经自带了该工具。

Python 2.7.9 + 或 Python 3.4+ 以上版本都自带 pip 工具。

pip 官网：https://pypi.org/project/pip/

#### 安装

在终端利用SSH登录到服务器系统(CentOS 7.6)，由于**普通用户**无法使用`sudo`，如果没有安装python，则需**手动安装pip**，在终端中运行

```bash
wget https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py --user
```

**注意**:参数`--user`一定要记得使用，否则会提示权限不足。

安装完成后会在家目录下生成 .local 文件夹，里面有bin文件夹和lib文件夹，加入环境变量

```bash
export PATH=~/.local/bin:$PATH
```

此时已在非root用户环境中安装好通用的Python包管理工具`pip`。

如果系统安装了Python且Python 2.7.9 + 或 Python 3.4+ 以上版本，**自带 pip 工具**。

#### 更换国内镜像源

为了加快下载速度，更换到[国内的镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)：

```bash
pip install pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

#### 常用命令

**显示版本和路径**

```bash
pip --version
```

**获取帮助**

```bash
pip --help
```

**升级 pip**

```bash
pip install -U pip
```

> 如果这个升级命令出现问题 ，可以使用以下命令：
>
> ```
> sudo easy_install --upgrade pip
> ```

**安装包**

```
pip install SomePackage              # 最新版本
pip install SomePackage==1.0.4       # 指定版本
pip install 'SomePackage>=1.0.4'     # 最小版本
```

比如要安装 Django。用以下的一条命令就可以，方便快捷。

```bash
pip install Django==1.7
```

**升级包**

```bash
pip install --upgrade SomePackage
```

升级指定的包，通过使用==, >=, <=, >, < 来指定一个版本号。

**卸载包**

```bash
pip uninstall SomePackage
```

**搜索包**

```bash
pip search SomePackage
```

**显示安装包信息**

```bash
pip show 
```

**查看指定包的详细信息**

```bash
pip show -f SomePackage
```

**列出已安装的包**

```bash
pip list
```

**查看可升级的包**

```bash
pip list -o
```

**注意事项**

如果 Python2 和 Python3 同时有 pip，则使用方法如下：

Python2：

```
python2 -m pip install XXX
```

Python3:

```bash
python3 -m pip install XXX
```

### OpenCV安装

#### Opencv for Python

首先确保Linux系统安装了Python 3.x，推荐[Anaconda](#Anaconda3安装)，激活不同的虚拟环境后，配置基于Python 3.x的Opencv 4.2。

激活对应的虚拟环境，比如我使用了Anaconda创建了一个虚拟环境`tensorflow-gpu`，激活：

```bash
source activate tensorflow-gpu
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

然后使用`pip`命令进行安装，查找[opencv的不同版本](https://pypi.org/project/opencv-python/3.4.9.33/#history)，使用下面的命令，替换版本号即可：

```bash
pip install opencv-python==3.4.9.33
```

**验证**，在虚拟环境中输入

```bash
python
```

输出

```bash
Python 3.6.8 |Anaconda, Inc.| (default, Dec 30 2018, 01:22:34) 
[GCC 7.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

接着输入

```bash
>>> import cv2
>>> cv2.__version__
'3.4.9'
```

结果显示Opencv3.4.9，安装正确。

**~~源码编译安装~~**

参考Opencv 4.2.0的官网提示](https://docs.opencv.org/4.2.0/d2/de6/tutorial_py_setup_in_ubuntu.html)，其教程是在Ubuntu系统进行的，服务器系统为CentOS，编译安装中注意区别。

- **下载**

  进入官网 : http://opencv.org/releases.html 或者 https://github.com/opencv/opencv/releases, 选择需要的 `x.x.x.zip`版本，以载`opencv-4.2.0.zip` 为例：

  ```bash
  cd
  wget -c https://github.com/opencv/opencv/archive/4.2.0.zip
  unzip
  ```

  文件被解压到名为`opencv-4.2.0`的文件夹内

- **安装依赖**

  确保系统安装了`cmake`、`gcc`、`g++`、`python 3.x`以及以及对应的`numpy`包。

  Opencv 4.2.0支持相机、媒体（ffmpeg、gstreamer）等特征

  **安装多媒体框架ffmpeg**

  centos服务器系统是非常稳定的，所以官方的RPM repository提供的RPM包往往是很滞后的，毕竟是服务器版本，安全稳定是重点，如果是个人用户，推荐Ubuntu，环境搭建起来毕竟方便。CentOS提供的RPM包不够丰富，很多时候需要自己编译，而**EPEL**恰恰可以解决这两方面的问题。**EPEL的全称叫 Extra Packages for Enterprise Linux** 。EPEL是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。装上了 EPEL之后，就相当于添加了一个第三方源。

  **EPEL则为服务器版本提供大量的RPM包，而且大多数RPM包在官方 repository 中是找不到的**。**绝大多数rpm包要比官方repository 的RPM包版本要来得新，EPEL会有很多源地址**

  ##### 1. FFMPEG安装

  **利用EPEL安装FFMPEG**，[参考教程](https://linoxide.com/linux-how-to/install-ffmpeg-centos-7/)。

  - **安装EPEL repo**

    ```bash
    sudo yum -y install epel-release
    ```

  - **添加可用的epel repo**

    ```bash
    rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
    rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-1.el7.nux.noarch.rpm
    ```

  - **安装FFMPEG及其开发包**

    ```bash
    sudo yum -y install ffmpeg ffmpeg-devel
    ```

  - **查看FFMPEG版本**

    ```bash
    ffmpeg
    ```

    输出

    ```bash
    ffmpeg version 2.8.15 Copyright (c) 2000-2018 the FFmpeg developers
      built with gcc 4.8.5 (GCC) 20150623 (Red Hat 4.8.5-36)
      configuration: --prefix=/usr --bindir=/usr/bin --datadir=/usr/share/ffmpeg --incdir=/usr/include/ffmpeg --libdir=/usr/lib64 --mandir=/usr/share/man --arch=x86_64 --optflags='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic' --extra-ldflags='-Wl,-z,relro ' --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libvo-amrwbenc --enable-version3 --enable-bzlib --disable-crystalhd --enable-gnutls --enable-ladspa --enable-libass --enable-libcdio --enable-libdc1394 --enable-libfdk-aac --enable-nonfree --disable-indev=jack --enable-libfreetype --enable-libgsm --enable-libmp3lame --enable-openal --enable-libopenjpeg --enable-libopus --enable-libpulse --enable-libschroedinger --enable-libsoxr --enable-libspeex --enable-libtheora --enable-libvorbis --enable-libv4l2 --enable-libx264 --enable-libx265 --enable-libxvid --enable-x11grab --enable-avfilter --enable-avresample --enable-postproc --enable-pthreads --disable-static --enable-shared --enable-gpl --disable-debug --disable-stripping --shlibdir=/usr/lib64 --enable-runtime-cpudetect
      libavutil      54. 31.100 / 54. 31.100
      libavcodec     56. 60.100 / 56. 60.100
      libavformat    56. 40.101 / 56. 40.101
      libavdevice    56.  4.100 / 56.  4.100
      libavfilter     5. 40.101 /  5. 40.101
      libavresample   2.  1.  0 /  2.  1.  0
      libswscale      3.  1.101 /  3.  1.101
      libswresample   1.  2.101 /  1.  2.101
      libpostproc    53.  3.100 / 53.  3.100
    Hyper fast Audio and Video encoder
    usage: ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...
    
    Use -h to get full help or, even better, run 'man ffmpeg'
    ```

    **FFMPEG安装成功。**

  ##### 3. gtk3

  ```bash
  sudo yum install gtk3 gtk3-devel gtk3-devel-docs
  ```

  ##### 4. v4l（Video for Linux）

  [参考链接](http://www.linuxfromscratch.org/blfs/view/svn/multimedia/v4l-utils.html)，可选。

  ##### 5. gstreamer

  ```bash
  yum install gstreamer*
  ```

- 编译

  如果对 `CMakeLists` 文件不进行修改，那么 `Opencv` 默认的安装位置

  ```bash
  /usr/local/include/opencv2/         -- 新版Opencv核心头文件
  /usr/local/include/opencv/          -- 旧Opencv核心头文件
  /usr/local/share/OpenCV/            -- 一些Opencv其他安装信息
  /usr/local/lib/                     -- Opencv中的动态链接库存放位置
  ```

  开始编译：

  ```bash
  cd opencv-4.2.0
  mkdir build # 创建编译的文件目录
  cd build
  
  # 安装目录: /usr/local
  cmake -D CMAKE_BUILD_TYPE=Release \
        -D CMAKE_INSTALL_PREFIX=/usr/local \    
        -DBUILD_JPEG=ON -DBUILD_TIFF=ON \
        -DBUILD_PNG=ON ..
  
  # 编译
  make
  ```

- **安装**

  ```bsah
  sudo make install 
  ```


### GCC多版本切换*

在CentOS 7下，使用

```bash
sudo yum install gcc
```

CentOS官方的gcc或者g++版本最多到4.8.5，如果要安装Tensorflow 2.0以上的版本，就需要GCC 7.3.1，由于CentOS源没有提供高版本的GCC安装包，这时就不能通过安装包安装，通常的解决方案就是通过编译安装高版本的GCC。在这里介绍如何利用CentOS的新特性**SCL**进行高版本GCC的安装。

[SCL软件集](https://www.softwarecollections.org/en/scls/user/rhscl/)（Software Collections）是为了给 RHEL/CentOS 用户提供一种以方便、安全地安装和使用应用程序和运行时环境的多个（而且可能是更新的）版本的方式，同时避免把系统搞乱。

**devtoolset和GCC对应关系如下表**：

|  devtoolset  | GCC版本 |
| :----------: | :-----: |
| devtoolset-3 |  4.9.2  |
| devtoolset-4 |  5.3.1  |
| devtoolset-6 |  6.3.1  |
| devtoolset-7 |  7.3.1  |
| devtoolset-8 |  8.3.1  |

#### 添加centos-release-scl包

```bash
sudo yum install centos-release-scl
```

#### 启用RHSCL库

```bash
sudo yum-config-manager --enable rhel-server-rhscl-7-rpms
```

#### 安装GCC

Devtoolset 默认安装到 `/opt/rh` 目录下

##### GCC 4.9.2

```bash
sudo yum install devtoolset-3
```

##### GCC 5.3.1

```bash
sudo yum install devtoolset-4
```

##### GCC 6.3.1

[参考官方链接](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-6/)

```bash
sudo yum install devtoolset-6
```

##### GCC 7.3.1

[参考官方链接](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-7/)

```bash
sudo yum install devtoolset-7
```

##### GCC 8.3.1

[参考官方链接](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-8/)

```bash
sudo yum install devtoolset-8
```

scl-utils 只是方便 Software Collections 使用，比如要查看当前安装了哪些Software Collections，可以使用命令

```bash
scl --list
```

#### 临时生效

假设使`GCC 7.3.1`生效，其对应`devtoolset-7`，执行命令

```bash
scl enable devtoolset-7 bash
```

这种方法只对当前的shell环境有效，退出后GCC回退到系统指定的版本。

#### 全局生效

[执行下列命令](http://kuanghy.github.io/2016/03/22/devtoolset)可使对应的GCC全局有效：

```bash
ln -s /opt/rh/devtoolset-7/root/usr/bin/* /usr/local/bin/
hash -r
```

测试 gcc 版本

```bash
gcc --version
```

### MatLab R2020a安装*

NO GUI安装会产生比较多莫名其妙的错误，现场安装吧……



## 第三章：深度学习环境搭建

### SSH远程登录和文件传输

- **SSH**是用来**登录**远程服务器的安全协议；
- **FileZilla**是一个免费开源的FTP软件，支持SSH文件传输协议(SFTP)(可进行本地计算机与远程服务器之间的**文件传输**)

SSH和FileZilla都安装在**本地计算机系统**中

#### SSH安装 

> [SSH](https://en.wikipedia.org/wiki/Secure_Shell)(Secure Shell)由 IETF 的网络小组（Network Working Group）所制定；SSH 为建立在应用层基础上的安全协议。SSH 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。

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

#### SSH登录

在任一平台上打开终端(Windows上推荐[**Xshell**]( https://www.netsarang.com/zh/xshell-download/ )、[**FinalShell**]( http://www.hostbuf.com/t/988.html )，Linux系统和Mac直接打开终端即可)，输入命令

```bash
ssh username@IPaddress -p PORT
```

其中，
`username`为服务器的用户名
`IPaddress`为服务器的IP地址
`PORT`为服务器端口号，默认为22，已禁止。已设置实验室专用端口，请联系管理员

输入密码后即可进入自己账户的主目录（普通用户，无root权限）

#### 修改账户密码

登录到服务器之后，修改原始密码保证账户安全，直接输入

```bash
passwd
```

`username`为您在服务器上的用户名，根据提示修改新的密码。

至此，您已经安全登录到服务器上。

#### FileZilla在Windows和Linux下互传文件

> FileZilla 客户端是一个快速可靠的、跨平台的FTP,FTPS和SFTP客户端。具有图形用户界面(GUI)和很多有用的特性，比如支持FTP, FTP并支持SSL/TLS (FTPS)协议，支持SSH文件传输协议(SFTP)等。

以windows 10为例，下载[FileZilla](https://download.filezilla-project.org/client/FileZilla_3.38.1_win64-setup_bundled.exe)，选择安装位置，正常安装即可。

![](https://pic.downk.cc/item/5e95d3bac2a9a83be5f30b2e.png)

#### FileZilla连接服务器

打开FileZilla软件，

* 1. 点击"**文件**"——"**站点管理器**"

* 2. 点击"**新站点**"，在"**我的站点**"下重命名，此处设为“Lab532”

* 3. 继停留在"**站点管理器**"界面，在"**常规**"选项卡中，设置如下：

  * 主机：**服务器IP地址**
  * 端口：**默认是 22，已禁用，详细端口请联系管理员**
  * 协议：**SFTP - SSH File transfer Protocol**
  * 登录类型：
    - 1）若选"**正常**"
      - 用户名：username
      - 密码：password
    - 2）若选"**询问密码**"，只能填写用户名：username，然后点击右下角的"**连接**"，再输入密码。

* 4. 之后一直ok即可。

#### 文件传输  

以window 10为例，利用FileZilla登录到服务器后，可以看到左边是**本地站点**，右边是**远程站点**(服务器上的目录)，如下图所示，直接将本地文件`test.py`用鼠标拖拽到服务器上的指定目录即可（**上传过程**），这个过程是可逆的（**下载过程**），非常方便。

![](https://pic.downk.cc/item/5e95d3f9c2a9a83be5f335d0.png)

#### 总结

利用**FileZilla**软件配合**SSH**协议，即可完成文件传输及程序运行等必要工作。

### CUDA、cuDNN多版本切换之：修改符号链接和覆盖cuDNN文件*

#### CUDA切换

在[安装CUDA](#安装CUDA)这一小节介绍了多种CUDA文件的安装，这些CUDA版本统一被安装到`/usr/local`文件夹下，使用命令

```bash
ll /usr/local
```

输出

![](https://pic.downk.cc/item/5e95b42ac2a9a83be5dc2927.png)

**系统是通过`/usr/local/`下的`cuda`符号链接来识别CUDA当前指定的CUDA版本**，比如现在符号链接`**cuda**`指向**cuda-9.0**，如果要指向其它CUDA版本，修改符号链接即可，比如切换到**CUDA-10.0**版本：

```bash
cd /usr/local/

# 删除符号链接
sudo rm cuda

# 新建符号链接
sudo ln -s /usr/local/cuda-10.0 /usr/local/cuda
```

**查看当前`cuda`指向**

```bash
ll
```

![](https://pic.downk.cc/item/5e95b5b6c2a9a83be5dd2da7.png)

或者

```bash
stat cuda
```

![](https://pic.downk.cc/item/5e95b63cc2a9a83be5dd7b59.png)

**查看CUDA版本**

```bash
nvcc --version
```

#### cuDNN切换

在Tensorflow不同版本的GPU环境配置中，CUDA的切换，往往伴随着cuDNN的切换，cuDNN是由头文件和动态链接库组成的，所以下载对应的cuDNN，按照[cuDNN的安装方法](#cuDNN安装*)进行文件替换即可。

#### 总结

这种方法的CUDA和cuDNN版本切换，适用于管理员整体切换和个人使用者，不适合服务器环境下的普通用户，有一种好的解决方法是配合[Environment Modules](https://modules.readthedocs.io/en/latest/)来进行环境管理，后续可以进行深入探索，**推荐使用Anaconda来进行CUDA、cuDNN版本的管理。**

 ### （推荐）CUDA、cuDNN多版本切换之：Anaconda下安装不同版本的CUDA、cuDNN

虚拟环境的重要性：**无它，唯方便尔。**

> 因为独立的环境利于管理，不激活不会对系统环境造成不必要的影响，若虚拟环境遇到了无法期望的错误，即可删除环境并重新创建即可，不需要重装系统；创建独立的虚拟环境，各个虚拟环境相互独立，互不影响，不同应用可以使用不同的版本，环境内的包升级不影响其他应用。

虚拟环境的创建有很多方法：Anaconda、virtualenv、docker等，但是就深度学习环境来说，Anaconda方便管理和使用，在此推荐**Anaconda**。

考虑到这么一个场景：苦于科研无想法，于是在github上逛gai碰碰运气，终于看到一个不错的项目，一顿操作，clone这个repo之后，从anaconda创建一个虚拟环境来尝试运行，仔细阅读README，**发现要求特定版本的一些依赖库**，其它的库都还好说，如果要求特定版本的CUDN和cuDNN，如果重新下载安装配置就比较麻烦，何况非管理员用户没有安装和配置权限。这时候考虑Anaconda。

Anaconda生态比较完整，只要系统里正确安装了[NVIDIA驱动](#NVIDIA驱动安装（CentOS、Ubuntu）*)，就可以在Anaconda下的虚拟环境中，安装特定版本的cuda和cudnn，而不必全局进行安装。

需要注意的是：

- **Anaconda的 cudatoolkit 不包含完整安装cuda的全部文件，只是包含了用于 tensorflow，PyTorch，xgboost 和 Cupy 等所需要的共享库文件，cuDNN亦是如此。**

- **如果系统已经全局安装了CUDA和cuDNN，在虚拟环境中又安装了其它版本的CUDA和cuDNN，互不影响，且首先调用虚拟环境中的版本。**

#### conda创建虚拟环境

确定系统安装了[Anaconda3](#Anaconda3安装)，假设此时要搭建一个Tensorflow 1.6的GPU环境，[查表](#Tensorflow建议配置)可知，需求CUDA 9、cuDNN 7版本，python版本指定为3.6。

- **创建虚拟环境**

  在终端输入：

  ```bash
  conda create -n tensorflow16-gpu pip python=3.6
  ```

  创建完成后，在 **~/anaconda3/env** 文件夹下出现名为为 **_tensorflow16-gpu_** 的虚拟环境

  查看当前系统的Anaconda环境

  ```bash
  conda env list
  ```

- **激活虚拟环境**

  不建议使用系统建议的激活方式：`conda activate tensorflow16-gpu`，推荐使用：

  ```bash
  source activate tensorflow16-gpu
  ```

  若要退出当前环境，输入

  ```bash
  source deactivate tensorflow16-gpu
  ```

  究竟使用哪种激活方式，看Anaconda发展和个人喜好。

#### 示例：安装Tensorflow-GPU 1.6（CUDA 9和cuDNN 7）

- **安装CUDA 9**

  **查询Anaconda库里的CUDA版本：**

  ```bash
  conda search cuda
  ```

  截止2020年4月11日，Anaconda官方[CUDA版本](https://anaconda.org/anaconda/cudatoolkit/files)包含：[9.0](https://developer.nvidia.com/cuda-90-download-archive)、[9.2](https://developer.nvidia.com/cuda-92-download-archive)、[10.0](https://developer.nvidia.com/cuda-10.0-download-archive)、10.1（[update1](https://developer.nvidia.com/cuda-10.1-download-archive-update1)和[update2](https://developer.nvidia.com/cuda-10.1-download-archive-update2)）、[10.2](https://developer.nvidia.com/cuda-downloads)，Anaconda [CUDA版本](https://anaconda.org/anaconda/cudatoolkit/files)紧跟NVIDIA官方[CUDA](https://developer.nvidia.com/cuda-toolkit-archive)更新速度，满足绝大部分深度学习环境的要求。

  注意：不同的Anaconda源里的版本不一样，但是channel是`pkgs/main`的比较权威的，推荐使用`pkgs/main`里的版本。

  ![](https://pic.downk.cc/item/5e97d227c2a9a83be532729a.png)

  **Tensorflow 1.6 GPU版本需求CUDA 9.0：**

  ```bash
  conda install cudatoolkit==9.0
  ```

  安装完成

  ![](https://pic.downk.cc/item/5e97d5f0c2a9a83be534c17d.png)

* **安装cuDNN 7**

  **查询Anaconda库里的CUDA版本：**

  ```conda
  conda search cudnn
  ```

  截止2020年4月11日，Anaconda官方[cuDNN版本](https://anaconda.org/anaconda/cudnn/files)如下，根据需求进行安装，也满足绝大部分深度学习环境的要求。

  ![](https://pic.downk.cc/item/5e97d82fc2a9a83be5361d52.png)

  **Tensorflow 1.6 GPU版本需求cuDNN 7**。

  选择cuDNN 7.0.5：

  ```bash
  conda install cudnn==7.0.5
  ```

  出现错误

  ```bash
  Collecting package metadata (current_repodata.json): done
  Solving environment: failed with initial frozen solve. Retrying with flexible solve.
  Collecting package metadata (repodata.json): done
  Solving environment: failed with initial frozen solve. Retrying with flexible solve.
  Solving environment: - 
  Found conflicts! Looking for incompatible packages.                                       failed                                                                          
  UnsatisfiableError:
  ```

  这是怎么回事呢？打开[cuDNN官网](https://developer.nvidia.com/rdp/cudnn-archive)，注意到，同一个版本的cuDNN，可能对应不同的CUDA版本，比如cuDNN 7.0.5可以对应CUDA 8.0、CUDA 9.0和CUDA 9.1，所以查看Anaconda官方里的cuDNN 7.0.5对应哪个CUDA版本。

  ![](https://pic.downk.cc/item/5e97db58c2a9a83be5381c80.png)

  打开cuDNN的[Anaconda仓库](https://anaconda.org/anaconda/cudnn/files)，查询到Anaconda仓库里的cuDNN 7.0.5对应的是CUDA 8.0），因此选择CUDA 9.0对应的**cuDNN 7.1.2**，所以在安装前，使用

  ```bash
  conda search cudnn
  ```

  ![](https://pic.downk.cc/item/5e980e14c2a9a83be559cec6.jpg)

  **查询cuDNN与CUDA的对应关系。**

  **安装cuDNN 7.1.2：**

  ```bash
  conda install cudnn==7.1.2
  ```

  ![](https://pic.downk.cc/item/5e980693c2a9a83be5556703.png)

  这种方法安装CUDA和cuDNN，没有单独形成文件夹，都是统一存放在虚拟环境中的`lib`、`include`文件夹下，查看`CUDA 9.0`和`cuDNN 7.1.2`是否被安装到虚拟环境`tensorflow16-gpu`中：

  ```bash
  ls ~/anaconda3/envs/tensorflow16-gpu/lib/
  ```

  输出

  ![](https://pic.downk.cc/item/5e98076bc2a9a83be555d9a8.png)

  或者使用以下命令**查看CUDA共享库**的安装：

  ``` bash
  ls ~/anaconda3/envs/tensorflow16-gpu/lib/ | grep cuda
  ```

  输出

  ```bash
  libcudart.so
  libcudart.so.9.0
  libcudart.so.9.0.176
  ```

  使用以下命令**查看cuDNN共享库**的安装：

  ```bash
   ls ~/anaconda3/envs/tensorflow16-gpu/lib/ | grep cudnn
  ```

  输出

  ```bash
  libcudnn.so
  libcudnn.so.7
  libcudnn.so.7.1.2
  libcudnn_static.a
  ```

  使用以下命令**查看cuDNN头文件**的安装：

  ```bash
  ls ~/anaconda3/envs/tensorflow16-gpu/include/ | grep cudnn
  ```

  输出

  ```bash
  cudnn.h
  ```

  可以看到相关CUDA、cuDNN的共享库，

* **安装tensorflow-gpu 1.6.0**

  安装完CUDA 9.0和cuDNN 7.1.2之后，在激活的环境中输入

```bash
pip install tensorflow-gpu==1.6.0
```

是否正确安装，请参照章节 **_测试tensorflow-gpu版本的正确性_** 里的方法验证。

### 版本检测及GPU是否安装成功检测

#### Tensorflow GPU版本的正确性验证

在**激活**的环境中，输入`python`，进入到python3.6环境中，依次输入命令

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
>>> tf.__version__
```

注意，`__`为两个`_`组合而成。

#### PyTorch GPU版本的正确性验证

在**激活**的环境中，输入`python`，进入到python3.6环境中，依次输入命令

```python
>>> import torch
>>> torch.cuda.is_available()
```

若输出`True`，则表示PyTorch GPU版本安装成功！

查看PyTorch 的版本的命令为

```python
>>> import torch
>>> torch.__version__
```

注意，`__`为两个`_`组合而成。

### Anaconda下安装Tensorflow的GPU各个版本（从1.6到2.1）

![Tensorflow版本](https://pic.downk.cc/item/5e9137b1504f4bcb04541d52.png)

根据Tensorflow[官网](https://www.tensorflow.org/install/source#linux)，在Anaconda环境下安装Tensorflow 1.6.0、1.11.1和2.1.0三个典型的GPU版本。使用`conda search cudnn`命令**查询cuDNN与CUDA的对应关系。**

![](https://pic.downk.cc/item/5e97d82fc2a9a83be5361d52.png)

#### Tensorflow 1.6.0

系统默认GCC为4.8.5，在正常的终端中输入

```bash
conda create -n tensorflow16-gpu pip python=3.6
source activate tensorflow16-gpu
```

在激活的环境中输入：

```bash
## 安装CUDA 9.0和cuDNN 7.1.2
conda install cudatoolkit==9.0
conda install cudnn==7.1.2
pip install tensorflow-gpu==1.6.0
```

#### Tensorflow 1.11.0

系统默认GCC为4.8.5，在正常的终端中输入

```bash
conda create -n tensorflow111-gpu pip python=3.6
source activate tensorflow111-gpu
```

在激活的环境中输入：

```bash
## 安装CUDA 9.2和cuDNN 7.2.1
conda install cudatoolkit==9.2
conda install cudnn==7.2.1
pip install tensorflow-gpu==1.11.0
```

#### Tensorflow 2.1.0

**切换GCC版本至7.3.1**

```
scl enable devtoolset-7 bash
```

仅本shell本次登录有效，若使全局生效，[查看方法](#全局生效)。

在正常的终端中输入

```bash
conda create -n tensorflow21-gpu pip python=3.6
source activate tensorflow12-gpu
```

在激活的环境中输入：

```bash
## 安装CUDA 10.1.243和cuDNN 7.6.5
conda install cudatoolkit==10.1.243
conda install cudnn==7.6.5
pip install tensorflow-gpu==2.1.0
```

### Anaconda下安装PyTorch的GPU各个版本（从0.4到1.4）

数据来源于[官网](https://pytorch.org/get-started/previous-versions/)，截止2020年4月11号，`PyTorch 1.4`版本需要配合`CUDA 10.1`或`CUDA9.2`配合使用。

![PyTorch](https://pic.downk.cc/item/5e91390d504f4bcb04552f6c.png)

#### PyTorch 0.4.1

```bash
conda create -n pytorch041 pip python=3.6
source activate pytorch041
```

在虚拟环境中

```bash
conda install cudatoolkit==9.0
conda install pytorch=0.4.1 cuda90 -c pytorch
```

#### PyTorch 1.1.0

```bash
conda create -n pytorch110 pip python=3.6
source activate pytorch110
```

在虚拟环境中

```bash
conda install pytorch==1.1.0 torchvision==0.3.0 cudatoolkit=9.0 -c pytorch
```

#### PyTorch 1.4

```bash
conda create -n pytorch14 pip python=3.6
source activate pytorch14
```

在虚拟环境中

```bash
conda install pytorch==1.4.0 torchvision==0.5.0 cudatoolkit=10.1 -c pytorch
```

#### 总结

如果在安装过程中出现类似的错误

```bash
CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/linux-64/pytorch-0.4.1-py36_py35_py27__9.0.176_7.1.2_2.tar.bz2>
Elapsed: -

An HTTP error occurred when trying to retrieve this URL.
HTTP errors are often intermittent, and a simple retry will get you on your way.
```

可能因为网络原因导致安装中断，不断尝试安装命令即可。

安装完成后，请根据小节[PyTorch GPU版本的正确性验证](#PyTorch GPU版本的正确性验证)进行版本验证。

### NVim/Vim简单使用

本小节内容节选自我的另一篇文章[《Linux之打造vim实用配置方案》](https://www.kangzhiheng.top/2020/03/30/Linux之打造vim实用配置方案/)。

#### 什么是Vim？

**[Vim](https://www.vim.org/)- the ubiquitous text editor**，一种无处不在的文本编辑器，维基百科是这么介绍**vim**的：

> **Vim**是从[vi](https://zh.wikipedia.org/wiki/Vi)发展出来的一个[文本编辑器](https://zh.wikipedia.org/wiki/文本编辑器)。其代码补完、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。和[Emacs](https://zh.wikipedia.org/wiki/Emacs)并列成为[类Unix系统](https://zh.wikipedia.org/wiki/类Unix系统)用户最喜欢的编辑器。
>
> Vim的第一个版本由[布莱姆·米勒](https://zh.wikipedia.org/wiki/布萊姆·米勒)在1991年发布。最初的简称是**V**i **IM**itation，随着功能的不断增加，正式名称改成了**V**i **IM**proved。现在是在[开放源代码](https://zh.wikipedia.org/wiki/开放源代码)方式下发行的[自由软件](https://zh.wikipedia.org/wiki/自由软件)。

**文件的增删查改**——熟悉Windows的朋友可能对文件增删查改习以为常，在此不做讨论。对于一名程序工作者，可能经常在Linux下进行程序的增删查改，很多人可能有这么一套方法：我在Windows环境下编写好了程序，然后通过Xftp、FileZilla等类似的工具将其丢到服务器上，一顿操作下来，发现程序报错了，怎么办？然后在Windows下修改，再丢到服务器上双十合十祈求老天保佑……当然像PyCharm这种强大的IDE，会提供远程接入服务器进行同步文件及程序运行等功能，这种也挺好，就是可能需要鼠标点来点去，也有一定的学习成本。现在有这么一个问题，PyCharm明天到期了……社区版？不好意思所有社区版鄙人都不会去尝试，别问为啥，问就是不喜欢。

**配置文件**——配置文件和工作效率有着紧密的联系。对于算法工程师或者后端工程师，在Github逛gai的时候终于发现了一个好用的模型，一顿`git clone repoyoulike`，然后按照配置文档进行修改，什么**tf**要升级啦，**gcc/g++**要降级啦，这个文档要修改路径，那个文档要改参数，都离不开Vim，如果是带桌面的Linux系统，可能会使用`gedit`来打开文件进行编辑，但是这样效率很低，尤其是大型程序修改起来特别费劲。对于大多数情况，可能用**ssh**连接远程服务器，这个时候，怎么也绕不开Vim，尤其是那些只能在服务器上运行的程序。

**知其然知其所以然**——使用Vim来处理程序方面的设定问题，不但可以让自己比较熟悉Linux的运行状况，也更加能保证你的修改可以在Linux下正确运行。

**Vim**适用于各种操作系统上，比如类Unix（种Linux发行版本、Mac 用户）以及Windwos系统。Unix可以使用包管理器安装 Vim；对于 Windows 用户，可以从[官网](https://www.vim.org/download.php)下载。 

#### Vim哲学

简单概括：摆脱鼠标，一键到达。

即为全命令操作，能用键盘做到的事情，绝不用鼠标，在Vim下，键盘可以满足你的一切需求，效率是第一生产力。

#### 升级支持python3的Vim8*

在众多Linux发行版中，比如CentOS或者Ubuntu等，默认安装的是**vi**编辑器，Vim兼容vi。Vim已经存在20年了，时代在进步，[Vim8](https://github.com/vim/vim)在[NeoVim](https://github.com/neovim/neovim)的步步紧逼下，扩展了一些新的特征，比如支持时钟、异步操作、支持终端窗口**terminal**，尤其是支持**terminal**非常关键。本文最开始在Vim7下进行操作的，但是有些插件需要Vim8版本，尤其是[代码补全插件YouCompleteMe](https://github.com/ycm-core/YouCompleteMe)，在升级了支持**Python3**的Vim8后（**在Vim版本中，不能同时支持Python2和Python3**），依然有些瑕疵，比如对Python3的第三方库的扩展不是很友好，对C/C++支持得还不错，不知道是不是我的插件安装有问题还是什么原因，折腾了两三天后，果断把Vim换成了[NeoVim](https://github.com/neovim/neovim)，[YouCompleteMe](https://github.com/ycm-core/YouCompleteMe)换了另一个代码补全插件[coc-vim](https://github.com/neoclide/coc.nvim)，香得不行。在这一小节，介绍怎么**编译支持Python3的Vim8版本**，**前提是系统了安装Python3**，Python3的安装不做讨论。

1. **编译安装yum源里的vim最新版**

   [参考YouCompleteMe](https://github.com/ycm-core/YouCompleteMe/wiki/Building-Vim-from-source)里给出的方法，略作调整。

   首先检查系统里的Vim是否已经安装，在终端输入命令

   ```bash
   rpm -qa | grep vim
   ```

   如果有显示且没有提示任何缺包或者错误，则表示Vim已经被安装，如果没有安装，直接从第3步（**下载Vim最新版源代码**）开始阅读。

   一般情况下，运行下列命令安装Vim

   ```bash
   sudo yum -y install yum*    // CentOS7.6系统
   ```

   查看vim版本

   ```bash
   vim --version
   ```

   可以看到，Vim版本一般是7.4，而现在Vim发行版已经发行到了第八代，包括很多新的功能，为了打造属于自己的IDE，必须升级Vim到最新版本。

   如果是Vim版本，查看Vim是否支持Python3

   ```bash
   vim --version | grep python
   ```

   如果结果显示python3前面是`+`号，则这一小节可以跳过，如果是`-`号，则需要自己编译Vim，需执行以下步骤。

2. **卸载Vim**

   在编译之前，需要卸载系统上的Vim版本

   - 如果使用`yum install vim*`命令安装的Vim，卸载Vim时执行

     ```bash
     yum remove vim
     ```

   - 如果Vim是使用`make`编译过的，在**CentOS7.6系统**下，进入到Vim源代码目录，执行

     ```bash
     sudo make uninstall
     ```

   如果Vim源代码已经删除，则需要重新[下载Vim源码](git clone https://github.com/vim/vim.git)，然后`cd vim`，再执行上述语句。

   如果是**Ubuntu系统**，卸载起来就比较方便

   ```bash
   sudo apt install checkinstall
   cd ~/vim       // vim是Vim源代码文件夹
   sudo checkinstall
   ```

3. **下载Vim最新版源代码**

   ```bash
   cd ~                                         // 进入到当前用户的根目录下
   git clone https://github.com/vim/vim.git     // 下载Vim源代码
   cd vim                                       // 进入到Vim源代码文件
   ```

4. **配置**

   ```bash
   ./configure --with-features=huge \
               --enable-multibyte \
               --enable-rubyinterp=yes \
               --enable-python3interp=yes \
               --with-python3-config-dir=$(python3-config --configdir) \
               --enable-perlinterp=yes \
               --enable-luainterp=yes \
               --enable-gui=gtk2 \
               --enable-cscope \
               --prefix=/usr/local
   ```

   命令[解释](https://www.jianshu.com/p/aac78ff576c5)：

   ` --with-features=huge`：支持最大特性

   `--enable-multibyte`：打开多字节支持，可以在Vim中输入中文

   `--enable-rubyinterp`：打开对ruby编写的插件的支持

   ` --enable-python3interp`：打开对python3编写的插件的支持

   `--with-python-config-dir`：Python3的路径

   `--enable-perlinterp`：打开对perl编写的插件的支持

   ` --enable-luainterp`：打开对lua编写的插件的支持

   `--enable-gui`：打开GUI支持

   `--enable-cscope`：打开对cscope的支持

   `--prefix=/usr/local/`：指定将要安装到的路径

5. **设置VIMRUNTIMEDIR**

   **VIMRUNTIMEDIR**是vim运行时候所需资源的目录，非常重要。

   ```bash
       make VIMRUNTIMEDIR=/usr/local/share/vim/vim82
   ```

6. **编译安装**

   ```bash
   sudo make install
   ```

7. **设置Vim为默认的编辑器**

   使用`update-alternative`设置vim为默认编辑器：

   ```bash
   sudo update-alternatives --install /usr/bin/editor editor /usr/local/bin/vim 1
   sudo update-alternatives --set editor /usr/local/bin/vim
   sudo update-alternatives --install /usr/bin/vi vi /usr/local/bin/vim 1
   sudo update-alternatives --set vi /usr/local/bin/vim
   ```

按照上述步骤完成后查看Vim**版本

```
vim --version
```

![vim版本](https://pic.downk.cc/item/5e89ec78504f4bcb042f0550.png)

可以看到Vim版本是8.2版本，已开启**python3支持**，需要进行验证，在终端输入`vim`，接着在**正常模式**下输入`:echo has("python3")`，按一下回车键，若屏幕最下面输出`1`，则配置正确，Vim支持Python3。

编译的时候可能会出现各种意想不到的问题，这个时候要多看专业的文档，比如官网文档、github及**Issues（非常有用）**、[stackoverflow](https://stackoverflow.com/)等，其次再考虑其它出处，尽可能多看**专业的英文文档**，很多问题我们习惯看一些野路子中文解答，看似走了捷径但是花费了更多的时间，切记！一些优秀的项目有对应的中文文档，首推！

该方法是全局设置方法。

- **安装中文vim帮助文档**

  进入Vim环境，按一下前缀键`esc`，在英文语法环境下，输入`:help`后（终端最下面显示），进入vim帮助文档，此界面是英文的，如果想要设置中文帮助文档，请执行以下步骤：

  - **下载中文安装包**

    ```bash
    wget https://github.com/yianwillis/vimcdoc/archive/v2.3.0.tar.gz
    ```

  - **解压**

    ```bash
    tar zxvf v2.3.0.tar.gz
    ```

  - **安装**

    ```bash
    cd vimcdoc-2.3.0/				# 进入解压后的目录
    ./vimcdoc.sh -i					# 安装
    ```

    命令会自动识别 Vim 的安装路径，并将中文的文档拷贝到相应的地方，原有的英文文档不受影响，查阅中文文档学习Vim。

#### Vim8 —> NeoVim

完全是偶然。在上回讲到，在Vim下折腾了两三天的[代码补全插件YouCompleteMe](https://github.com/ycm-core/YouCompleteMe)，使用起来并不是很顺手，比如对Python3的第三方库的扩展不是很友好，对C/C++支持得还不错，不知道是不是我的插件安装有问题还是什么原因，果断把Vim换成了[NeoVim](https://github.com/neovim/neovim)，[YouCompleteMe](https://github.com/ycm-core/YouCompleteMe)换了另一个代码补全插件[coc-vim](https://github.com/neoclide/coc.nvim)，香得不行。

关于Vim的插件介绍，请查阅我的另一篇文章[《Linux之打造vim实用配置方案》](https://www.kangzhiheng.top/2020/03/30/Linux之打造vim实用配置方案/)。

**NeoVim和Vim的用法基本一致**，底层架构有所不同而已，Vim用户可以很方便的操作NeoVim，配置文件可以直接进行迁移。

[NeoVim](https://github.com/neovim/neovim)的安装：

```bash
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install -y neovim python3-neovim
```

#### 心理准备

- **学习Vim在开始时是痛苦的**，**但是熟悉之后，工作效率会大大提升**
- 需要时间
- 需要不断地练习
- 不要期望你能在3天内把vim练得比别的编辑器更有效率

#### 配置文件

如果使用NVim或Vim编辑文件：

```bash
# NeoVim
nvim file

# Vim
vim file
```

发现界面黑乎乎的，没有行号，没有语法高亮等，作为一名视觉动物，稍微美化一下NeoVim或者Vim，使其不那么打击我们学习的热情。

NVim的配置文件路径：`~/.config/nvim/init.vim`

Vim的配置文件路径：`~/.vim/vimrc`

将下列配置，根据需求，粘贴到配置文件中，然后`:wq`，在终端中输入`source ~/.config/nvim/init.vim`或者`source ~/.vim/vimrc`刷新配置文件。

**请仔细阅读每一行注释。**

```bash
 -----------------
"  基本设置
" -----------------

" 语法高亮
syntax on

" 显示行号
set number

" 显示当前行号的相关行，比如以此行为标准，向上/向下数第1行、第2行等 
set relativenumber

" 设置TAB宽度, ts: tabstop
set expandtab
set tabstop=2
set shiftwidth=2
set softtabstop=2

" 光标所在的那一栏上面/下面还显示出6行
set scrolloff=6


" 显示当前行的下滑线
set cursorline

" 保证当前vim环境下的字不会超出屏幕
set wrap

" 显示当前的命令
set showcmd

" 左下角显示当前的模式名称
set showmode

" 按下TAB，自动补全指令
set wildmenu

" 是否显示状态栏, 0 : 不显示, 1 : 表示只在多窗口时显示, 2 : 表示显示
set laststatus=2
" 显示当前光标的位置、进度以及时间
set statusline=%F%m%r%h%w\ [pos=(%l,%v)]\ [progress=%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")} 

" 关闭当错误发生的bell提示音
set noeb
" 当错误发生时, 屏幕闪烁
" set vb
" 当错误发生时, 不要闪烁屏幕
" set novisualbell    
" ------------------
"   关于搜索和高亮
" ------------------

" 高亮搜索到的词
set hlsearch
"为了避免打开新的文件时显示的上一个文件的搜索结果产生的高亮
" exec 表示在:后面运行的命令
exec "nohlsearch"

" 输入时, 边输入, 边高亮
set incsearch
" 忽略大小写
set ignorecase
" 智能大小写
set smartcase

" 支持鼠标点击和滚动
" set mouse=a

" -----------------
"   map 键盘映射
" -----------------

" 按下小写s, 啥事不干
map s <nop>      
" 按下大写S, 保存当前文件, <CR>为回车按键
map S :w<CR> 
" 按下大写Q, 退出当前文件
map Q :q<CR> 
" 按下大写R, 更新文件配置
map R :source $MYVIMRC<CR>    
```

`77~81`行解释，假设要编辑一个文件，进行了修改，此时

- 保存文件 — 先按前缀键`Esc`，再按住大写`S`，默认操作：先按前缀键`Esc`，再输入`:w`；
- 更新配置 — 先按前缀键`Esc`，再按住大写`R`，默认操作：退出文件，再`source files`；
- 退出文件 — 先按前缀键`Esc`，再按住大写`Q`，默认操作：先按前缀键`Esc`，再输入`:q`；

这三个快捷键非常有用，可以相互搭配使用！

#### 几种模式

- 普通模式（Normal）：启动NVim/Vim后，Vim默认在模式下，进入Normal模式的快捷键：`Esc`；
- 插入模式（Insert）：快捷键：`i`，会看到vim左下角有一个 **-- 插入 --** 字样，表示可以以插入的方式输入，此时，你可以输入文本了，就像你用“记事本”一样；

#### 常用命令

在终端输入：

```bash
vimtutor
```

查看Vim教程（已在[章节](升级支持python3的Vim8*)安装了中文版教程），强烈建议自学一遍。先按前缀键`Esc`，再输入`:q`可退出教程。

- `i `→ Insert 模式，按 ESC 回到 Normal 模式；
- 退出并保存
  - 一般操作：先按前缀键`Esc`，再输入`:wq`；
  - 快捷键：根据上述配置文件，先按前缀键`Esc`，再按住`SQ`；
- 保存并更新配置：
  - 一般操作：先按前缀键`Esc`，再输入`:wq`，在终端输入`source files`；
  - 快捷键：根据上述配置文件，先按前缀键`Esc`，再按住`SR`；
- 退出不保存：先按前缀键`Esc`，再输入`:q!`

- 关于删除：**在普通模式（Normal）下**，
  - `x` → 删除光标所指向的字符；
  - `dd` → 删除光标所在的那一行；
- 关于插入：**在普通模式（Normal）下，**除了`i`可以进入插入模式外，还有
  - `a `→ 在光标后插入；
  - `o `→ 在当前行后插入一个新行；
  - `O` → 在当前行前插入一个新行；
  - `cw` → 替换从光标所在位置后到一个单词结尾的字符；
- 关于撤销/恢复：**在普通模式（Normal）下**，
  - `u` → 撤销；
  - <C-r>，意为`Ctrl + r` → 恢复；
- 关于跳转：**在普通模式（Normal）下**，
  - `gg` → 跳转到当前文件的第一行行首；
  - `G` →  跳转到当前文件的最后一行行首；
  - `0 `→ 跳转到当前行的行首；
  - `$` → 跳转到当前行的最后一个字符处；
  - `50gg` → 跳转到第50行；
  - `50j` → 从当前行向后跳转50行；
- 关于搜索：**在普通模式（Normal）下**，输入`/`，就可以进行文件内搜索，按`n`查找下一个，`N`查找上一个

这只是些非常基础的命令，不过足以应付一般情况，NVim/Vim下有很多实用的插件，请关注我的另一篇文章：[《Linux之打造vim实用配置方案》](https://www.kangzhiheng.top/2020/03/30/Linux之打造vim实用配置方案/)。

### Tmux简单使用

Tmux 是一个终端复用器（terminal multiplexer），非常有用，**建议所有重要的操作，都在Tmux里进行，只要服务器不宕机，操作一直在进行。**

#### 简介

命令行的典型使用方式是，打开一个终端窗口（terminal window，以下简称"窗口"），在里面输入命令。**用户与计算机的这种临时的交互，称为一次"会话"（session）** 。

会话的一个重要特点是，窗口与其中启动的进程是[连在一起](http://www.ruanyifeng.com/blog/2016/02/linux-daemon.html)的。打开窗口，会话开始；关闭窗口，会话结束，会话内部的进程也会随之终止，不管有没有运行完。

一个典型的例子就是，[SSH 登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)远程计算机，打开一个远程窗口执行命令。这时，网络突然断线，再次登录的时候，是找不回上一次执行的命令的。因为上一次 SSH 会话已经终止了，里面的进程也随之消失了。

**为了解决这个问题，会话与窗口可以"解绑"：窗口关闭时，会话并不终止，而是继续运行，等到以后需要的时候，再让会话"绑定"其他窗口。**

#### 作用

**Tmux 就是会话与窗口的"解绑"工具，将它们彻底分离。**

> - 它允许在单个窗口中，同时访问多个会话。这对于同时运行多个命令行程序很有用。
> - 它可以让新窗口"接入"已经存在的会话。
> - 它允许每个会话有多个连接窗口，因此可以多人实时共享会话。
> - 它还支持窗口任意的垂直和水平拆分。

#### 几个概念

- 会话(session): 建立一个 tmux 工作区会话，会话可以长期驻留，重新连接服务器不会丢失，我们只需重新 tmux attach 到之前的工作区就可以恢复会话
- 窗口(window): 容纳多个窗格
- 窗格(pane): 可以在窗口中分成多个窗格

![](https://pic.downk.cc/item/5e986635c2a9a83be5a21e27.jpg)

#### 安装

- **CentOS/Fedora**

  ```bash
  sudo yum install tmux
  ```

- **Ubuntu/Debian**

  ```bash
  sudo apt-get install tmux
  ```

- Mac

  ```bsah
  brew install tmux
  ```

#### 启动和退出

在终端输入

```bash
tmux
```

![](https://pic.downk.cc/item/5e982247c2a9a83be5672750.png)

启动 Tmux 窗口，底部有一个状态栏。状态栏的左侧是窗口信息（编号和名称），右侧是系统信息。

按下`Ctrl+d`或者显式输入`exit`命令，就可以退出 Tmux 窗口。

#### 前缀键

与`vim`、`nvim`类似，都存在一个**前缀键**，Tmux 窗口有大量的快捷键，**所有快捷键都要通过前缀键唤起**。默认的前缀键是`Ctrl+b`，即先按下`Ctrl+b`，快捷键才会生效。

举例来说，帮助命令的快捷键是`Ctrl+b ?`。它的用法是，在 Tmux 窗口中，先按下`Ctrl+b`，再按下`?`，就会显示帮助信息。

然后，按下 ESC 键或`q`键，就可以退出帮助。

当然如果觉得前缀键`Ctrl+b`不太方便，可以更换前缀键。

#### 会话管理

##### 新建会话

第一个启动的 Tmux 窗口，编号是`0`，第二个窗口的编号是`1`，以此类推。这些窗口对应的会话，就是 0 号会话、1 号会话。

使用编号区分会话，不太直观，更好的方法是为会话起名。

```bash
tmux new -s YourSeeName
```

新建了一个名为**YourSeeName**的Tmux窗口。

##### 分离会话

在 Tmux 窗口中，按下`Ctrl+b d`或者输入`tmux detach`命令，就会将当前会话与窗口分离，进入普通的终端界面，**但是会话和里面的进程仍然在后台运行**，`tmux ls`命令可以查看当前所有的 Tmux 会话。

##### 会话查看

在Tmux会话内，列出所有会话的快捷键是`Ctrl+b s`

登录到终端以后，使用命令

```bash
tmux ls
```

可以查看当前所有的 Tmux 会话。

##### 进入会话

进入到Tmux最后一次退出的窗口

```bash
tmux attach
```

使用`tmux ls`查看可用的窗口，然后选择想进入的窗口：

```bash
# 进入名为Seesion-Name的会话窗口
tmux a -t Seesion-Name

# 进入编号为SessionNum的会话窗口
tmux a -t Session-Num	# SessionNum为0、1、2...3
```

##### 杀死会话

```bash
# 使用会话名称
tmux kill-session -t Seesion-Name

# 使用会话编号
tmux kill-session -t Session-Num
```

##### 切换会话

```bash
# 使用会话名称
tmux switch -t Seesion-Name

# 使用会话编号
tmux switch -t Session-Num
```

##### 重命名会话

在Tmux会话内，重命名的快捷键是`Ctrl+b $`

在终端界面

```bash
# 使用会话名称
tmux rename-session -t Seesion-Name New-Name

# 使用会话编号
tmux rename-session -t Session-Num New-Name
```

#### 窗格操作

`Ctrl+b %`：划分左右两个窗格

``Ctrl+b "``：划分上下两个窗格

`Ctrl+b q`：显示窗格编号



## 第四章：在服务器上利用GPU跑程序的简化步骤

1. 利用FileZilla软件登录远程服务器上传所需程序文件
2. 在终端利用SSH登录远程服务器
3. 按照下面的命令进行操作，建议使用**tmux终端复用神器**，第2、3、4步不是必须的，但是强烈推荐使用！

```bash
ssh username@IPaddress -p 22      # 登录服务器
tmux ls                           # 查看终端会话列表，若没有，新建一个
                                  # 会话窗口 tmux new -s Seesion-Name，保证程序在后台运行
tmux a -t Seesion-Name             # 进入你指定的会话窗口
source activate tensorflow-gpu    # 在会话窗口中激活tensorflow-gpu环境
cd xxx/yyy                        # 程序文件所在路径
ls                                # 查看文件
python abc.py                     # 运行程序
```

## 第五章：服务器管理*

### 添加/删除账户

#### 添加用户

```bash
# 1、创建用户并修改密码
sudo useradd -m username -s /bin/bash -g lab532 
sudo passwd username  
```

使用`useradd`命令创建用户，其中

- `-m`：创建用户的主目录
- `-s`：新账户的登录 shell
- `-g`：新账户所属的用户组的名称或 ID，实验室用户都在组`lab532`中

#### 删除用户

```bash
# 2、删除用户
sudo deluser username --remove
```

### 用户组

#### 查看用户的UID和GID

以用户`adoredee`为例，查看用户`adoredee`的的UID和GID

```bash
id adoredee
```

- UID为UserId，即用户ID，用来标识每个用户的唯一标示符
- GID为GroupId，即组ID，用来标识用户组的唯一标识符

#### 修改用户的用户组

```bash
sudo usermod -g lab532 adoredee
```

强制用户`adoredee`的基本组为`lab532`

#### 查看某一用户组下的所有成员

查看用户组`lab532`下的所属成员

```bash
cat /etc/group | grep 'lab532'
```

输出

```bash
lab532:x:1002:
```

得知用户组`lab532`的组ID是**1002**，

```bash
awk -F":" '{print $1"\t\t"$4}' /etc/passwd | grep '1002'
```

显示组`lab532`下的所有用户。

#### 修改用户组名称

```bash
groupmod -n newname oldname
```

用户组名从 `oldname`修改为 `newname`。

### 将普通用户赋予管理员权限

使用`useradd`命令创建的用户没有管理员权限，不能使用`sudo`命令，假设要将用户`adoredee`提拔为系统管理员，首先打开文件

```bash
# 给予/etc/sudoers文件 写 的权限
sudo chmod +w /etc/sudoers
sudo nvim /etc/sudoers
```

然后找到如下界面

```bash
## Next comes the main part: which users can run what software on 
## which machines (the sudoers file can be shared between multiple
## systems).
## Syntax:
##
##      user    MACHINE=COMMANDS
##
## The COMMANDS section may have other options added to it.
##
## Allow root to run any commands anywhere
root     ALL=(ALL)       ALL
```

在root下面一行新增命令：

```bash
adoredee ALL=(ALL)       ALL
```

退出并保存文件，取消`/etc/sudoers`文件 **写** 的权限

```bash
sudo chmod -w /etc/sudoers
```

此时用户`adoredee`从普通用户晋升为系统管理员。

**系统管理员请尊重其他用户的隐私。**

### 修改登录界面显示

![](https://pic.downk.cc/item/5e989362c2a9a83be5ccd569.png)

远程连接服务器时，可以在登录成功后，显示欢迎界面，如上图，设置方法如下：

```bash
# CentOS
sudo nvim /etc/motd
 
# Ubuntu 
sudo nvim /etc/update-motd.d
```

下面是几种不同的风格，直接复制到文件中即可。

#### SJTU

```
            ____     _____  ______  __  __
           /\  _`\  /\___ \/\__  _\/\ \/\ \
           \ \,\L\_\\/__/\ \/_/\ \/\ \ \ \ \
            \/_\__ \   _\ \ \ \ \ \ \ \ \ \ \
              /\ \L\ \/\ \_\ \ \ \ \ \ \ \_\ \
              \ `\____\ \____/  \ \_\ \ \_____\
               \/_____/\/___/    \/_/  \/_____/
```

#### Lab532

```
          /\       /_/__/_\  /\ ___`\ 
           \ \      \ \   \ \ \/\_____\ 
            \ \      \ \___\ \ \ \____/_  
             \ \      \ \___\ \ \ \___\ \ 
              \ \_____ \ \   \ \ \ \___\_\ 
               \/______/\_\   \_\ \/___/_/
```

#### 佛祖保佑

```bash
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 *
 *                             _ooOoo_
 *                            o8888888o
 *                            88" . "88
 *                            (| -_- |)
 *                            O\  =  /O
 *                         ____/`---'\____
 *                       .'  \\|     |//  `.
 *                      /  \\|||  :  |||//  \
 *                     /  _||||| -:- |||||-  \
 *                     |   | \\\  -  /// |   |
 *                     | \_|  ''\---/''  |   |
 *                     \  .-\__  `-`  ___/-. /
 *                   ___`. .'  /--.--\  `. . __
 *                ."" '<  `.___\_<|>_/___.'  >'"".
 *               | | :  `- \`.;`\ _ /`;.`/ - ` : | |
 *               \  \ `-.   \_ __\ /__ _/   .-` /  /
 *          ======`-.____`-.___\_____/___.-`____.-'======
 *                             `=---='
 *		 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^        
 *              佛祖镇楼         永无BUG        永不修改
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
```

#### 佛祖瘫痪

```
/**
 * _ooOoo_
 * o8888888o
 * 88" . "88
 * (| -_- |)
 *  O\ = /O
 * ___/`---'\____
 * .   ' \\| |// `.
 * / \\||| : |||// \
 * / _||||| -:- |||||- \
 * | | \\\ - /// | |
 * | \_| ''\---/'' | |
 * \ .-\__ `-` ___/-. /
 * ___`. .' /--.--\ `. . __
 * ."" '< `.___\_<|>_/___.' >'"".
 * | | : `- \`.;`\ _ /`;.`/ - ` : | |
 * \ \ `-. \_ __\ /__ _/ .-` / /
 * ======`-.____`-.___\_____/___.-`____.-'======
 * `=---='
 *          .............................................
 *           佛曰：bug泛滥，我已瘫痪！
 */
```

#### 神兽草泥马

```bash
             ┏┓　　　┏┓+ +
        ┏┛┻━━━┛┻┓ + +
        ┃　　　　　　　┃ 　
        ┃　　　━　　　┃ ++ + + +
        ████━████ ┃+
        ┃　　　　　　　┃ +
        ┃　　　┻　　　┃
        ┃　　　　　　　┃ + +
        ┗━┓　　　┏━┛
          ┃　　　┃　　　　　　　　　　　
          ┃　　　┃ + + + +
          ┃　　　┃　　　　Codes are far away from bugs with the animal protecting　　　
          ┃　　　┃ + 　　　　神兽保佑,代码无bug　　
          ┃　　　┃
          ┃　　　┃　　+　　　　　　　　　
          ┃　 　　┗━━━┓ + +
          ┃ 　　　　　　　┣┓
          ┃ 　　　　　　　┏┛
          ┗┓┓┏━┳┓┏┛ + + + +
           ┃┫┫　┃┫┫
           ┗┻┛　┗┻┛+ + + +
```

#### 兔子

```
/**********/**********/**********//**********//**********//**********//**********/
  .--,       .--,
 ( (  \.---./  ) )
  '.__/o   o\__.'
     {=  ^  =}
      >  -  <
     /       \
    //       \\
   //|   .   |\\
   "'\       /'"_.-~^`'-.
      \  _  /--'         `
    ___)( )(___
   (((__) (__)))    高山仰止,景行行止.虽不能至,心向往之。
 
 
 
/**********/**********/**********//**********//**********//**********//**********/
```

#### 顶

```
/**
 * 頂頂頂頂頂頂頂頂頂　頂頂頂頂頂頂頂頂頂
 * 頂頂頂頂頂頂頂　　　　　頂頂　　　　　
 * 　　　頂頂　　　頂頂頂頂頂頂頂頂頂頂頂
 * 　　　頂頂　　　頂頂頂頂頂頂頂頂頂頂頂
 * 　　　頂頂　　　頂頂　　　　　　　頂頂
 * 　　　頂頂　　　頂頂　　頂頂頂　　頂頂
 * 　　　頂頂　　　頂頂　　頂頂頂　　頂頂
 * 　　　頂頂　　　頂頂　　頂頂頂　　頂頂
 * 　　　頂頂　　　頂頂　　頂頂頂　　頂頂
 * 　　　頂頂　　　　　　　頂頂頂　
 * 　　　頂頂　　　　　　頂頂　頂頂　頂頂
 * 　頂頂頂頂　　　頂頂頂頂頂　頂頂頂頂頂
 * 　頂頂頂頂　　　頂頂頂頂　　　頂頂頂頂
 */
```

#### 妹子

```
//                       .::::.
//                     .::::::::.
//                    :::::::::::
//                 ..:::::::::::'
//              '::::::::::::'
//                .::::::::::
//           '::::::::::::::..
//                ..::::::::::::.
//              ``::::::::::::::::
//               ::::``:::::::::'        .:::.
//              ::::'   ':::::'       .::::::::.
//            .::::'      ::::     .:::::::'::::.
//           .:::'       :::::  .:::::::::' ':::::.
//          .::'        :::::.:::::::::'      ':::::.
//         .::'         ::::::::::::::'         ``::::.
//     ...:::           ::::::::::::'              ``::.
//    ````':.          ':::::::::'                  ::::..
//                       '.:::::'                    ':'````..
//

```

#### 神兽小龙

```bash
                                                   __----~~~~~~~~~~~------___
                                  .  .   ~~//====......          __--~ ~~
                   -.            \_|//     |||\\  ~~~~~~::::... /~
                ___-==_       _-~o~  \/    |||  \\            _/~~-
        __---~~~.==~||\=_    -_--~/_-~|-   |\\   \\        _/~
    _-~~     .=~    |  \\-_    '-~7  /-   /  ||    \      /
  .~       .~       |   \\ -_    /  /-   /   ||      \   /
 /  ____  /         |     \\ ~-_/  /|- _/   .||       \ /
 |~~    ~~|--~~~~--_ \     ~==-/   | \~--===~~        .\
           '         ~-|      /|    |-~\~~       __--~~
                       |-~~-_/ |    |   ~\_   _-~            /\
                            /  \     \__   \/~                \__
                        _--~ _/ | .-~~____--~-/                  ~~==.
                       ((->/~   '.|||' -_|    ~~-/ ,              . _||
                                  -_     ~\      ~~---l__i__i__i--~~_/
                                  _-~-__   ~)  \--______________--~~
                                //.-~~~-~_--~- |-------~~~~~~~~
                                       //.-~~~--\
 
                                 神兽保佑   代码无BUG!
```

#### 电脑

```
       ,----------------,              ,---------,
          ,-----------------------,          ,"        ,"|
        ,"                      ,"|        ,"        ,"  |
       +-----------------------+  |      ,"        ,"    |
       |  .-----------------.  |  |     +---------+      |
       |  |                 |  |  |     | -==----'|      |
       |  |  I LOVE DOS!    |  |  |     |         |      |
       |  |  Bad command or |  |  |/----|`---=    |      |
       |  |  C:\>_          |  |  |   ,/|==== ooo |      ;
       |  |                 |  |  |  // |(((( [33]|    ,"
       |  `-----------------'  |," .;'| |((((     |  ,"
       +-----------------------+  ;;  | |         |,"
          /_)______________(_/  //'   | +---------+
     ___________________________/___  `,
    /  oooooooooooooooo  .o.  oooo /,   \,"-----------
   / ==ooooooooooooooo==.o.  ooo= //   ,`\--{)B     ,"
  /_==__==========__==_ooo__ooo=_/'   /___________,"
  
```

#### 书

```
                  .-~~~~~~~~~-._       _.-~~~~~~~~~-.
              __.'              ~.   .~              `.__
            .'//                  \./                  \\`.
          .'//                     |                     \\`.
        .'// .-~"""""""~~~~-._     |     _,-~~~~"""""""~-. \\`.
      .'//.-"                 `-.  |  .-'                 "-.\\`.
    .'//______.============-..   \ | /   ..-============.______\\`.
  .'______________________________\|/______________________________`.

```

#### 键盘

宽度有点窄，变形了……

```
/**
 * ┌───┐   ┌───┬───┬───┬───┐ ┌───┬───┬───┬───┐ ┌───┬───┬───┬───┐ ┌───┬───┬───┐
 * │Esc│   │ F1│ F2│ F3│ F4│ │ F5│ F6│ F7│ F8│ │ F9│F10│F11│F12│ │P/S│S L│P/B│  ┌┐    ┌┐    ┌┐
 * └───┘   └───┴───┴───┴───┘ └───┴───┴───┴───┘ └───┴───┴───┴───┘ └───┴───┴───┘  └┘    └┘    └┘
 * ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───────┐ ┌───┬───┬───┐ ┌───┬───┬───┬───┐
 * │~ `│! 1│@ 2│# 3│$ 4│% 5│^ 6│& 7│* 8│( 9│) 0│_ -│+ =│ BacSp │ │Ins│Hom│PUp│ │N L│ / │ * │ - │
 * ├───┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─────┤ ├───┼───┼───┤ ├───┼───┼───┼───┤
 * │ Tab │ Q │ W │ E │ R │ T │ Y │ U │ I │ O │ P │{ [│} ]│ | \ │ │Del│End│PDn│ │ 7 │ 8 │ 9 │   │
 * ├─────┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴─────┤ └───┴───┴───┘ ├───┼───┼───┤ + │
 * │ Caps │ A │ S │ D │ F │ G │ H │ J │ K │ L │: ;│" '│ Enter  │               │ 4 │ 5 │ 6 │   │
 * ├──────┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴────────┤     ┌───┐     ├───┼───┼───┼───┤
 * │ Shift  │ Z │ X │ C │ V │ B │ N │ M │< ,│> .│? /│  Shift   │     │ ↑ │     │ 1 │ 2 │ 3 │   │
 * ├─────┬──┴─┬─┴──┬┴───┴───┴───┴───┴───┴──┬┴───┼───┴┬────┬────┤ ┌───┼───┼───┐ ├───┴───┼───┤ E││
 * │ Ctrl│    │Alt │         Space         │ Alt│    │    │Ctrl│ │ ← │ ↓ │ → │ │   0   │ . │←─┘│
 * └─────┴────┴────┴───────────────────────┴────┴────┴────┴────┘ └───┴───┴───┘ └───────┴───┴───┘
 */
```

#### 剪刀手

```bash
                      d*##$.
 zP"""""$e.           $"    $o
4$       '$          $"      $
'$        '$        J$       $F
 'b        $k       $>       $
  $k        $r     J$       d$
  '$         $     $"       $~
   '$        "$   '$E       $
    $         $L   $"      $F ...
     $.       4B   $      $$$*"""*b
    '$        $.  $$     $$      $F
      "$       R$  $F     $"      $
       $k      ?$ u*     dF      .$
       ^$.      $$"     z$      u$$$$e
       #$b             $E.dW@e$"    ?$
         #$           .o$$# d$$$$c    ?F
         $      .d$$#" . zo$>   #$r .uF
          $L .u$*"      $&$$$k   .$$d$$F
           $$"            ""^"$$$P"$P9$
          JP              .o$$$$u:$P $$
          $          ..ue$"      ""  $"
         d$          $F              $
         $$     ....udE             4B
          #$    """"` $r            @$
           ^$L        '$            $F
             RN        4N           $
              *$b                  d$
               $$k                 $F
               $$b                $F
                 $""               $F
                 '$                $
                  $L               $
                  '$               $
                   $               $


```

#### 龙图腾

```
......................................&&.........................
....................................&&&..........................
.................................&&&&............................
...............................&&&&..............................
.............................&&&&&&..............................
...........................&&&&&&....&&&..&&&&&&&&&&&&&&&........
..................&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&..............
................&...&&&&&&&&&&&&&&&&&&&&&&&&&&&&.................
.......................&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&.........
...................&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&...............
..................&&&   &&&&&&&&&&&&&&&&&&&&&&&&&&&&&............
...............&&&&&@  &&&&&&&&&&..&&&&&&&&&&&&&&&&&&&...........
..............&&&&&&&&&&&&&&&.&&....&&&&&&&&&&&&&..&&&&&.........
..........&&&&&&&&&&&&&&&&&&...&.....&&&&&&&&&&&&&...&&&&........
........&&&&&&&&&&&&&&&&&&&.........&&&&&&&&&&&&&&&....&&&.......
.......&&&&&&&&.....................&&&&&&&&&&&&&&&&.....&&......
........&&&&&.....................&&&&&&&&&&&&&&&&&&.............
..........&...................&&&&&&&&&&&&&&&&&&&&&&&............
................&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&............
..................&&&&&&&&&&&&&&&&&&&&&&&&&&&&..&&&&&............
..............&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&....&&&&&............
...........&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&......&&&&............
.........&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&.........&&&&............
.......&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&...........&&&&............
......&&&&&&&&&&&&&&&&&&&...&&&&&&...............&&&.............
.....&&&&&&&&&&&&&&&&............................&&..............
....&&&&&&&&&&&&&&&.................&&...........................
...&&&&&&&&&&&&&&&.....................&&&&......................
...&&&&&&&&&&.&&&........................&&&&&...................
..&&&&&&&&&&&..&&..........................&&&&&&&...............
..&&&&&&&&&&&&...&............&&&.....&&&&...&&&&&&&.............
..&&&&&&&&&&&&&.................&&&.....&&&&&&&&&&&&&&...........
..&&&&&&&&&&&&&&&&..............&&&&&&&&&&&&&&&&&&&&&&&&.........
..&&.&&&&&&&&&&&&&&&&&.........&&&&&&&&&&&&&&&&&&&&&&&&&&&.......
...&&..&&&&&&&&&&&&.........&&&&&&&&&&&&&&&&...&&&&&&&&&&&&......
....&..&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&...........&&&&&&&&.....
.......&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&..............&&&&&&&....
.......&&&&&.&&&&&&&&&&&&&&&&&&..&&&&&&&&...&..........&&&&&&....
........&&&.....&&&&&&&&&&&&&.....&&&&&&&&&&...........&..&&&&...
.......&&&........&&&.&&&&&&&&&.....&&&&&.................&&&&...
.......&&&...............&&&&&&&.......&&&&&&&&............&&&...
........&&...................&&&&&&.........................&&&..
.........&.....................&&&&........................&&....
...............................&&&.......................&&......
................................&&......................&&.......
.................................&&..............................
..................................&..............................

```

## 第六章：问题汇总

### 1. 禁止使用sudo yum update

起因是服务器更新了内核，CentOS从7.6升级到了7.7，输入`nvidia-smi`提示

> NVIDIA NVML Driver/library version mismatch

在终端输入以下命令查看nvidia的kernel模式

```bash
lsmod | grep nvidia
```

什么都没有。

接着查看显卡驱动所使用的内核版本

```bash
cat /proc/driver/nvidia/version
```

Nothing！

再查看nvcc编译器的版本

```bash
nvcc --version
```

提示：**bash: nvcc: 未找到命令...**

Nothing is everywhere！可能只能去现场启动**重启大法**了。

- **Notice：**

  如果运行`lsmod | grep nvidia`出现了类似

  ```bash
  nvidia_uvm 634880 8
  nvidia_drm 53248 0
  nvidia_modeset 790528 1 nvidia_drm
  nvidia 12312576 86 nvidia_modeset,nvidia_uvm
  ```

  这样的输出，则先**关闭相关的进程**

  ```bash
  sudo rmmod nvidia_uvm
  sudo rmmod nvidia_modeset
  ```

  然后

  ```bash
  sudo rmmod nvidia
  nvidia-smi
  ```

  此时可能会出现

  ```bash
  NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver
  ```

  再看看**NVIDIA显卡状态**

  ```bash
  lspci | grep -i nvidia
  ```

  输出（查看集成显卡的命令是`lspci | grep -i VGA`）

  ```bash
  04:00.0 VGA compatible controller: NVIDIA Corporation GP104 [GeForce GTX 1080] (rev a1)
  04:00.1 Audio device: NVIDIA Corporation GP104 High Definition Audio Controller (rev a1)
  ```

  正常。

  **查看系统依赖**

  ```
  yum info dkms
  yum info libvdpau 
  yum info kernel-devel
  ```

  都已安装，若没有安装，执行`sudo yum install packages`，`packages`指的是缺省的包。

  **为内核安装NVIDIA模块**

  ```bash
  dkms status
  ```

  是否有输出

  ```bash
  cd /usr/src/
  ```

  看看NVIDIA有哪些版本，选择其中一个进行安装

  ```bash
  dkms install -m nvidia -v xxx.xx     // xxx.xx为/usr/src/下的NVIDIA的版本号
  ```

  如果有多个版本，则挨个试试……

  非必要情况下，不要重启。

  继续尝试`nvidia-smi`命令，如果依旧不正常，继续探索。

### 2. ImportError: libcublas.so.9.0

- **错误**——激活环境后，导入`import tensorflow as tf`包后，出现错误

  > ImportError: libcublas.so.7.0: cannot open shared object file: No such file or directory

- **原因**——cuda版本问题，cuda动态链接库没有与系统共享，可能是cuda安装过程中，环境变量`LD_LIBRARY_PATH`未能配置正确

- **解决**——`libcublas.so.9.0`文件在`/usr/local/cuda/lib64`路径下，将其动态链接库为系统共享即可，针对普通管理员用户，在终端运行

  ```bash
  sudo ldconfig /usr/local/cuda/lib64
  ```

  问题可能会被解决，如果出现了**情况2**，请看解答。

### 3. libcublas.so.7.0: cannot open shared object file

- **错误**——运行**情况1**里的`sudo ldconfig /usr/local/cuda/lib64`后，出现错误

  > libcublas.so.7.0: cannot open shared object file: No such file or directory

- **原因**——符号链接出错

- **解决**——创建软链接，将`libcudnn.so.7.0.5`软连接到`libcudnn.so.7`，如果`libcudnn.so.7.0.5`丢失，`libcudnn.so.7`将失效，`-s`表示建立软连接，`-f`表示强制执行。

  ```bash
  sudo ln -sf /usr/local/cuda-9.0/lib64/libcudnn.so.7.0.5 /usr/local/cuda-9.0/lib64/libcudnn.so.7
  ```

### 4. 安装PyTorch时中断安装

- **错误**——在安装PyTorch过程中出现类似的错误

  > CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/linux-64/pytorch-0.4.1-py36_py35_py27__9.0.176_7.1.2_2.tar.bz2>
  > Elapsed: -
  >
  > An HTTP error occurred when trying to retrieve this URL.
  > HTTP errors are often intermittent, and a simple retry will get you on your way.

- **原因**——网络原因
- **解决**——[更换Anaconda源](#配置Anaconda国内开源镜像)或者继续尝试安装命令。

（未完待续，随时更新）

## 参考

1. [stat cuda](https://blog.csdn.net/ksws0292756/article/details/80120561?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2)
2. [NVIDIA NVML Driver/library version mismatch](https://blog.csdn.net/Hungryof/article/details/99233087?depth_1-utm_source=distribute.pc_relevant.none-task-blog-OPENSEARCH-2&utm_source=distribute.pc_relevant.none-task-blog-OPENSEARCH-2)
3. [NVIDIA driver](https://gist.github.com/fxrcode/0afa3c8db13f0ae0b427bb6ddc44adf4)
4. [NVIDIA驱动安装](https://blog.csdn.net/weixin_42451919/article/details/89400365)
5. https://qinqianshan.com/unix/linux/nvidia/
6. [CUDA和cuDNN版本切换](https://dinghow.site/2019/05/19/cuda-cuDNN-version/)
7. [切换GCC](https://oldpan.me/archives/cuda-cuDNN-multiple-edition)
8. [CUDA切换](https://zhuanlan.zhihu.com/p/35834028)