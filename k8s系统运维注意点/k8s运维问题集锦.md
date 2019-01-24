## K8s 棘手运维问题集锦

#### 1 k8s运维过程问题之一，遇到难于删除的pod，或者因为不可知的因素pod一直处于无法删除的状态可以使用如下的方式进行强制删除

    $kubectl delete pod deviosow-1828 --namespace=kube-system --grace-period=0 --force

#### 2 针对deamonset文件部署的pod在大型的集群中更新升级问题，因为demonset启动的pod更改过配置需要重新删除旧pod以后新启动的pod才会符合现有的更改配置。

	#所以我们提出分标签删除一组相同lable的pod,按照如下方式就可以达到我们的目的
	$kubectl delete pod -l app=node-exporter -n monitoring