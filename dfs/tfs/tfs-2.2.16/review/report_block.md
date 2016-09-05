
## TFS 块汇报机制

```
release-2.2.16/src/
```  


### Nameserver 定期让Dataserver 做汇报

* Dataserver

```
    //dataservice.cpp
    //DS 与NS 心跳, 第一次在ns注册时Nameserver会让ds做第一次汇报block
    //然后每个一定时间ns会再触发ds汇报block
    int DataService::run_heart(const int32_t who)
    {
      ....
        iret = send_blocks_to_ns(heart_interval, who, 2000);//2s
      ....
    }
```
* Nameserver

```
    ///接收ds心跳
    int HeartManagement::keepalive(tbnet::Packet* packet)
    {
      int32_t ret = (NULL != packet && SET_DATASERVER_MESSAGE == packet->getPCode()) ? TFS_SUCCESS : EXIT_PARAMETER_ERROR;
      if (TFS_SUCCESS == ret)
      {
        SetDataserverMessage* message = dynamic_cast<SetDataserverMessage*> (packet);
        const DataServerStatInfo& ds_info = message->get_ds();
        //Nameserver 处理心跳
        ret = manager_.get_layout_manager().get_client_request_server().keepalive(ds_info, now);
    }

    ///心跳线程检查汇报任务是否超时, 超时加入汇报队列中
    int ClientRequestServer::keepalive(const common::DataServerStatInfo& ds_info, const time_t now)
    {
        ...
        bool rb_expire = false;
        bool isnew = false;
        ret = manager_.get_server_manager().add(ds_info, now, isnew);
        ///这里决定汇报
        if (server->is_report_block(rb_expire, now, isnew))
        {
          server->set_report_block_status(REPORT_BLOCK_STATUS_IN_REPORT_QUEUE);
          manager_.get_server_manager().add_report_block_server(server, now, rb_expire);
        }
        ...
    }

      ///检查汇报超时(新加入的ds 汇报任务立即开始汇报)
      inline bool is_report_block(bool& rb_expire, const time_t now, const bool isnew) const
      {         
        if (!isnew)
          rb_expire = is_report_block_expired(now);
        //当前时间和next_report_block_time_比较
        return (isnew || rb_expire) ? true : now >= next_report_block_time_;
      }

    ///单次汇报逻辑，汇报完设置下次汇报时间
    int ClientRequestServer::report_block(const uint64_t server, const time_t now,
        std::set<common::BlockInfo>& blocks)
    {
      ...
      ///汇报逻辑
      ...
      //设置下一次汇报
      pserver->set_report_block_status(REPORT_BLOCK_STATUS_COMPLETE);
      pserver->set_next_report_block_time(now, random() % 0xFFFFFFF, false);
      manager_.get_server_manager().del_report_block_server(pserver);
      ...
    }

    ///扫描发起汇报任务线程
    void LayoutManager::check_all_server_report_block_()
    {
      while (!ngi.is_destroyed())
      {
        ....
        get_server_manager().get_and_move_report_block_server(helper, MAX_SLOT_NUMS);

        int32_t index = helper.get_array_index();
        while (index-- > 0)
        {
          CallDsReportBlockRequestMessage req;
```


### Ns 角色切换时触发汇报

```
    void LayoutManager::switch_role(time_t now)
    {
      ....
      ///原理就是更新汇报超时时间，让ns提前向ds发起请求
      get_server_manager().set_all_server_next_report_time(now);
      ...
    }
```

### 工具触发触发汇报

```
      ....
      ///原理就是更新汇报超时时间，让ns提前向ds发起请求
      get_server_manager().set_all_server_next_report_time(now);
      ...
```
