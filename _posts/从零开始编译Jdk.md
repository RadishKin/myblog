想要深入了解JVM，就必须了解其实现机制。了解JVM实现的最好方法便是自己动手编译JDK。本文中，我将参照《深入理解Java虚拟机》中的教程，编译openjdk1.8 

## 配置环境

### 系统环境
- 编译机器：Lenovo RUI7000 (i7-7700hq+8g DDRR4内存)
- 操作系统：Manjaro i3wm 18.0
- 虚拟机：kvm-qemu 
- 虚拟机系统：ubuntu18.04

### 软件环境

java虚拟机部分大多是C++代码，但类库都是由java写成的，所以编译JDK之前你首先需要有个jdk（嘿嘿，我编译我自己

所以编译环境需要以下依赖程序：
- bootstrap jdk：jdk1.8或jdk1.7
- gcc 4.4+ (据官网说gcc版本最高只测试到7.4)
- 

