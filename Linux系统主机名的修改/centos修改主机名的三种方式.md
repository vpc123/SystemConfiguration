## centos7 三种修改主机名的方式

1  立即修改，临时生效

    $hostname host1(修改的名字)

2  永久修改主机名(需要重新开shell)

    $hostnamectl set-hostname <hostname>

3 还有一种方式通过修改/etc/hostname文件，之后reboot就可以完成名称修改。

    $vi /etc/hostname
    $reboot


