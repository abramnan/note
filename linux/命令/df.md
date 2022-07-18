# df命令
显示目前在linux系统上的文件系统磁盘的使用情况统计

## df -h
通过它可以产出可读的格式df命令的输出

```
[root@VM_70_229_centos ~]# df -h
[root@VM_70_229_centos ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        50G  1.3G   46G   3% /
devtmpfs        492M     0  492M   0% /dev
tmpfs           498M   24K  498M   1% /dev/shm
tmpfs           498M  8.3M  489M   2% /run
tmpfs           498M     0  498M   0% /sys/fs/cgroup
/dev/vdb5       2.0G   43M  1.8G   3% /data/github
```
Filessystem表示磁盘，Mounted on表示挂载在这个磁盘的目录
