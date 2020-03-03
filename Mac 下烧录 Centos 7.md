# Mac 下树莓派3B+烧录 Centos 7

### 硬件准备

- Mac 电脑
- 读卡器
- SD卡
- 树莓派3B+

### 安装

##### 1 下载centos系统镜像

可以前往[centos官网](http://isoredirect.centos.org/altarch/7/isos/armhfp/)下载arm版的centos 7镜像，

下载下来的是 .xz 文件，解压后可得到 .img 或者 .raw 文件。例如我这里解压后，得到

```shell
CentOS-Userland-7-armv7hl-RaspberryPI-Minimal-1908-sda.raw
```

**2 写入SD卡 ** 

把SD卡插入电脑，然后在命令行查看当前的硬盘情况：

```bash
diskutil list
```

例如我这里，/dev/disk2 就是我的SD卡了

```shell
ekwong  ~  diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.0 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.0 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD - 数据     223.5 GB   disk1s1
   2:                APFS Volume Preboot                 82.8 MB    disk1s2
   3:                APFS Volume Recovery                528.5 MB   disk1s3
   4:                APFS Volume VM                      3.2 GB     disk1s4
   5:                APFS Volume Macintosh HD            11.0 GB    disk1s5

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *15.9 GB    disk2
   1:             Windows_FAT_32 NO NAME                 299.9 MB   disk2s1
   2:                      Linux                         15.6 GB    disk2s2
```

卸载SD卡：

```shell
diskutil unmountDisk /dev/disk2 # 把这里的 /dev/disk2 修改成你的 SD 卡磁盘名称
```

烧录镜像：

```shell
# 把文件路径指向你解压出来的镜像文件，把 /dev/disk2 修改成你的 SD 卡磁盘名称
sudo dd bs=4m if=~/Downloads/CentOS-Userland-7-armv7hl-RaspberryPI-Minimal-1908-sda.raw of=/dev/rdisk2 
```

这时候，命令行会没有什么输出，过几分钟后，会有如下输出：

```shell
578+0 records in
578+0 records out
2424307712 bytes transferred in 212.299948 secs (11419257 bytes/sec)
```

说明烧录好了。

再次卸载SD卡：

```shell
diskutil unmountDisk /dev/disk2 # 把这里的 /dev/disk2 修改成你的 SD 卡磁盘名称
```

##### 3 初始化Centos 7

拔出SD卡，插入树莓派内，树莓派连接显示器和键盘，接通电源。

初始账号：root

密码：    centos

修改一下密码：

```shell
sudo passwd
```

查看网卡信息

```shell
ifconfig
```

 找到设备的内网IP，例如我的**内网IP**是 192.168.200.209

##### 4 远程登录

在Mac上打开命令行，输入

```shell
# 把本地public key保存到树莓派的CentOS 7, 后面登录就无需输入密码
# 把IP地址改成你的树莓派IP地址
ssh-copy-id root@192.168.200.209
```

然后进行登录：

```shell
# 把IP地址改成你的树莓派IP地址
ssh root@192.168.200.209 
```

##### 5 调整磁盘大小

默认情况下，刚安装好时，查看磁盘只有2G容量，

```she
[root@bogon ~]# df -h
文件系统        容量  已用  可用 已用% 挂载点
/dev/root       1.4G  1.2G  212M   85% /
devtmpfs        459M     0  459M    0% /dev
tmpfs           464M     0  464M    0% /dev/shm
tmpfs           464M   13M  451M    3% /run
tmpfs           464M     0  464M    0% /sys/fs/cgroup
/dev/mmcblk0p1  286M   52M  235M   19% /boot
tmpfs            93M     0   93M    0% /run/user/0
```

但我的SD卡是16G的啊，于是要调整一下。

在root目录下，就有一个README文档，打开看一下

```shell
[root@bogon ~]# cat README 
== CentOS 7 userland ==

If you want to automatically resize your / partition, just type the following (as root user):
rootfs-expand
```

很好，直接有个指令可以让我们拓展容量，直接输入指令

```shell
rootfs-expand
```

然后查看容量：

```shell
 df -h
文件系统        容量  已用  可用 已用% 挂载点
/dev/root        14G  1.2G   13G    9% /
devtmpfs        459M     0  459M    0% /dev
tmpfs           464M     0  464M    0% /dev/shm
tmpfs           464M   13M  451M    3% /run
tmpfs           464M     0  464M    0% /sys/fs/cgroup
/dev/mmcblk0p1  286M   52M  235M   19% /boot
tmpfs            93M     0   93M    0% /run/user/0
```

大功告成！