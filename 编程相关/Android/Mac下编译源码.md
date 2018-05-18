[TOC]

### 创建一个磁盘镜像

hdiutil create -type SPARSE -fs 'Case-sensitive Journaled HFS+' -size 60g ~/android.dmg.sparsefile

### 挂在分区

 hdiutil attach ~/android.dmg.sparsefile.sparseimage -mountpoint /Volumes/android
 
### 配置源码下载工具
 
 mkdir ~/bin # 创建文件夹
PATH=~/bin:$PATH #设置环境变量
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo #下载repro 到/bin/repo文件里
chmod a+x ~/bin/repo # 给repo 文件权限

### 修改文件限制

在 .bash_profile中添加
ulimit -S -n 1024 

### 进入分区初始化想要下载的分支
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b 你想下载的版本分支

> https://source.android.com/source/build-numbers#source-code-tags-and-builds

### 下载

repo sync


##　编译

### 编译遇到的问题
- xcode版本的问题  下载相应的就好 ://github.com/phracker/MacOSX-SDKs/releases 也可以下载需要的版本放入xcode中即可
- dalvik/CleanSpec.mk:47: missing separator.  Stop.   重新开一个窗口就好