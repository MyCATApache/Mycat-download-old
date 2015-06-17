###MyCat Release Notes
####MyCat 1.4-RC
###新功能
+ 增加日期范围hash分片算法
+ RELOAD @@CONFIG只reload基本配置，不重新加载数据源，RELOAD @@CONFIG_ALL重加载所有配置
+ 支持SequoiaDB
+ 增加mycat私有仓库
+ 添加having 支持。目前只支持 =，>，<，!=，>=，<=

###改进和修复
+ Update BufferPool.java
+ 修改只考虑可见性，未考虑同步的newCreated变量
+ 缓存正则表达式
+ 修复mongodb的acceptsURL
+ jdbc 驱动 bug
+ 防止拿到连接后，执行sql时。连接被后端检查给关闭掉
+ 修改心跳检查bug
+ 修复between and 字段带别名时路由错误的bug
+ Join Key 不在Select 的第一个字段的bug
+ 解决客户端JDBC连接到管理接口报 unsupported statement 错误。
+ ShareJoin bug


####MyCat 1.4-beta
###新功能
+ 支持start transaction与begin命令开启事务
+ 支持多层嵌套or语句，并增加单元测试
+ 范围取模分片配置文件
+ 增加范围取模分片配置
+ 在分片规则中添加JCH分片配置
+ 添加跳增一致性哈希分片的测试类
+ 增加跳增一致性哈希分片算法(分片思想源自Google在2014年公开的论文，该算法几乎不占内存只需几个寄存器用于计算，速度比一致性哈希更快，横向扩展时数据迁移量大大减少。)
+ 支持范围求模算法的between路由
+ 支持DESCRIBE和DESC
+ 表名支持引号[`]

###改进和修复
+ 优化重复代码逻辑
+ 增加对自增序列的非空判断，如果没有配置，则抛出配置异常，提示没有序列的定义，避免直接抛出空指针异常。
+ 修复distinct的带别名出错的bug
+ 去掉WhereUnit中的parent，没用到
+ Downgrade JDK from 8 to 7
+ Update PartitionByJumpConsistentHash.java
+ 去除不必要的cast
+ 进一步改进英语表达
+ 数据库Sequnce 异常的时候返回客户端具体错误信息，如存储过程不存在
+ 修改jdbc的心跳数据，show @@heartbeat
+ 修复aio在高并发时出现的bug
+ 修复关于JDBC方式连接后端连接占满数据库的问题 #312 中的2,3问题
+ 去掉不必要的sysout
+ 修改jdbc连接心跳检查active计算错误，show @@backend没有算jdbc连接，  
+ select sum(a-b) from c where id=100语句没有查到返回null时候，合并数据的时候报nullpointexception错误
+ 修复自增id的insert里面的值被强制转大写的bug
+ 修复insert序列解析错误和序列next value for的大小写与空格支持
+ 修复desc语句导致单元测试卡死bug
+ 修复jdbc心跳bug导致不断创建连接的bug
+ 修复druidmanager引起的jdbc连接失败问题
+ 新增，删除 修改全局表 返回受影响的行不对(应该只计算一个节点的)
+ 修复在hibernate生成的sql中group by语句中的字段和select语句中的字段的别名不一致（但是同一字段）时报错
+ 修复null被当做空串的bug
+ fix a bug, which resulted in RW seperation is unavailable
+ 文件清理，增加versions.java自动生成的功能，修正show @@versions的输出。
+ add TestCase:delete of global table route to every datanode defined
+ 全局表的delete和update紧急Bug



####MyCat 1.4-alpha
###新功能
+ 增加时间戳的全局唯一序列
+ 使用jdbc 方式连接 ， 引入 druid数据源与数据库监控
+ 增加对or条件的路由支持
+ 引入load data
+ 完整实现mysql压缩协议的支持
+ 增加压缩协议的配置选项
+ 支持跨分片的avg函数以及avg与group by组合
+ catlet支持jar
+ 支持schema内，未配置的表走默认dataNode，已配置的走分片规则
+ 支持批量插入sequence生成
  * 修改：
  * MyCATSequnceProcessor.executeSeq 方法中获取sequence 由FoundationDB方式改为Druid方式实现。
  * 增加一个catlet，BatchInsertSequence支持批量插入语法自动生成sequence ID。
+ schema.xml中的Table上的DataNode支持数据库均匀分布，如
``` <table name="xxx"
dataNode="distribute(dn1$0-372,dn11$0-372)" rule="latest-month-calldate"
/>```
+ limit自动转换原生分页

###改进和修复
+ 修改reload为非阻塞
+ 修复 自增主键，表写错大小写导致mycat无限循环获取
+ 修复DataMergeService跨线程时的异常处理
+ 增强SingleNodeHander的异常处理
+ 升级druid和univocity-parsers版本
+ 修改E-R为非阻塞
+ fixed a bug for StringUtil.getTableName(String)
+ add two test case for StringUtil.getTableName
+ fix a bug, if 'insert into' was following a '\r' or '\n'.
edit StringUtil.getTableName(String), change "==' '" to "<=' '"
+ 避免insert判断时是非空格字符
+ 修复multi insert not provided误判
+ 修正后端jdbc方式时，delete语句解析出错的情况
+ fix a bug when 'sequence conf not found'
+ modify charset index 45 for utf8mb4
+ 解决空串被当做null的bug
+ 修复load data 默认节点时的npe异常
+ 修改心跳检查Bug，没有判断数据库切换类型
+ fix order by语句中带'符号时报错找不到字段问题。java.lang.IllegalArgumentException: all columns in order by clause should be in the selected column list!`create_time`
+ entry.getKey().toUpperCase() 不用 Upper了，容易引起误解，以为大小写的问题
+ 去掉中文提示信息改成all columns in order by clause should be in the selected column list!
+ fix a bug on reloading sequence_db_conf.properties
+ order by别名的一种场景语句解析bug，如语句SELECT c.name as cname, Count(*)   AS col_1_0_
 from  customer c group by c.name
+ 父子表场景，子表select语句路由到多个节点报错。对应测试用例DruidMysqlRouteStrategyTest.testERRouteMutiNode
+ 带计算表达式的order by语句，select fee-3 as fee3,id from travelrecord order by fee-3
+ 增强jdbc和multiNodeQueryHandler的异常处理
+ Mycat JDBC连postgresql不能支持事务提交和回滚
+ 修改releaseConnection方法
+ 修改显示版本为1.4，升级依赖的jar为最新release版
+ 重构HeatBeat逻辑和代码，引入MySQL主从状态监控的心跳和切换逻辑
+ fix a bug on crossing sharding with 'in clause' sql
+ fix a bug on executing reload @@config, which would not reload sequence_db_conf.properties. this commit has fixed it.
+ removed bucketMapPath from PartitionByMurmurHash.java
+ MycatPrivileges单实例化 及 StringUtil增强
+ MYCAT_HOME未设置时，默认设置为当前路径。
+ 提交assembly
+ 修复encose的空指针异常
+ SONAR问题修复
+ 优化去除AIOOutputWriter
+ 解决AIO的writePendingException
+ 支持未压缩的小包粘连
+ 删除几个fdb遗留无用代码
+ 移除fdbParse相关的包，代码
+ 修复单节点ok包packid多加一次的bug
+ 实现load功能
   * 重构部分代码
   * 优化load data infile的性能
   * 优化load data性能
   * 优化load data数据包得识别，避免误认2.优化load data全局表，避免路由查找3.修复了一个在同一个连接上执行load data几百次会出错的bug
   * 修复load data 超大文件时的bug
   * 支持load data的自定义列分割符换行符引号等，增强了load data handler的异常处理,优化去掉aiooutputwriter的多余部分
   * 优化load data 的clear调用
   * 支持load data的自定义列分割符换行符引号等，增强了load data handler的异常处理
   * 优化去掉部分load data日志
+ fix 引用类的问题
+ 修改jdbc的resultset转换RowDataPacket的方式，可以提高30%的性能
+ 修改jdbc的sql执行为多线程执行，避免jdbc跨多个节点时性能成倍消耗
 


####MyCat 1.3.0.3-release
+ JDBC方式， 字段有别名，报错 java.lang.IllegalArgumentException: group by

+ session移除已关闭的连接

+ 增加LockTable和UnlockTables语句支持

####MyCat 1.3.0.3-alpha
+ order by 带函数的 的bug,如  order by date_format(traveldate, '%y-%m-%d')

+ 1.3.0.1修改order by ,group by 别名的bug

+ 增加pluginManage,修正eclipse导入lifeCycle错误

+ 修正多节点merge排序结果不正确bug

+ 修正skip过慢问题

+ 排序优化

+ 注解增加/*#mycat:格式;where condition1 or 1=1之类的永真条件路由错误问题

+ 修正最大堆排序结果不正确bug

+ between语句范围路由的支持

+ 修正druid group by 语句字段带表名提示字段找不到的问题

+ 解决druid主键in查询只匹配一个条件的问题

+ fixed:#87;mycat注释兼容mysql注释

+ 解决druid解析器order by字段名称与select语句字段名称不一制问题

+ 解决group by order by问题;解决select for update问题;修改错误提示信息

+ 解决druid解析器count语句问题

+ 修正Druid解析器添加分片字段错误



####MyCat 1.3.0.1

>git:

Fix bug:

1. 数据报文在某些特殊情况下没有实时写入Socket导致时延（重要）
2. Druid解析器的一些空指针、Limit处理、以及单引号的Bug

#####存在的缺陷

1. 目前还没有发现新的Bug

---
####MyCat 1.3


>git:e3111dfdee64163bd15aa6022171ad34c3aad455

特性:

1. 一致性hash分片支持
2. 默认Druid解析器提升性能
3. 人工智能Catlet 编程实现跨分片Join ，143行Demo代码可供参考
4. 性能大幅提升，相对1.2 插入性能提升100%，查询性能提升50%

Fix bug:

1. checkWriteBuffer可能分配不够的空间，存在潜在Bug()
2. Mysql 连接同步优化，以及修改Timeout 超时的问题
3. 主键缓存优化
4. 后端连接事件优化

#####存在的缺陷
- 比较重要的一个缺陷是数据报文在某些特殊情况下没有及时写入Socket导致时延，已经在1.3.0.1修复
	
 
| Author    |                   |                          |                             |
| :-------- | :---------------  | :----------------------- |:--------------------------- |
|姓名 |群名片|QQ|任务|
| Marshy  |        ||负责任务跟踪，提供了机器供Mycat官网使用，是Mycat美女大总管|
| |成都研发  ||分片排序优化模块       |
|王灯武 |武  | 15297449|引入新的Druid解析器    |
| 吴京润|一坨  |744142084| 一致性Hash分片和工具       |
| 海王星  | 负责github和版本发布，持续化构建       |||
| 石头狮子  | 写文档、实现了HA Datasource工具等       |||
| 冰风影  | 修复JDBC连接缺陷，开始NoSQL模块开发       |||
| .Jagger  | 自增长主键实现和性能测试、参与性能优化过程|||
| 非非想  |Mycat官网第一版       |||
| 一觉醒来  |协助数据库调优       |||
| 赵闪|Cheris |694050364 |提供了性能测试报告       |||
|詹益峰|哈哈|758297924|Mycat in action文档汉译英|
|刘晓栋|晓栋|67814134|翻译Mycat in action|
|陈秀升|xiuson|443697052|Mycat in action翻译和Mycat-server readme.md中英文两部分|
| 其他友情客串者  |酱油师、xiuson、哈哈、BEN、Mr.ZHU、超仔、深海、广州-UFO、等很多同学 ||||


---

####MyCat 1.2

>MyCat 1.2 (svn:r515)

特性:

1. 增加了SQL拦截器的功能，默认的实现将Mysql转义字符进行了处理

Fix bug:

1.  事务隔离问题

| Author    |                    |
| :-------- | :---------------  |
| 新加入 林志强、林晁、齐朗晔、杨创、何智峰等（一些遗漏了真名的同学，可以来信）  | 全面系统测试，协助发现和解决了一些重要缺陷|
|孙娟(酱油师）|全局序列号更新       |
| (opencloudber）  | 排序归并的一些缺陷发现并修复    |
| 王金成(永不言败）   | 发现和修复一些缺陷       |
| 蒋常春（CC）  | 性能测试       |
| 白磊（first level ）  | 性能测试    |
| 李翔远（光电=ω=鼠标 ）  |  	时间范围分片函数以及between支持      |
| 黄进兵（疯鱼）  | 贡献字符串分片函数|
| 高权（IT坨坨）  |测试以及缺陷反馈|
| 吴志辉（Leader）  |MyCat-server       |
| rainbow 团队  |协助数据库调优       |
|Qing|MyCat-cluster|
|提婆谭（暂定）|MyCat-Balance|
|Michael@micmiu.com|MyCat-server|
|无影|MyCat-server|
|shenzhw|性能测试|
|小鱼|测试|
|阿德|排序算法|
|面朝大海|修复BUG|
|木糖醇患者|修复BUG|

#####很多志愿者在努力研发中，后期版本陆续列出贡献者信息
---
