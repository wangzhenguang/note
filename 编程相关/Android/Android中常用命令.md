[TOC]

#### cat命令
```
cat demo.txt | grep xxx
```
> linux中grep是过滤 win是findstr


#### echo、touch
```
touch "xxxx" > demo.txt

echo "xxx" > demo.txt

echo "xxx" >> demo.txt

```

#### adb shell dumpsys activity top 
查看当前应用的activity信息

#### adb shell dumpsys package
查看指定报名应用的详细信息
adb shell dumpsys package com.test.demo

#### adb shell dumps ys meminfo [pname/pid]
可以查看指定进程名或者进程id的内存信息

#### adb shell sumpsys dbinfo [packagename]
查看数据库信息

#### adb install 
安装apk


#### adb uninstall 
卸载应用

#### adb pull [sourceDir] [destDir]
将设备中的文件拉取到本地

###　adb push

### adb shell screencap
截屏 
adb shell screencap - p [dir]

#### adb shell screenrecord [dir]
录屏操作


#### adb shell input text 
输入文本内容

#### adb forward 
设备的端口转发
```
adb forward tcp:1111 tcp:22222
adb forward tcp:1111 jwdp:1233
```

#### adb jdwp 
查看设备中可以被调试的应用的进程号

### adb logcat
adb logcat -s tag
adb logcat |grep xxxx


#### run-as [package name]
可以在非root设备查看指定debug模式的包名应用沙盒数据
#### ps 
查看设备的进程信息
```
ps | grep [regx]
ps -t pid
```
#### pm clear [package]
清空指定包名应用数据

#### pm install/uninstall 
安装/卸载设备中的apk文件，功能和adb install/uninstall一样

#### am start -n [package name]/[package name.activity name]
启动一个应用
am start -D -n debug方式启动

#### am startservice -n [package name]/[package name.activity name]
启动一个服务

#### am broadcast -a [action]
```
am broadcast -a android.NET.conn.CONNECTIVITY_CHANGE

```

#### netcfg 
查看设备的ip地址
#### netstat 查看设备端口号

### app——process
运行java代码

#### dalvikvm 运行一个dex文件

#### top 查看当前应用的cpu消耗情况
top [-n/m/d/s/t]
m 显示多少个进程
n 刷新次数
d 刷新间隔时间
s 按哪列排序
t 显示线程信息而不是进程

#### getprop 查看系统属性值

#### aapt dump xmltree [apk] [xml] 查看或编辑apk

#### dexdump [dex dirpath]
查看一个dex文件的详细信息

#### cat /proc/[pid]/maps 查看当前内存加载情况
比如加载了那些so、dex文件


####　cat　/proc/[pid]/status 查看进程状态信息

#### cat /proc/[pid]/net/tcp/tcp6/udp/udp6



