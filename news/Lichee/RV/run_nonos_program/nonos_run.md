# 在D1上使用裸机程序

> 编辑于2022.04.29

近日一国外小哥实现了一种在D1上运行裸机程序的方法。让我们一起来看一下吧

<!-- more -->

Github仓库地址在这里：https://github.com/Ouyancheng/FlatHeadBro

相关使用方法在仓库的 readme 写的很详细了。

大概就是用先编译出一个 启动固件，烧录到SD卡启动板子后可以看到串口有相关的信息打印出来。

接着只需要将想要运行的程序使用python通过串口传送到开发板上面即可。
