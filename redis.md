# redis
- 简介
  remote dictionary server: 基于内存的数据存储系统。mysql是基于磁盘存储的系统
  支持5种基本数据类型与5种高级数据类型  
  string 字符串            stream 消息队列  
  list 列表                Geospatial 地理空间  
  set 集合                 HyperLogLog  
  SortedSet 有序集合       bitmap 位图  
  Hash 哈希                Bitfield 位域  

- 使用方式  
  CLI(command line interface) 使用命令行工具来使用redis  
  API(Application Programming Interface) 程序接口  
  GUI(Graphical User Interface) 使用图形化工具

- 设置数据  
  `set name akane`  
  `get name`  
  `del name` => 删除后输出nil  
  `exists name` => 不存在输出0  
  `keys` => 查看数据库中有哪些键(*所有键)  
  `flushall` 删除所有键  
  `ttl name` 查看键的过期时间(-1代表未设置)  
  `expire name` 设置键的过期时间  
  `setex name (过期时间) value` 设置一个有过期时间的键值对  
  `setnx` 只有当键不存在时才设置键的值，否则不做任何动作  

- List  
  `lpush/rpush 列表名 元素` 将元素添加到列表的头或尾  
  `lrange 列表名 起始位置 结束位置` (从0开始，-1代表列表中最后一个元素 )  
  `llen 列表名` 查看列表长度   
  `ltrim 列表名 索引头 索引尾` 删除列表中不在给出索引范围内的元素  

- Set(不能重复，重复添加会返回0--false)  
  `sadd 集合名称 元素` 将元素添加入集合  
  `smsmber 集合名称` 查看集合中的元素  
  `sismember 集合名称 元素` 查看元素在不在集合中  
  `srem 集合名称 元素` 删除成功返回1  
  `scard 集合名称` 获取集合元素  
  `sinter 集合1 集合2` 获取两个集合的交集  
  `sunion 集合1 集合2` 获取两个集合的并集  
  `sdiff 集合1 集合2` 获取在集合1中存在，集合2中不存在的  

- SortedSet/zset  
  `zadd 集合名 分数(排序依据) + 名称`  
  `zrange result 第..名 至 第..名` 查看第m名到第n名之间  
  `zrange result 第..名 至 第..名 withscores` 查看带分数的排名  
  `zscore result 名称` 查看对应名称的分数  
  `zrank result 名称` 查看对应名称的排名(按分数从小到大)  
  `zrevrank result 名称` 查看对应名称的排名(按分数从大到小)  

- 哈希(键值对的集合)  
  `hset 集合名 键值对的名称 键值对的值`  
  `hget 集合名 键值对的名称` 返回键值对的值  
  `hgetall 集合名` 返回哈希中所有的键值对  
  `hdel 集合名 键值对的名称` 删除键值对  
  `hkeys 集合名` 返回所有键值对的名称  
  `hlen 集合名` 返回所有键值对值的数量  

- 发布订阅模式   
  `publish 名称 内容 ` 发布  
  `subscribe 频道名称` 订阅频道   

- stream流  
  解决了消息无法持久化，无法记录历史消息的问题  
  `xadd 名称 键值对`(*会自动为消息标序号)  
  `xlen 名称`查看消息数量  
  `xrange`  
  `xdel 名称 消息的编号(添加时会返回)`  
  `xread count 2 block 1000 streams 名称 0` 从头开始一次读取两条消息，如果没有消息阻塞1s  

- 地理位置信息  
  `geoadd city 经度 纬度 城市名`  
  `geopos city 城市名` 返回表示经度和纬度的数组  
  `geodist city 城市1 城市2` 返回直线距离  
  `geosearch city frommember byradius 半径` 搜索距离指定城市规定半径内的城市  

- hyperloglog    
  如果一个集合中所有元素不重复，那么集合的基数就是集合中元素的个数。使用随机算法计算，放弃对精确度的要求追求效率   
  `PFADD 名称 所有值`  
  `PFCOUNT 名称`    
  `pfmerge result 名称1 名称2`    

- 位图
  字符串类型的扩展，使用string类型表示一个bit数组  
  `bitcount 名称`统计key中有多少个1  
  `bitpos 名称 0/1`统计key中第一个0/1出现的序号  

- 位域  
  `bitfield player:1 set u8 #0 1`  
  `get player:1`  
  `bitfield player:1 get u8 #0`  

- 事务  
  支持在一次redis语句中执行多条指令  
  使用multi与exec结合实现  
  multi会开启一个队列，将所有任务放入，由exec执行。任何一个任务执行失败不会影响其他命令  
  `multi` 进入tx模式，之后输入的所有命令会返回queued，等待exec执行  

- redis持久化
  RDB方式(redis database) 在时间间隔内将内存上的数据写入磁盘
  AOF方式(append only file) 执行写命令时，不仅将数据写入内存，还将数据写入aof文件中。通过日志形式记录每一个写操作。重启redis时，会重新执行aof文件中的所有操作重新创建数据库

- 主从复制
  将主节点数据复制到从节点(单向)主节点负责写操作，从节点负责读操作。主节点发生变化后，将自己的数据发给从节点，接收后从节点更新数据

- 哨兵模式
  主节点宕机时会影响系统。哨兵模式可以解决问题。 
1. 监控：不断发送命令检查节点是否正常，
2. 通知：出现问题之后发布订阅通知子节点
3. 自动故障转移：将从节点升级为主节点并将其他从节点指向这个子节点