
# 分布式对象存储系统


* 分层存储
* 元数据管理
* 小文件优化
* 大文件优化
* 跨集群复制
* 多副本策略
* 流控保护
* 网络拓扑感知
* 物理拓扑感知


## 元数据管理

* 基础元数据

> 文件属性如创建时间、修改时间、大小..

* 用户自定义元数据

> 用户级的元数据，如访问规则

### TFS

#### 2.2.16

##### 基础元数据
不持久化元数据，依靠dataserver汇报.

##### 自定义元数据


## 数据文件管理


### TFS

基于逻辑块机制，小文件合并
