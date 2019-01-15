## 全面解析devicemapper各个配置字段的含义和配置项


##### 解析devicemapper字段信息：

    Storage Driver: devicemapper
     Pool Name: docker-252:0-5637144768-pool
     Pool Blocksize: 65.54kB
     Base Device Size: 64.42GB
     Backing Filesystem: xfs
     Data file: /dev/loop0
     Metadata file: /dev/loop1
     Data Space Used: 87.43GB
     Data Space Total: 214.7GB
     Data Space Available: 127.3GB
     Metadata Space Used: 99.05MB
     Metadata Space Total: 2.147GB
     Metadata Space Available: 2.048GB
     Thin Pool Minimum Free Space: 21.47GB

### 运作理论
devicemapper graphdriver使用设备映射器精简配置模块（dm-thinp）来实现CoW快照。对于每个设备映射器图形位置（通常/var/lib/docker/devicemapper，下面的$graph），基于两个块设备创建精简池，一个用于数据，一个用于元数据。默认情况下，这些块设备是使用自动创建稀疏文件的环回挂载自动创建的。

##### 字段详解：
	#存储驱动类型
	1 Storage Driver: devicemapper

使用的默认环回文件是$graph/devicemapper/data和 $graph/devicemapper/metadata。从docker实体映射到相应的devicemapper卷所需的其他元数据存储在$graph/devicemapper/json文件中（编码为Json）。

为了支持系统上的多个设备映射图，thin pool将命名为：docker-0:33-19478248-pool，其中0:30部分是次要/主要设备nr，并且19478248是$graph目录的inode编号。
在 thin pool中，docker会自动创建一个基本瘦设备，称为docker-0:33-19478248-base固定大小的设备。这在创建时自动格式化，只包含一个空文件系统。此设备是所有docker镜像和容器的基础。所有基本映像都是此设备的快照，然后这些映像将用作其他映像和最终容器的快照。

##### 字段详解：
	#精简池设备名称
	1 Pool Name: docker-252:0-5637144768-pool
	# 精简池设备块大小
    2  Pool Blocksize: 65.54kB


### 选项
devicemapper后端支持使用--storage-opt标志启动docker守护程序时可以指定的一些选项。
这使用dm前缀，并将使用类似的东西

    $docker -d --storage-opt dm.foo=bar

以下是支持的选项列表：

- dm.basesize

指定创建基本设备时要使用的大小，这会限制图像和容器的大小。默认值为10G。注意，精简设备本质上是“稀疏的”，因此大多数空的10G设备不会在池上使用10 GB的空间。但是，文件系统将为空的情况使用更多空间，设备越大。

使用示例：
##### 字段详解：
	#基础设备大小
	1 Base Device Size: 64.42GB
	#docker设定基本设备块大小
    $docker -d --storage-opt dm.basesize=20G

- dm.loopdatasize

指定为用于thin pool的“数据”设备创建环回文件时要使用的大小。默认大小为100G。请注意，该文件是稀疏的，因此它最初不会占用这么多空间。

使用示例：

    $docker -d --storage-opt dm.loopdatasize=200G

- dm.loopmetadatasize

指定为用于thin pool的“元数据”设备创建环回文件时要使用的大小。默认大小为2G。请注意，该文件是稀疏的，因此它最初不会占用这么多空间。
使用示例：

	##### 字段详解：
	#元数据设备使用可用大小
	1 Metadata Space Used: 99.05MB
    2 Metadata Space Total: 2.147GB
    3 Metadata Space Available: 2.048GB


- dm.fs

指定要用于基础设备的文件系统类型。支持的选项是“ext4”和“xfs”。默认为“ext4”

使用示例：

	##### 字段详解：
	#基础设备的文件系统类型
	1 Backing Filesystem: xfs
    $docker -d --storage-opt dm.fs=xfs

- dm.mkfsarg

指定在创建基础设备时要使用的额外mkfs参数。

使用示例：

    $docker -d --storage-opt "dm.mkfsarg=-O ^has_journal"

- dm.mountopt

指定安装精简设备时使用的额外安装选项。

使用示例：

    $docker -d --storage-opt dm.mountopt=nodiscard

- dm.datadev

指定用于thin pool的数据的自定义块设备。
如果使用块设备进行设备映射器存储，理想情况下应指定datadev和metadatadev以完全避免使用环回设备。

使用示例：
	##### 字段详解：
	# 块设备进行设备映射器存储
	1 Data file: /dev/loop0
    2 Metadata file: /dev/loop1
    $docker -d --storage-opt dm.datadev=/dev/sdb1 --storage-opt dm.metadatadev=/dev/sdc1

- dm.metadatadev

指定用于thin pool的元数据的自定义块设备。

为获得最佳性能，元数据应位于与数据不同的主轴上，或者在SSD上更好。

如果设置新的元数据池，则必须有效。这可以通过将前4k归零来指示空元数据来实现，如下所示：

``dd if = /dev/zero of =$metadata_dev bs = 4096 count = 1```

使用示例：

	##### 字段详解：
	# 元存储和元数据存储的使用情况
	1 Data Space Used: 87.43GB
    2 Data Space Total: 214.7GB
    3 Data Space Available: 127.3GB
    4 Metadata Space Used: 99.05MB
    5 Metadata Space Total: 2.147GB
    6 Metadata Space Available: 2.048GB
    $docker -d --storage-opt dm.datadev=/dev/sdb1 --storage-opt dm.metadatadev=/dev/sdc1

- dm.blkdiscard

删除devicemapper设备时启用或禁用blkdiscard。如果使用环回设备，则默认情况下（仅限）启用此功能，并且需要在图像/容器移除时重新解析回送文件。

对回禁用这可能会导致多快容器取出时间，但会在不被返回到系统用于其他用途的/var/lib/docker目录中使用的空间容器被删除时。

使用示例：

    $docker -d --storage-opt dm.blkdiscard=false


总结：所有的数据字段按照要求已经完全定义并且给出使用方法字段的属性含义。


参考:
link:https://github.com/snitm/docker/tree/master/daemon/graphdriver/devmapper
