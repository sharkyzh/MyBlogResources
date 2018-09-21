---
title: Linux新增磁盘、分区、格式化和自动挂载
category:
  - centos
tags:
  - centos
abbrlink: bf5cf99
date: 2018-09-21 15:15:47
---
### Linux新增磁盘、分区、格式化和自动挂载

系统环境：CentOS7 64位 mini版 
场景：在虚拟机中添加一块40G的SCSI硬盘，分成一个扩展分区，格式化为ext3文件格式，挂载到/opt目录上

#### 1. 查看磁盘信息
```
lsblk
```

#### 2. 分区
```
fdisk /dev/sdb
```
执行上述命令后，根据提示对磁盘进行分区。注意：下边操作提示的红色字体表示输入的指令

1> 命令(输入 m 获取帮助)： m

分区命令列表

2> 添加新的分区


```
命令(输入 m 获取帮助)：n
```
 <!-- more -->

3> 选择分区类型


```
Partition type: 
p primary (0 primary, 0 extended, 4 free) 
e extended 
Select (default p): e 
输入e表示创建扩展分区
```


4> 分区数量


```
分区号 (1-4，默认 1)：1
```


5> 设置扇区

起始 扇区 (2048-83886079，默认为 2048)：回车 
Last 扇区, +扇区 or +size{K,M,G} (2048-83886079，默认为 83886079)：回车

6> 保存


```
命令(输入 m 获取帮助)：w
```


完整操作流程如下图所示： 
操作流程

#### 3. 格式化分区


```
mkfs -t ext3 /dev/sdb
```


分区
最后提示： 
Writing superblocks and filesystem accounting information: 回车

#### 4.挂载磁盘

```
mount /dev/sdb /opt
```

这种方式只临时挂载到/opt目录下，下次开机时不会自动挂载 
注意：挂载后/opt目录下的文件会被临时删除，注意备份

#### 5.开机自动挂载

编辑/etc/fstab文件，在文件中添加如下内容：


```
/dev/sdb /opt ext3 defaults 1 1
```

下回系统重启之后就会自动将/dev/sdb磁盘挂载到/opt目录下了。可以通过mount命令查看文件系统的挂载情况，如下图所示： 
设备挂载情况