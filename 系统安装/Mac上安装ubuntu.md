
[TOC]

### 安装步骤

- 1  下载Ubuntu ios镜像
- 2 用UItralSo 写入到U盘中
- 3 重启mac， 按住 command + r键
- 4 重启mac后，选择实用工具->终端，执行 csrutil disable
- 5重启mac，打开磁盘工具，选则APPLE...,重新分区，缩小磁盘的空间，减少的就用来装Ubuntu
- 6插入写入镜像的u盘启动，选择 EFIBoot,进入U盘系统
- 7进入系统后，选择 Installxxxx,安装系统。
- 8 设置Ubuntu的安装位置，找到预留下来的分区，点击“-”,预留空间变为空闲，点击“+” 选择逻辑分区，空间起始位置，Ext4文件系统，挂载点“/”。 大小需要预留5-10G的空间
- 9 选择预留的5-10g的空闲空间，点击“+”, 选择 逻辑分区 空间起始位置，用于“交换空间” 。
- 10 选择 安装启动引导器的设备，为dev/sda 重启
- 11 进入mac下载rEFInd的，安装
- 12 安装完成。

### Ubuntu 16.04 无线网卡安装
sudo apt-get install linux-header-generic build-essential dkms

sudo apt-get undate

sudo apt-get install linux-source

sudo apt-get install --reinstall bcmwl-kernel-source(这里可能就成功了)

sudo modprobe wl

sudo dpkg -i wireless-bcm43142-dkms_6.20.5519-1_amd64.deb
