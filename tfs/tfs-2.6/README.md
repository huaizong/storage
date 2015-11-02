# TFS-2.6 调研

* [tfs-2.2.16](http://code.taobao.org/svn/tfs/tags/release-2.2.16)
* [tfs-2.6.2]( http://code.taobao.org/svn/tfs/tags/release-2.6.2)
* [tfs-2.6.4]( http://code.taobao.org/svn/tfs/tags/release-2.6.4)



## 2.6.x vs 2.2.16

### 兼容性(2.2.16-->2.6.x)

* 两个版本TFS C++ SDK 不兼容，不能直接互相读写，不支持两个版本tfs间集群同步
* 2.6的SDK可以以2.2的TFS Name 格式读取文件
* 2.6目前没有Java SDK

### 部署差异

#### Tair 依赖
> 2.6 新增依赖Tair, 用于存储erasure code 编组信息

### feature

#### 1. 支持Erasure Code

> 延时编码，基于 RS 编码方案, 编码方案可定制，代码默认 3+1;

#### 2. 优化扩展块预分配机制
> 扩展块不再通过预留的方式实现，只在必要时直接使用主块作为扩展块，减少扩展块预留的浪费和不可控;

#### 3. 完整实现block id 分区创建发布
> 按不同NS角色分发不同block id

#### 4. 64位Block ID支持
> block id 由32位升级为64位，tfs name 变长, 如  T1AthvB5LT1RCvBVdK --> TB1AthvByET1R47BVdKrtxKrkVb

#### 5. 支持新的机柜信息配置方式
> 支持机柜信息配置

#### 6. 索引信息存储文件元信息
> 文件元信息存储到索引文件中

#### 7. 写改进
> 预期可以改善写请求的响应时间

#### 7.1 lease 机制完整实现
> 减少写请求给Nameserver的压力

#### 7.2 流程优化
* 减少sdk与服务端交互次数
* 改进master 副本与slave 副本的写盘操作为准并行

#### 8 多集群同步优化
* 同步失败放入另外一个队列重试

#### 9 kv 实现 [未比较]
> 当前暂没有计划在tfs层引入此功能，此代码未review

## 2.6.2 vs 2.6.4 vs 2.6.6
* 2.6.6 依赖部分组件未完全开源
* 2.6.4 版本比较2.6.2高,但有较长时间没有更新
* 2.6.2 近期有较多更新和bugfix


## TODO
> 比较2.6.2 和 2.6.4 的差别
