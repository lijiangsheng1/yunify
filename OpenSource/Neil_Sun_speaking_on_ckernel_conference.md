# 一名Linux系统工程师是如何练成的

## 引子

中国Linux内核开发者大会，已经开了十二年了，向来都是以干货十足著称，这里的干货指的是涉及到计算机操作系统底层的技术，如CPU指令集、驱动、虚拟化、容器、程序调度、内存管理、网络延迟等等，哪怕你是算法高手、应用专家、业务资深咨询，在这里也得睡个好觉。嗯，不妨看看今年的议程：

![](https://raw.githubusercontent.com/lijiangsheng1/yunify/master/media/scheduled.jpeg)

对于中国Linux内核大会本身，就不做过多的描述了，如果有兴趣，可以参考其[往届会议](http://www.ckernel.org/index.html#old)。

## Qemu 后端块设备驱动开发

是的，我们这次聊的是来自青云QingCloud 内核小组的 Neil Sun 的分享。乍看之下，QEMU 的驱动开发，似乎和大会的主题有所偏离，其实不然，要知道 QEMU 本身就是一个模拟器，几乎无所不能，QEMU是一套由法布里斯·贝拉(Fabrice Bellard)所编写的以GPL许可证分发源码的模拟处理器，在GNU/Linux平台上使用广泛。Bochs，PearPC等与其类似，但不具备其许多特性，比如高速度及跨平台的特性，通过KQEMU这个闭源的加速器，QEMU能模拟至接近真实电脑的速度。

据孙锋介绍，Qemu的驱动开发是和Linux内核开发有一些相似之处的。更何况QEMU有着广泛的用途，如云计算中的虚拟化、智能手机操作系统Android模拟开发环境等有着QEMU的身影。

### 背景

说起为何要去开发 QEMU 的后端驱动，还得从青云 QingCloud 的 [NeonSan](https://www.qingcloud.com/enterprise/products-and-services/qingstor-neonsan/) 讲起， NeonSAN 是由软件定义存储技术实现的新一代分布式超大容量块存储系统（Server SAN），而既然是存储系统，对于云平台来说，就需要给虚拟机使用，而我们知道青云QingCloud的 IaaS平台使用的是 KVM Linux 内核虚拟化技术，而 KVM 使用的设备就是QEMU，另外由于QEMU的模拟特性，运行在KVM之下的Guest 虚拟机的操作系统性能就有所影响，所以又开发了 VirtIO，以进一步提高 IO 的性能，看看其运行的工作原理：

![](https://raw.githubusercontent.com/lijiangsheng1/yunify/master/media/virtualio_arch.png)

所以，要让 NeonSan 能够搭配基于 KVM 的虚拟化技术，就需要手动的为 QEMU 撰写驱动。

### 开始编写之前需要了解的一些工具

QEMU 后端支持的块存储有多种类型：宿主机的文件、宿主机的块设备、网络设备如 nbd、glusterfs等。 QEMU 针对镜像提供了工具：```qemu-image```，针对网络的 nbd有工具： ```qmeu-nbd```，启动虚拟机的命令: ```qemu-system-*```，以及针对开发者使用的 ```qemu-io```，所谓的工欲善其事，必先利其器，在开发之前，定要熟悉这些工具的适用场景以及操作。

### 正式编写 QEMU 后端设备驱动

编写后端设备驱动，你需要了解 BlockDriver 结构体的基本信息，如基本信息、块的大小定义等，Block size， 物理块大小原子性，逻辑块大小，logic size 大小须指定，若其不指定，客户操作系统Linux将无法启动。以及基本的 IO 函数实现。

这个时候，按照 QEMU 的规范和上述信息，NeonSan 的网络支持，其实是蛮简单的实现。（工程师一般都这么说。）

### 写完之后是要验证并测试的

作为一名Linux底层系统工程师，必须具备工匠精神，写完驱动仅仅是完成了任务的一半而已，另外一半，就是不要出错，确保功能是完整的、可用的、安全的。那么就需要一系列的工具来进行验证。QEMU 在这部分也作了很完善的支持，如 ```qemu-iotests```、```blkverify```、```blkdebug```。

那么在 QEMU 之外，也需要对应的测试，如：

1. 在 Guest 虚拟机下使用```ltp fs test```来验证结果。
2. 在 Guest 虚拟机下使用```fio``` 来测试性能。
3. 性能还可以使用的工具有```iostat```、```dstat```、```blktrace```
4. 在宿主机上的性能测试，可以使用```Perf ``` + ``` FrameGraph```组合，最后生成酷炫的火焰报告图。
5. 以及针对开发者分析的工具：“QMP”、“HMP” ，然后利用 ```nc ip port```来进行监听。

好了，到这里基本上就结束了 QEMU 后端块存储驱动开发的全部过程了。当然这是几个月以来 Neil Sun 以及 青云QingCloud 内核团队的共同努力的结果。从准备、到分析、到编写、再到实际验证，这是一整套严谨的过程，一个具备工匠精神的工程师的日常操守。

## 青云QingCloud 内核小组都干些什么工作？

先来介绍下，此次分享的演讲者：孙锋，在加入青云QingCloud之前，孙锋是三星电子天津的内核开发工程师，针对三星移动端设备的Linux内核进行稳定性的开发和维护。加入青云之后，日常就是维护青云QingCloud后台系统的内核，以及对诸如虚拟化、分布式存储、SDN等的研发支撑。

青云QingCloud 的后台采用的较新的Linux 内核，所以有可能会遇到其它人还没有遇到过的Bug，踩别人没有踩过的坑，所以这里是让那些对系统底层原理感兴趣的同学的最佳实践之地，当然，也是青云QingCloud底层稳定性的看门人。而且解决的问题，都会反馈到上有社区，参与到全球的kernel的开发当中，成为让人荣耀加身Linux内核开发者。

另外，青云QingCloud 也会维护一些常见Linux的发行版，如Debian、CentOS等，那么这些系统既然是青云提供的，青云QingCloud 就得保证其日常的更新，以及问题的修复。而这也是内核小组的工作。

一句话，内核小组就是研发的基石，最底层的技术支撑。

Qemu的驱动开发是和Linux内核开发有一些相似之处的。

BlockDriver 结构体的基本信息，

Block size， 物理块大小原子性，逻辑块大小，

logic size 大小须指定，若其不指定，客户操作系统Linux将无法启动。

开发之后的验证工作，也是颇让人费神的部分，Qemu提供了很多的工具来帮助开发者完成这些事，如fio、blkdebug,blkverfiy, fio on guest ,iostat, dstat,

iostat -x output more , per second read/write band

dstat will replace iostat , write in python, and custom write plugin.

blktrace this application need more infomation.  need btt convert to human read.

perf on host, 火焰图 flamegraph this is hack mind, it's very Cool.

QMP,HMP, nc  , writing-qmp-commands.txt 来检测一些信息。

libvirt 的支持，所修改的比较少。configure.ac, m4 , libvirt


snapshot, internal snapshot, raw format , 后端存储去实现。数据一致性是由后端来实现的。

AIO 也是由后端实现的，QEMU 只是接入而已。（4K ，512字节的拆分）QEMU的其余的没有怎么改动，如协程等。 分布式的server SAN。

QEMU，和后端的对接，对QEMU的修改，patch，以及仅针对NeoSAN作了相应的驱动。
