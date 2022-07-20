# overlay目录

挂载是指这个目录挂载在这个分区中，多个目录可以挂载在同一个分区

问题：
overlay是一个虚拟的分区 是docker的联合挂载后生成的
1、为什么docker内部还有一个挂载，这个挂载为什么不是一个目录
一般都是目录，/etc/hosts这种是什么，还没看到

2、overlay 和 /dev/vda1 有什么关系
overlay存储的是docker的信息(镜像信息，容器信息)，/dev/vda1是系统分区
docker的数据都是放在系统分区的

vda1 是系统分区，vdb1是数据分区
 (这里的分区都是指宿主机的分区，即vda1在docker中和在宿主机中执行df -h时，大小是相同的(vdb1也是这样))