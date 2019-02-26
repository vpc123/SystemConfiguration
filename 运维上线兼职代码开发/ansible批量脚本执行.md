### 批量脚本执行



例子（k8s集群添加节点）:



```
$ansible -i hosts all -m shell -a "sh /opt/software/deploy-k8s/join-in-cluster.sh" -u gyt -s -k -K
```



hosts文件如下：



```
[all]
192.168.202.69
192.168.202.12
192.168.202.32
192.168.202.31
```



这是ansibel执行批量脚本的一个简单例子：

我们接下来将会综合开始进行检查：



```
#copy  文件  到各个主机上
$ansible  -i  hosts  all   -m  copy  -a  “src=/root/xc/test/kubelet  dest=/root/”  -u root  -s  -k   -K

#执行各个主机检查命令
$ansible -i hosts all -m shell -a “ls -l /root/kubelet” -u root -s -k -K

#对各个主机进行权限赋值
$ansible -i hosts all -m shell -a “chmod +x /root/kubelet” -u root -s -k -K

#查看集群主机的状态信息
$ansible -i hosts all -m shell -a “systemctl status kubelet” -u root -s -k -K
$ansible -i hosts all -m shell -a “systemctl status docker” -u root -s -k -K

#检查containerd是否存在问题
$ansible -i hosts all -m shell -a “systemctl status docker | grep 'containerd -l'” -u root -s -k -K
```



对于分组检查集群机器的命令如下所示：



```
[aa]
192.168.202.69
192.168.202.12
192.168.202.32
192.168.202.31
[bb]
192.161.202.69
192.161.202.12
192.161.202.32
192.161.202.31
[cc]
192.166.202.69
192.166.202.12
192.166.202.32
192.166.202.31
```

我们采用分组限流执行按照分组执行：



```
$ansible  -i  hosts   aa  -m  shell   -a  “systemctl  status  kubelet”  -u  root  -s  -k  -K
#注释，我们根据hosts后面的参数指定分组
```



总结:

2019-2-26     作者:流火夏梦       学习部署实践ansible进行批量化脚本上线。
