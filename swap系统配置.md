## Linux环境下Swap配置方法

1.内存占用情况

    [root@joshua ~]# free -m

2.通过dd命令增加swap，增加一个2g的swap空间

    [root@joshua ~]# dd if=/dev/zero of=/var/swap bs=1024 count=2048000

3.创建swap文件

    [root@joshua ~]# mkswap /var/swap

	[root@joshua ~]# mkswap -f /var/swap

4.加载该文件： 

    [root@joshua ~]# swapon /var/swap
5.看看swap是否生效

    [root@mysql01 var]# free 
6.查看swap当前状态： 

    [root@joshua ~]# cat /proc/swaps 

7.添加至/etc/fstab , vim /etc/fstab

    #
    # /etc/fstab
    # Created by anaconda on Thu Aug 24 12:21:20 2017
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    UUID=1ae5f12f-79c1-47d4-bed7-941ab9385396 / ext4 defaults 1 1
    tmpfs /dev/shm tmpfs defaults 0 0
    devpts /dev/pts devpts gid=5,mode=620 0 0
    sysfs /sys sysfs defaults 0 0
    proc /proc proc defaults 0 0
    /var/swap swap swap defaults 0 0

8.如果不再需要swap，可以清理该分区： 

    [root@joshua ~]# swapoff /var/swap

