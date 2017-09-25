# QingCloud 块存储 IOPS 小记

## 介绍

IOPS，是英文 input output operations per second 的缩写，翻译成汉语的意思是每秒的输入输出操作。它是衡量磁盘IO的一个重要指标。甚至如AWS这样的厂商，依据IOPS 不同的级别，提供不同的商品和服务。

至少在QingCloud官方的介绍中是看不到关于这项参数的，恰好笔者又是QingCloud的小白，于是就想法测试一下。据江湖传闻，QingCloud后端的研发是不屑于回答类似的问题的，他们在打造改变世界的下一代神器。

## 放弃dd的理由

在过去的单机时代，我们习惯使用dd这个超级命令来测试磁盘的I/O性能，但是dd有一些问题，尤其是在当今互联网应用的时代：

* dd是单线程的，顺序写的测试，假如你运行的是典型的web+数据库的应用的话，dd的结果毫无意义，这些服务都不是顺序写的。
* 没有读取的性能测试

所以我们选择fio，fio可以模拟多个线程/进程，并能进行随机的读和写。也是现在非常流行的一款I/O性能测试工具。

## 本次测试的步骤

1. 在QingCloud基础设施平台下创建虚拟主机。
2. 创建硬盘卷
3. 将刚刚创建的卷挂接到虚拟主机
4. 配置并mount块设备
5. 安装测试I/O 性能的工具：fio、ioping
6. 记录相关数据，并整理
7. 清理上述资源，以免产生不必要的费用

## 设置环境篇

关于如何创建基于Ubuntu16.04的虚拟主机，笔者已经介绍过很多遍了，这里就不再赘述，直接从创建QingCloud 硬盘卷开始,如下图所示范的，

![]()

### 超高性能硬盘

创建完成之后，登录虚拟主机操作系统，执行如下命令，

```
$sudo mkdir /mnt/vdc
$sudo mkfs.ext4 /dev/vdc
$sudo mount /dev/vdc /mnt/vdc
```

### 容量型硬盘
创建完成之后，登录虚拟主机操作系统，执行如下命令，

```
$sudo mkdir /mnt/vdd
$sudo mkfs.ext4 /dev/vdd
$sudo mount /dev/vdd /mnt/vdd
```

## 性能测试篇

安装工具fio，在系统中执行如下命令：

```
$sudo apt-get install -y fio ioping
```

### 随机读写

运行如下命令：

```
fio -randrepeat=1 -ioengine=libaio -direct=1 -gtod_reduce=1 -name=test -filename=/mnt/vdc/test -bs=4k -iodepth=64 -size=4G -readwrite=randrw -rwmixread=75
```


此命令的意思是，会创建一个4GB的文件/mnt/vdc/test，每次执行64次操作，用4Kb的读和写来（3：1的几率）。以下是在QingCloud高性能磁盘的输出：

```
test: (g=0): rw=randrw, bs=4K-4K/4K-4K/4K-4K, ioengine=libaio, iodepth=64
fio-2.2.10
Starting 1 process
test: Laying out IO file(s) (1 file(s) / 4096MB)
Jobs: 1 (f=1): [m(1)] [100.0% done] [97.90MB/33840KB/0KB /s] [25.6K/8460/0 iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=20321: Thu Sep 14 17:06:33 2017
  read : io=3071.7MB, bw=100357KB/s, iops=25089, runt= 31342msec
  write: io=1024.4MB, bw=33467KB/s, iops=8366, runt= 31342msec
  cpu          : usr=5.97%, sys=23.24%, ctx=650799, majf=1, minf=9
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=786347/w=262229/d=0, short=r=0/w=0/d=0, drop=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: io=3071.7MB, aggrb=100356KB/s, minb=100356KB/s, maxb=100356KB/s, mint=31342msec, maxt=31342msec
  WRITE: io=1024.4MB, aggrb=33466KB/s, minb=33466KB/s, maxb=33466KB/s, mint=31342msec, maxt=31342msec

Disk stats (read/write):
  vdc: ios=782329/260940, merge=0/6, ticks=1932792/27260, in_queue=1959892, util=99.65%
```

由此可以看出，QingCloud的超高性能硬盘的读操作IOPS为25086，写操作的IOPS为8366，总共运行时间31秒

以下是在QingCloud容量型磁盘的输出：

```
test: (g=0): rw=randrw, bs=4K-4K/4K-4K/4K-4K, ioengine=libaio, iodepth=64
fio-2.2.10
Starting 1 process
test: Laying out IO file(s) (1 file(s) / 4096MB)
Jobs: 1 (f=1): [m(1)] [100.0% done] [28164KB/9236KB/0KB /s] [7041/2309/0 iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=26944: Thu Sep 14 17:45:34 2017
  read : io=3071.7MB, bw=19968KB/s, iops=4991, runt=157524msec
  write: io=1024.4MB, bw=6658.8KB/s, iops=1664, runt=157524msec
  cpu          : usr=1.14%, sys=4.76%, ctx=1006983, majf=1, minf=9
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=786347/w=262229/d=0, short=r=0/w=0/d=0, drop=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: io=3071.7MB, aggrb=19967KB/s, minb=19967KB/s, maxb=19967KB/s, mint=157524msec, maxt=157524msec
  WRITE: io=1024.4MB, aggrb=6658KB/s, minb=6658KB/s, maxb=6658KB/s, mint=157524msec, maxt=157524msec

Disk stats (read/write):
  vdd: ios=784626/264667, merge=0/62, ticks=9657636/866808, in_queue=10524784, util=100.00%
```

由此可以看出，QingCloud的容量型硬盘的读操作IOPS为4991，写操作的IOPS为1664，总共运行时间157秒

### 随机读

运行如下命令：

```
fio -randrepeat=1 -ioengine=libaio -direct=1 -gtod_reduce=1 -name=test -filename=/mnt/vdc/test -bs=4k -iodepth=64 -size=4G -readwrite=randread
```

此命令的意思是，会创建一个4GB的文件/mnt/vdc/test，每次执行64次操作，用4Kb的大小随机读。以下是在QingCloud高性能磁盘的输出：

```
test: (g=0): rw=randread, bs=4K-4K/4K-4K/4K-4K, ioengine=libaio, iodepth=64
fio-2.2.10
Starting 1 process
Jobs: 1 (f=1): [r(1)] [100.0% done] [98.27MB/0KB/0KB /s] [25.2K/0/0 iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=22801: Thu Sep 14 17:20:57 2017
  read : io=4096.0MB, bw=99969KB/s, iops=24992, runt= 41956msec
  cpu          : usr=3.80%, sys=16.70%, ctx=925976, majf=0, minf=73
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=1048576/w=0/d=0, short=r=0/w=0/d=0, drop=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: io=4096.0MB, aggrb=99969KB/s, minb=99969KB/s, maxb=99969KB/s, mint=41956msec, maxt=41956msec

Disk stats (read/write):
  vdc: ios=1047844/3, merge=0/1, ticks=2658364/0, in_queue=2658400, util=99.64%
```
由此可以看出，QingCloud的随机读操作IOPS为24992，总共运行时间42秒

以下是在QingCloud容量型磁盘的输出：

```
test: (g=0): rw=randread, bs=4K-4K/4K-4K/4K-4K, ioengine=libaio, iodepth=64
fio-2.2.10
Starting 1 process
Jobs: 1 (f=1): [r(1)] [100.0% done] [36884KB/0KB/0KB /s] [9221/0/0 iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=1791: Thu Sep 14 18:25:55 2017
  read : io=4096.0MB, bw=36428KB/s, iops=9107, runt=115138msec
  cpu          : usr=1.41%, sys=6.22%, ctx=1031816, majf=2, minf=76
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=1048576/w=0/d=0, short=r=0/w=0/d=0, drop=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: io=4096.0MB, aggrb=36428KB/s, minb=36428KB/s, maxb=36428KB/s, mint=115138msec, maxt=115138msec

Disk stats (read/write):
  vdd: ios=1047037/3, merge=0/1, ticks=7338440/0, in_queue=7338480, util=99.95%

```

由此可以看出，QingCloud的随机读操作IOPS为9107，总共运行时间115秒

### 随机写

运行如下命令：

```
fio -randrepeat=1 -ioengine=libaio -direct=1 -gtod_reduce=1 -name=test -filename=/mnt/vdc/test-write -bs=4k -iodepth=64 -size=4G -readwrite=randwrite
```

此命令的意思是，会创建一个4GB的文件/mnt/vdc/test，每次执行64次操作，用4Kb的大小随机读。以下是在QingCloud高性能磁盘的输出：

```
test: (g=0): rw=randwrite, bs=4K-4K/4K-4K/4K-4K, ioengine=libaio, iodepth=64
fio-2.2.10
Starting 1 process
test: Laying out IO file(s) (1 file(s) / 4096MB)
Jobs: 1 (f=1): [w(1)] [100.0% done] [0KB/40948KB/0KB /s] [0/10.3K/0 iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=23778: Thu Sep 14 17:27:40 2017
  write: io=4096.0MB, bw=39779KB/s, iops=9944, runt=105441msec
  cpu          : usr=2.54%, sys=16.08%, ctx=624813, majf=0, minf=9
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=0/w=1048576/d=0, short=r=0/w=0/d=0, drop=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
  WRITE: io=4096.0MB, aggrb=39778KB/s, minb=39778KB/s, maxb=39778KB/s, mint=105441msec, maxt=105441msec

Disk stats (read/write):
  vdc: ios=0/1055037, merge=0/51605, ticks=0/6569804, in_queue=6570796, util=99.79%
```

由此可以看出，QingCloud的超高性能随机写操作的IOPS为9944，总共运行时间105秒。

以下是在QingCloud容量型磁盘的输出：

```
test: (g=0): rw=randwrite, bs=4K-4K/4K-4K/4K-4K, ioengine=libaio, iodepth=64
fio-2.2.10
Starting 1 process
test: Laying out IO file(s) (1 file(s) / 4096MB)
Jobs: 1 (f=0): [w(1)] [100.0% done] [0KB/36140KB/0KB /s] [0/9035/0 iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=2350: Thu Sep 14 18:32:31 2017
  write: io=4096.0MB, bw=13563KB/s, iops=3390, runt=309235msec
  cpu          : usr=0.63%, sys=4.41%, ctx=1028670, majf=1, minf=9
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=0/w=1048576/d=0, short=r=0/w=0/d=0, drop=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
  WRITE: io=4096.0MB, aggrb=13563KB/s, minb=13563KB/s, maxb=13563KB/s, mint=309235msec, maxt=309235msec

Disk stats (read/write):
  vdd: ios=19/1049356, merge=0/70842, ticks=20/20416992, in_queue=20417304, util=99.97%

```
由此可以看出，QingCloud的超高性能随机写操作的IOPS为3390，总共运行时间309秒。

### 单个请求的IO 延迟

执行如下命令，来衡量io延迟：

```
$ sudo ioping -c 10 .
```

超高性能磁盘输出结果：

```
ioping -c 10 .
4 KiB from . (ext4 /dev/vdc): request=1 time=93 us
4 KiB from . (ext4 /dev/vdc): request=2 time=237 us
4 KiB from . (ext4 /dev/vdc): request=3 time=227 us
4 KiB from . (ext4 /dev/vdc): request=4 time=134 us
4 KiB from . (ext4 /dev/vdc): request=5 time=158 us
4 KiB from . (ext4 /dev/vdc): request=6 time=153 us
4 KiB from . (ext4 /dev/vdc): request=7 time=179 us
4 KiB from . (ext4 /dev/vdc): request=8 time=190 us
4 KiB from . (ext4 /dev/vdc): request=9 time=175 us
4 KiB from . (ext4 /dev/vdc): request=10 time=142 us

--- . (ext4 /dev/vdc) ioping statistics ---
10 requests completed in 9.00 s, 5.92 k iops, 23.1 MiB/s
min/avg/max/mdev = 93 us / 168 us / 237 us / 40 us
```
由此可以看出QingCloud超高性能的IO延迟平均为0.168微秒。

容量型磁盘输出结果：

```
ioping -c 10 .
4 KiB from . (ext4 /dev/vdd): request=1 time=414 us
4 KiB from . (ext4 /dev/vdd): request=2 time=1.26 ms
4 KiB from . (ext4 /dev/vdd): request=3 time=841 us
4 KiB from . (ext4 /dev/vdd): request=4 time=807 us
4 KiB from . (ext4 /dev/vdd): request=5 time=716 us
4 KiB from . (ext4 /dev/vdd): request=6 time=809 us
4 KiB from . (ext4 /dev/vdd): request=7 time=836 us
4 KiB from . (ext4 /dev/vdd): request=8 time=743 us
4 KiB from . (ext4 /dev/vdd): request=9 time=918 us
4 KiB from . (ext4 /dev/vdd): request=10 time=810 us

--- . (ext4 /dev/vdd) ioping statistics ---
10 requests completed in 9.01 s, 1.23 k iops, 4.79 MiB/s
min/avg/max/mdev = 414 us / 815 us / 1.26 ms / 195 us
```
由此可以看出QingCloud容量型磁盘的IO延迟平均为0.815微秒。

## 结论

QingCloud提供的超高性能IOPS 表现卓越，能够满足绝大多数的高I/O、低延迟的应用负载需求。相比而言，容量型硬盘更加倾向于对于I/O不敏感，存放持久性的数据。

## 参考资料：

1. [IOPS: Benchmarking Disk I/O – AWS vs DigitalOcean](https://dzone.com/articles/iops-benchmarking-disk-io-aws-vs-digitalocean)
2. [AWS Benchmark EBS Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/benchmark_procedures.html)
