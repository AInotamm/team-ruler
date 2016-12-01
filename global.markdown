# Log日志模型以及事件类型相关

- 固有模型
  
  ``` php
  [
          'Sender'    => '',    // 事件施加人
          'EventID'   => 0,     // 事件ID,区别于 EventCode 的是,ID 是唯一识别的
          'EventCode' => 0,     // 事件代码, 描述该事件类型
          'Raw'       => '',    // 调试的源数据,暂时为 SQL 语句,想到之后再添加
          'Content'   => '',    // 人为撰写的事件描述
          'Level'     => '',    // 事件等级,按照与系统相关的必要性排序
          'LogDate'   => null,  // 记录日志的时间
          'ClientIP'  => null,  // 当前施加事件的所有者的 IP 地址
  ]
  ```


- Extra
  
  - 事件ID的计算过程 (generateID)
    
    相关键值: AdminPanel:Log:EventID
    
  - 日志写操作调用方法
    
    大部分forName方法通过__call()进行魔术调用
    
    在未继承LogModel日志模型时, 调用forName()方法操作如下:
    
    ``` php
    $model = '\\Admin\\Model' . '\\' . C('USER_LOG_MODEL') . 'Model';
    $Log = new $model();
    $Log->forName($arg0, $arg1, ...);
    ```
    
    > 推荐后台模块通过以上操作调用方法
  
- 事件类型 (按照Code值由小到大添加)
  
  - EventCode -1 (用户注销成功)
    
    ``` php
    [
    	'Raw'      => '',
        'Content'  => '后台管理用户(#昵称)已经成功注销。'
        'Level'    => 'Info'
    ]
    ```
    
  - EventCode 0 (用户登录成功)
    
    ``` php
    [
    	'Raw'     => (#相关 SQL 语句)
        'Content' => '后台管理用户(#昵称)登录成功，登录地点' . $location
        'Level'   => 'Info'
    ]
    ```
    
  - EventCode 410 (权限列表为空/未设置相关权限)
    
    ``` php
    [
    	'Raw'     => null
        'Content' => '该用户未设置相关的后台管理权限。'
        'Level'   => 'Warn'
    ]
    ```
    
  - EventCode 502 (日志错误)
    
    ``` php
    [
    	'Raw'     => (#数据库报错信息)
        'Content' => '日志数据格式有误'
        'Level'   => 'Warn'
    ]
    ```
    
  - EventCode 503 (日志错误)
    
    ``` php
    [
    	'Raw'     => (#相关 SQL 语句)
        'Content' => '日志写操作失败,请检查调试数据'
        'Level'   => 'Warn'
    ]
    ```
    
  - EventCode 504 (编辑信息失败)
    
    ``` php
    [
    	'EventCode' => 504
    	'Raw'       => (#序列化的编辑内容)
        'Content'   => '编辑(#内容类型)信息失败,数据库报错信息为(#数据库报错信息)'
       	'Level'     => 'Error'
    ]
    ```
    
  - EventCode 505 (添加信息失败)
    
    ``` php
    [
    	'EventCode' => 505
        'Raw'       => (#序列化的新增内容)
        'Content'   => '添加(#内容类型)信息失败，数据库报错信息为(#数据库报错信息)
        'Level'     => 'Error'
    ]
    ```
    
  - EventCode 506 (删除信息失败)
    
    ``` php
    [
    	'EventCode' => 506
        'Raw'       => (#序列化的原有信息)
        'Content'   => '删除(#内容类型)信息失败，数据库报错信息暂无'
        'Level'     => 'Error'
    ]
    ```
    
  - EventCode 510 (缓存服务器失效)
    
    ``` php
    [
    	'Raw'     => (#Redis 报错信息)
        'Content' => '连接Redis缓存服务器失败。'
        'Level'   => 'Error'
    ]
    ```
    
  - EventCode 512 (缓存读取失败)
    
    ``` php
    [
    	'Raw'     => (#Redis 报错信息)
        'Content' => '读取缓存失败，对应的用户ID为(#用户ID)'
        'Level'   => 'Error'
    ]
    ```
    
  - EventCode 513 (缓存写入错误)
    
    ``` php
    [
    	'Raw'     => (#序列化的源数据)
        'Content' => '(#数据类型)缓存写入失败，Redis 相关日志：(#Redis 报错信息)'
        'Level'   => 'Error'
    ]
    ```
    
  - EventCode 514 (缓存删除错误)
    
    ``` php
    [
    	'Raw'     => (#序列化的源数据)
        'Content' => '(#数据类型)缓存删除失败，Redis 相关日志：(#Redis 报错信息)'
        'Level'   => 'Error'
    ]
    ```
    
    ​