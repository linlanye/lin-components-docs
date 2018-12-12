此部分为Lin的[basement](https://github.com/linlanye/basement) 服务器操作部分组件
---

#### 包含如下：
---

* [KVLocal](basement_server/kv/KVLocal.md) （Key-Value型模拟服务器操作类）
```
lin\basement\server\kv\KVLocal;
```

* [KV](basement_server/kv/KV.md) （Key-Value型服务器操作类）
```
lin\basement\server\kv\KVMemecache;  //windows下操作memcached
lin\basement\server\kv\KVMemecached; //linux下操作memcached
lin\basement\server\kv\KVRedis;      //操作redis
```

* [Local](basement_server/local/Local.md)  （本地文件操作类）
```
lin\basement\server\local\Local;
```

* [QueueLocal](basement_server/queue/QueueLocal.md)   （模拟队列型服务器操作类）
```
lin\basement\server\queue\QueueLocal;
```

* [Queue](basement_server/queue/Queue.md)   （队列型服务器操作类）
```
lin\basement\server\queue\QueueRedis;
```

* [SQL](basement_server/sql/SQLPDO.md) （关系数据库操作类）
```
lin\basement\server\sql\SQLPDO;
```