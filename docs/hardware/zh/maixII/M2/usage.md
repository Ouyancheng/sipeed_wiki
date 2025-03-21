---
title: 进阶使用
keywords: MaixII, MaixPy3, Python, Python3, M2dock, Tina, Openwrt
desc: maixpy doc: MaixII M2dock 上手使用
---

> 没有 Linux 系统使用基础的同学，不推荐以下的使用方式

## 认识 openwrt 系统

> 全志 V831 使用 Tina Linux 系统，移植自 [OpenWrt](https://openwrt.org) 。

OpenWrt 可以被描述为一个嵌入式的 Linux 发行版，详情可看 [官方网址](https://openwrt.org) 和 [官方开源仓库](https://github.com/openwrt/openwrt)。

OpenWRT 是一个高度模块化、高度自动化的嵌入式 Linux 系统，拥有强大的网络组件和扩展性，常常被用于工控设备、电话、小型机器人、智能家居、路由器以及 VOIP 设备中。 同时，它还提供了 100 多个已编译好的软件，而且数量还在不断增加，而 OpenWrt SDK 更简化了开发软件的工序。

对于 V831 tina 系统支持使用 adb 调用。需要将主机于板子的OTG标识的接口相连

---

- Windows 将 adb路径 添加到 PATH 即可在命令行使用。但是体验极差

---
- 对于linux需要先安装adb程序且增加一条 udev 规则并重新拔插一下 usb 即可生效
```bash
echo 'SUBSYSTEM=="usb", MODE="0660", GROUP="plugdev", SYMLINK+="android%n"' | sudo tee /etc/udev/rules.d/51-android.rules
```
---

接着只需要在命令行终端执行 `adb shell` 即可连接V831了

## 部分常用 Linux 命令

<details>
  <summary>部分常用命令</summary>
   <pre>
ls 查看目录下文件
cd 打开目录
pwd 打印当前目录
mv 移动/重命名 文件/文件夹
cp 复制 文件/文件夹
rm 删除
vi 编辑文件内容 #经测试windows下会出问题
top 查看系统内存
df 查看磁盘信息
time 查看时间
ifconfig 查看网络信息
free 查看剩余内存
ps 查看运行的进程
kill 杀死进程
killall 杀死所有进程
chmod 更改 文件/文件夹 权限
passwd 设置/更改 用户密码
cat 查看文件内容
ping 检测某网址是否连通
wget 下载某链接文件
grep 搜索文件内容
ln 建立文件链接
</pre>
</details>

### opkg 包管理器

Opkg 是一个轻量快速的套件管理系统，目前已成为 Opensource 界嵌入式系统标准。常用于 路由、 交换机等 嵌入式设备中，用来管理软件包的安装升级与下载。

#### 相关常用命令

- opkg update 更新可以获取的软件包列表
- opkg upgrade 对已经安装的软件包升级
- opkg list 获取软件列表
- opkg install 安装指定的软件包
- opkg remove 卸载已经安装的指定的软件包
  
例如：

```bash
root@sipeed:/# opkg list 
MaixPy3 - 0.2.5-1
alsa-lib - 1.1.4.1-1
busybox - 1.27.2-3
busybox-init-base-files - 167-1612350358
ca-certificates - 20160104
curl - 7.54.1-1
dropbear - 2015.71-2
e2fsprogs - 1.42.12-1
eyesee-mpp-external - 1.0-1
eyesee-mpp-middleware - 1.0-1
eyesee-mpp-system - 1.0-1
```


### pip 包管理器

[pip](https://pypi.org/project/pip/) 是 Python 包管理工具，该工具提供了对 Python 包的查找、下载、安装、卸载的功能。

> 以下讯息由[YanxingLiu](https://github.com/YanxingLiu)提供与测试。

#### pip换源

在安装系统后可以更换镜像源，加速 pip 安装。

#### 临时使用

```python
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

some-package 请自行更换成你想要安装的包

#### 设为默认

升级 pip 到最新的版本 (>=10.0.0) 后进行配置：

```python
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U
```

设置清华镜像源为默认：

```python
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

## 测试屏幕方法

- 请测试前观察系统上电后屏幕是否会闪烁一次；这表示屏幕已经通电、驱动起来，并对其复位（RST）后产生的。

在 Linux Shell 运行 `cat /dev/urandom > /dev/fb0` 就会输入随机数据到 fb0 产生雪花屏了，这表示屏幕显示是正常的。

<center><img src="./asserts/lcd_test.jpg" width="400"></center>

<details>
  <summary>帧缓冲相关知识</summary>
   帧缓冲（framebuffer）是 Linux 为显示设备提供的一个接口，把显存抽象后的一种设备。
   它允许上层应用程序在图形模式下直接对显示缓冲区进行 读写操作。framebuffer 是 LCD 对应的一种 HAL（硬件抽象层），提供抽象的，统一的接口操作，用户不必关心硬件层是怎么实施的。这些都是由 Framebuffer 设备驱动来完成的。帧缓冲设备对应的设备文件为 /dev/fb*，如果系统有多个显示卡，Linux下还可支持多个帧缓冲设备，最多可达 32 个，分别为 /dev/fb0 到 /dev/fb31，而 /dev/fb 则为当前缺省的帧缓冲设备，通常指向 /dev/fb0，在嵌入式系统中支持一个显示设备就够了。帧缓冲设备为标准字 符设备，主设备号为 29 ，次设备号则从 0 到 31 。分别对应 /dev/fb0-/dev/fb31 。
</details>

## 运行 Python3 解释器

在 Linux 上使用 Python 编程只需要在 adb shell 命令行交互的接口输入 python3 即可启动，可直接复制代码粘贴后按回车键运行。

```python
import platform
print(platform.uname())
```

2021年02月23日 实际操作结果：

```bash
   __  ___     _        __   _               
  /  |/  /__ _(_)_ __  / /  (_)__  __ ____ __
 / /|_/ / _ `/ /\ \ / / /__/ / _ \/ // /\ \ /
/_/  /_/\_,_/_//_\_\ /____/_/_//_/\_,_//_\_\ 
 ----------------------------------------------
Linux sipeed 4.9.118 #77 PREEMPT Wed Feb 3 11:06:36 UTC 2021 armv7l GNU/Linux
Please press Enter, then input maixpy3_config.py complete your configuration.

root@sipeed:/# python3
Python 3.8.5 (default, Jan 17 2021, 06:07:56) 
[GCC 6.4.1] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import platform
>>> print(platform.uname())
uname_result(system='Linux', node='sipeed', release='4.9.118', version='#77 PREEMPT Wed Feb 3 11:06:36 UTC 2021', machine='armv7l', processor='')
>>> 
```

## 测试拍照功能

同样进行上面操作先运行 python3, 再将下面代码复制进终端即可

```python
from maix import display, camera
display.show(camera.capture())
```

<center><img src="./asserts/hello_world.jpg" width="500"></center>

> 如果发现屏幕没有亮起显示摄像头内容，请先确保系统是最新的，排查硬件接线与通电方面的问题。因为通常产品出厂前都会做外设硬件测试的。

## 入门教程

本设备是支持使用 [Maixpy3](/soft/maixpy3/zh/index.html) 进行开发使用，请好好阅读 Maixpy3 的使用文档

### **使用 Jupyter IDE 开发**

> 注意！！！！ MaixPy M2dock 不支持在本机安装 Jupyter ！！！

关于 jupyter 的使用和安装请到 MaixPy3 中的[开发环境配置](/soft/maixpy3/zh/install/install.html#jupyter-安装)中查看



