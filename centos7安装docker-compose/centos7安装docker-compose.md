## centos7使用pip快速安装docker-compose

Docker-Compose是一个部署多个容器的简单但是非常必要的工具.
安装Docker-Compose之前，请先安装 python-pip


### 安装 python-pip

1、首先检查linux有没有安装python-pip包，终端执行 pip -V

    $pip -V

3、执行成功之后，再次执行yum -y install python-pip

    $yum -y install python-pip

 4、对安装好的pip进行升级 pip install --upgrade pip

    $pip install --upgrade pip

### 安装Docker-Compose

1.终端执行：pip install docker-compose --ignore-installed requests 

`$pip install docker-compose --ignore-installed requests` 

2、检查docker-compose 安装：docker-compose -version

    $docker-compose -version

### 安装docker 及 私库（删除镜像及垃圾回收）

0、关闭selinux,修改配置文件/etc/selinux/config,将其中SELINUX设置为disabled

1、加速配置：

    vi /etc/docker/daemon.json
    
    {
    "registry-mirrors": ["https://registry.docker-cn.com"]
    
    }



2、http私库推送

 
    $vi /etc/sysconfig/docker
    
    OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false --insecure-registry 192.168.68.100:5000'

3、配置私库可删除

    version: 0.1
    log:
      fields:
    service: registry
    storage:
      delete:
    enabled: true
      cache:
    blobdescriptor: inmemory
      filesystem:
    rootdirectory: /var/lib/registry
    http:
      addr: :5000
      headers:
    X-Content-Type-Options: [nosniff]
    health:
      storagedriver:
    enabled: true
    interval: 10s
    threshold: 3

4、启运私库窗口


`$sudo docker run -d -p 5000:5000 -v ~/data/registry:/var/lib/registry  -v /usr/local/app/data/config.yml:/etc/docker/registry/config.yml  registry` 

5、删除私库镜像

  `$curl -I -X DELETE http://192.168.68.100:5000/v2/busybox/manifests/sha256:6a67ba482a8dd4f8143ac96b1dcffa5e45af95b8d3e37aeba72401a5afd7ab8e`

sha256由pull镜像进可得

6、垃圾回收

 进入私库容器  docker exec -it f70d0c79e6d546d4 sh


    $docker exec -it f70d0c79e6d546d4 sh

 查看数据大小  du -chs /var/lib/registry/


    $du -chs /var/lib/registry/

进行垃圾回收  

    $registry garbage-collect /etc/docker/registry/config.yml 

docker 开启2375端口，提供外部访问docker

    $vim /usr/lib/systemd/system/docker.service

修改ExecStart行为下面内容


    ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock \

重新加载docker配置

    $systemctl daemon-reload // 1，加载docker守护线程
    $systemctl restart docker // 2，重启docker

总结:pip 安装docker-compose远胜于官网文档安装，所以最好的安装方式就是使用python-pip进行docker-compose进行安装。