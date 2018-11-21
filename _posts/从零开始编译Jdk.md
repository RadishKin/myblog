---
title: 一次失败的jdk编译
categories:
- 笔记
tags:
- java
- jdk
- jvm
---

想要深入了解JVM，就必须了解其实现机制。了解JVM实现的最好方法便是自己动手编译JDK。本文中，我将参照《深入理解Java虚拟机》中的教程，编译openjdk1.8 

## 准备工作

### 系统环境
- 编译机器：Lenovo RUI7000 (i7-7700hq+8g DDRR4内存)
- 操作系统：Manjaro i3wm 18.0


### 软件环境

java虚拟机部分大多是C++代码，但类库都是由java写成的，所以编译JDK之前你首先需要有个jdk（嘿嘿，我编译我自己

所以编译环境需要以下依赖程序：
- bootstrap jdk：jdk1.8或jdk1.7
- gcc，g++ 4.4+ (据官网说gcc版本最高只测试到7.4)

执行
```bash
sudo pacman -S java-8-openjdk gcc g++
```


### 下载源码
openjdk团队使用Mercurial工具管理源代码
安装:
```bash
sudo pacman -S Mercurial  
```
克隆代码
```bash
hg clone http://hg.openjdk.java.net/jdk8u/jdk8u/
```
clone完成后，进入源码目录，执行脚本
```bash
bash get_source.sh
```
会自动获取全部源码

>如果下载太慢，可以使用github上的代码镜像
>https://github.com/unofficial-openjdk/openjdk/
>注意：直接克隆整个仓库会下载所有版本jdk源码

## 编译
自动生成编译文件并检查依赖
```
bash ./configure
```
如果中途中断，请检查输出，安装对应的依赖库

执行编译命令
```bash
make all
```
如果成功会在所在文件夹下生成一个images的文件夹

然后我失败了QAQ

目前知道是遇到了一个已知bug，另外编译环境也炸了hhhh，下篇文章再说吧

咕咕咕


