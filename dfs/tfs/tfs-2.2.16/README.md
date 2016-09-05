# TFS 使用简介

添加这份文档，预期效果: 存储服务通过sdk和TFS联调时, 可通过本文档描述的方法获取所用TFS服务端的一些关键信息.

[TOC]

## 概念
* Nameserver
> TFS 中心管理节点，管理维护Block和!DataServer相关信息, 包括!DataServer加入，退出, 心跳信息, block和!DataServer的对应关系建立.

* Dataserver
> 存储节点，每块硬盘一个实例, 负责数据的实际读写;

* Adminserver
> Dataserver 监控服务

* Chkserver
> 多集群同步状态检查

* 主块(main block)
> 存储单元, 存储实际的文件元信息和文件内容

* 扩展块(extend block)
> 用于支持修改已写入文件时新写入文件比原文件大的情况.

* block id(逻辑块)
> 根据集群配置，N个副本情况下, 每个block id 对应N个主块＋M个扩展块

* sdk(java sdk)

## 安装部署说明

![TFS部署结构图](tfs_structure.png)

## 简单Debug

#### [集群情况-ssm]

##### 流量统计

```
[root@xxxx ~]# /usr/local/tfs/tfsa/bin/ssm  -s yyyy:xxxx -i "machine -f -i 1 -c 3"
2015-09-28 15:35:00 write flow(MBps):0, write tps:0, read flow(MBps):0, read tps:0
2015-09-28 15:35:01 write flow(MBps):0, write tps:0, read flow(MBps):0, read tps:0
2015-09-28 15:35:02 write flow(MBps):0, write tps:0, read flow(MBps):0, read tps:0
```


##### 文件数统计

```
[root@xxxx ~]# echo "      BLOCK 数           文件数    写入总SIZE  删除文件数   删除文件SIZE   平均文件大小" && /usr/local/tfs/tfsa/bin/ssm  -s yyyy:xxxx -i "block" | grep TOTAL
      BLOCK 数           文件数    写入总SIZE  删除文件数   删除文件SIZE   平均文件大小
TOTAL: 131                947      2.90G        36        67.6M PRE_FILE(3.1M)
```

##### Dataserver统计

```
[root@xxxx ~]# /usr/local/tfs/tfsa/bin/ssm  -s yyyy:xxxx -i "machine -a"
  SERVER_IP     NUMS UCAP  / TCAP =  UR  BLKCNT  LOAD TOTAL_WRITE  TOTAL_READ  LAST_WRITE  LAST_READ  MAX_WRITE   MAX_READ
--------------- ---- ------------------ -------- ---- -----------  ----------  ----------  ---------  --------  ---------
  192.168.41.219     2 9.21G  38.95G  23%     131  56  24.1K     0 61.6K     0     0     0    0     0   0     0   0     0
Total : 1          2 9.21G  38.95G  23%     131  56  24.1K     0 61.6K     0     0     0    0     0
```


#### [tfstool-文件操作]

##### 添加文件

```
[root@xxxx ~]# /usr/local/tfs/tfsa/bin/tfstool  -n -s yyyy:xxxx -i "put /bin/bash"
put /bin/bash => T1XaETByAT1RCvBVdK success.
```

##### 修改文件

```
[root@xxxx ~]# /usr/local/tfs/tfsa/bin/tfstool  -n -s yyyy:xxxx -i "put /bin/bash T1XaETByAT1RCvBVdK"
put /bin/bash => T1XaETByAT1RCvBVdK success.
```

##### 查看文件状态

```
[root@xxxx ~]# /usr/local/tfs/tfsa/bin/tfstool  -n -s yyyy:xxxx -i "stat T1XaETByAT1RCvBVdK"
stat T1XaETByAT1RCvBVdK success.
  FILE_NAME:     T1XaETByAT1RCvBVdK
  BLOCK_ID:      200
  FILE_ID:       13
  OFFSET:        19708779
  SIZE:          903336
  OCCUPY SIZE:   903372
  MODIFIED_TIME: 2015-09-28 13:44:37
  CREATE_TIME:   2015-09-28 13:43:55
  STATUS:        0
  CRC:           1438476645
```

##### 删除文件

```
[root@xxxx ~]# /usr/local/tfs/tfsa/bin/tfstool  -n -s yyyy:xxxx -i "rm T1XaETByAT1RCvBVdK"
unlink T1XaETByAT1RCvBVdK success.

[root@xxxx ~]# /usr/local/tfs/tfsa/bin/tfstool  -n -s yyyy:xxxx -i "stat T1XaETByAT1RCvBVdK"
stat T1XaETByAT1RCvBVdK success.
  FILE_NAME:     T1XaETByAT1RCvBVdK
  BLOCK_ID:      200
  FILE_ID:       13
  OFFSET:        19708779
  SIZE:          903336
  OCCUPY SIZE:   903372
  MODIFIED_TIME: 2015-09-28 13:46:13
  CREATE_TIME:   2015-09-28 13:43:55
  STATUS:        1
  CRC:           1438476645
```

##### reverse_name-文件名反解工具

```
[root@xxxx ~]# /usr/local/tfs/tfsa/bin/reverse_name  L1KyETB4ET1RCvBVdK
blockid: 210, fileid: 32, seqid: 32
```

### 日志查看

#### /www/tfs/work/tfsa/logs/dataserver_?.log

##### 新建文件日志

* 1. 一行 'create file filenumber'
* 2. 多行 'write data' (集群副本数、写入文件大小)
* 3. 一行 'close file'

```
[2015-09-28 11:15:05] INFO  dataservice.cpp:1190 [140232045352704] create file success. filenumber: 15792338638492139986, blockid: 201, fileid: 12, cost: 86
[2015-09-28 11:15:05] INFO  dataservice.cpp:1259 [140232034862848] write data success. filenumber: 15792338638492139986, blockid: 201, fileid: 12, version: 4, leaseid: 121619, role: master, cost time: 1295
[2015-09-28 11:15:05] INFO  dataservice.cpp:1259 [140232055842560] write data success. filenumber: 15792338638492139986, blockid: 201, fileid: 12, version: 4, leaseid: 121619, role: master, cost time: 189
[2015-09-28 11:15:05] INFO  dataservice.cpp:1436 [140232066332416] close file success. filenumber: 15792338638492139986, blockid: 201, fileid: 12, peerip: 192.168.41.219:7375, role: master, cost time: 38173, sla cost time: 57076
```

##### 更新文件日志

* 1. 一行 'create file filenumber'
* 2. 多行 'write data' (集群副本数、写入文件大小)
* 3. 一行 'file exist, update'
* 4. 一行 'close file'

```
[2015-09-28 11:19:45] INFO  dataservice.cpp:1190 [140212705421056] create file success. filenumber: 15792338629902206857, blockid: 246, fileid: 30, cost: 16076
[2015-09-28 11:19:45] INFO  dataservice.cpp:1259 [140212684441344] write data success. filenumber: 15792338629902206857, blockid: 246, fileid: 30, version: 8, leaseid: 121621, role: master, cost time: 1198
[2015-09-28 11:19:45] INFO  dataservice.cpp:1259 [140212673951488] write data success. filenumber: 15792338629902206857, blockid: 246, fileid: 30, version: 8, leaseid: 121621, role: master, cost time: 119
[2015-09-28 11:19:45] INFO  logic_block.cpp:145 [140212694931200] file exist, update! blockid: 246, fileid: 30
[2015-09-28 11:19:45] INFO  logic_block.cpp:175 [140212694931200] write file. fileid equal. blockid: 246, fileid: 30
[2015-09-28 11:19:45] INFO  dataservice.cpp:1436 [140212694931200] close file success. filenumber: 15792338629902206857, blockid: 246, fileid: 30, peerip: 192.168.41.219:63459, role: master, cost time: 18242, sla cost time: 38872
```
