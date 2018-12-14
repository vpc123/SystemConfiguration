https://blog.csdn.net/zstack_org/article/details/56274966


参考链接：https://www.freedesktop.org/software/systemd/man/journald.conf.html

2  https://github.com/systemd/systemd/issues/9141

引题：systemd-journald  系统参数配置调研


3  参考例子，日志落盘

https://unix.stackexchange.com/questions/482920/store-more-logs-in-memory-with-systemd-journald

### systemd-journald cpu

关于docker  因为配置超出容器cpu限制导致docker死亡，一个例子。

    $ cat /etc/systemd/journald.conf.d/50-set-rate-limit.conf 
    [Journal]
    RateLimitInterval=30s
    RateLimitBurst=120000
    SystemMaxUse=10G
    SystemKeepFree=5G

一 列出服务的配置项以及配置项的作用；
1 网络速率限制
RateLimitInterval=30s
RateLimitBurst=120000
2 系统磁盘使用限制
SystemMaxUse=10G
SystemKeepFree=5G


二 那些配置可以进行优化，优化的好处；
好处：可以限制磁盘使用空间避免超出磁盘存储空间导致系统死亡


参数解析：使用system-journald管理容器日志。

1.创建相关的目录来存放journal日志，修改权限，重启systemd-journal服务。

    [root@ChatDevOps ~]# mkdir /var/log/journal
    [root@ChatDevOps ~]# chgrp systemd-journal /var/log/journal
    [root@ChatDevOps ~]# chmod g+s /var/log/journal
    [root@ChatDevOps ~]# systemctl restart systemd-journald


在 docker.service 文件中的 ExecStart 字段中，添加(或：docker run --log-driver=journald)：

    --log-driver=journald \

之后：

    $systemctl daemon-reload
    $systemctl restart docker.service

配置文件写入到journald.conf中



     
    1#日志存储到磁盘
    Storage=persistent

    2#压缩日志
    Compress=yes 

	3#为日志添加序列号
	Seal=yes

	4#每个用户分别记录日志
	SplitMode=uid 

    5#日志同步到磁盘的间隔，高级别的日志，如：CRIT、ALERT、EMERG 三种总是实时同步
	SyncIntervalSec=1m 
    
    6#即制日志的最大流量，此处指 30s 内最多记录 100000 条日志，超出的将被丢弃
	RateLimitInterval=30s 

	7#与 RateLimitInterval 配合使用
	RateLimitBurst=100000

    8#限制全部日志文件加在一起最多可以占用多少空间，默认值是10%空间与4G空间两者中的较小者
	SystemMaxUse=64G
   
    9#默认值是15%空间与4G空间两者中的较大者
	SystemKeepFree=1G 

    10#单个日志文件的大小限制，超过此限制将触发滚动保存
	SystemMaxFileSize=128M

    11#日志滚动的最大时间间隔，若不设置则完全以大小限制为准
	MaxFileSec=1day

	12#日志最大保留时间，超过时限的旧日志将被删除
	MaxRetentionSec=100year 
  
    13#是否转发符合条件的日志记录到本机的其它日志管理系统，如：rsyslog
	ForwardToSyslog=yes 
	ForwardToKMsg=no

	14#是否转发符合条件的日志到所有登陆用户的终端
    ForwardToWall=yes 
    MaxLevelStore=debug 
    MaxLevelSyslog=err 
    MaxLevelWall=emerg 
    ForwardToConsole=no 
    #TTYPath=/dev/console
    #MaxLevelConsole=info
    #MaxLevelKMsg=notice


修改配置生效步骤：


    mkdir /var/log/journal
    chown root:systemd-journal /var/log/journal
    chmod 0770 /var/log/journal
    systemctl reset-failed systemd-journald.service && systemctl restart systemd-journald.service



日志查看工具 journalctl 的用法：

    journalctl
    -u/--unit=docker.service \ #可以多次使用该选项，按 OR 逻辑筛选显示
    -o/--output=export \ #指定显示格式，常用三种： export、json-pretty、cat
    -r/--reverse \ #反向显示，即较新的日志显示在最上面
    --no-pager \ #不要使用 less 或 more 分页显示
    -f/--follow \ #类似 tail -f 效果
    --flush \ #将内存中日志同步到磁盘
    -D/--directory=DIR \ #指定读取日志的路径
    --file=zLogFilePath \ #同上，指定具体文件路径，可同时使用多次指定多个文件
    --priority= "emerg" (或 0), "alert" (1), "crit" (2), "err" (3), "warning" (4), "notice" (5), "info" (6), "debug" (7) \ #指定要显示的日志等级
    --since= "2012-10-30 18:17:16" \
    --until= "2017-10-30 18:17:16" \
    --disk-usage \ #显示所有日志占用的磁盘空间
    #export 格式显示的特定进程的标识字段均可以用作筛选，例如：
    CONTAINER_ID= \ #以指定容器 ID 为标识显示日志
    CONTAINER_NAME= \ #同上，指定容器名称
    _PID= \ #以容器进程 ID 为标识显示日志
    _UID= \ ＃显示以某个用户 ID 身份运行的所有容器日志