
[文章引用1](https://blog.csdn.net/hao_yunfeng/article/details/82392261)
[文章引用2](https://mp.weixin.qq.com/s/_bk2JVOm2SkXfdcvki6-0w)
[文章引用3](https://blog.csdn.net/dianxiaoer20111/article/details/103034197)
[文章引用4](https://www.cnblogs.com/boothsun/p/8970952.html)
## 1.mysql索引
> ### 索引类型和使用场景
* Fulltext
    * 只有myisam引擎支持
    * 对中文支持很差，分词基于英语（中文插件Mysqlcft）
* #### R-Tree
    R-Tree在MySQL很少使用，仅支持geometry数据类型，支持该类型的存储引擎只有MyISAM、BDb、InnoDb、NDb、Archive几种。

* ### hash索引
    * 一次到位的查找，不像btree一点点遍历，
    * 不支持 <、>、=、in 这些操作，无法排序
    * hash过多也会导致速度下降
    * 不支持联合索引的最左前缀匹配规则
    * 比较适合确定值的匹配

* ### btree索引
    * B-Tree是最常见的索引类型，所有值（被索引的列）都是排过序的（参见b树搜索原理），每个叶节点到跟节点距离相等（因为b树的所有叶节点在最底层）。所以B-Tree适合用来查找某一范围内的数据，而且可以直接支持数据排序（ORDER BY）
    * B-Tree在MyISAM里的形式和Innodb稍有不同：
        * MyISAM表数据文件和索引文件是分离的，索引文件仅保存数据记录的磁盘地址
        * InnoDB表数据文件本身就是主索引，叶节点data域保存了完整的数据记录
> ### 索引使用坑
* ### 左前缀匹配
* ### 回表查询
* ### 联合索引
    联合索引又叫复合索引。两个或更多个列上的索引被称作复合索引
* ### 索引下推
    mysql5.6新增Index Condition Pushdown Optimization。默认开启，使用SET optimizer_switch = 'index_condition_pushdown=off';可以将其关闭。

## 2.mysql集群
> ### 配置
* 主服务器开启 log-bin 参数
* 从服务器只能连接主服务器，主从服务器ID要不一样

> ### 同步方式（粒度）
* statement: 会将对数据库操作的sql语句写道binlog中
* row: 会将每一条数据的变化写道binlog中。
* mixed: statement与row的混合。Mysql决定何时写statement格式的binlog, 何时写row格式的binlog。

> ### 同步流程
    mysql主服务器的变化会写入binlog，从服务器连接主服务器时，主服务器会为每个从服务器开启同步线程。
    从服务器上开启I/O线程和sql线程，I/O线程负责接收主服务器的binlog dump线程发来的数据并写入relay log，sql线程读取replay log并更新数据库。

> ### 主从复制延迟解决（只是减压加速，没有完美解决）
* 业务上redis缓存减少主库压力
* 企业级ssd硬件，关闭swap，主从服务器放一个光交换机内。
* 从服务器配置sync_binlog=0，从服务器配置logs-slave-updates不记录主服务器更新日志
* 从服务器禁用binlog
* 从服务器配置innodb_flush_log_at_trx_commit =2

> ## 附录
1. 经测试，mysql5.7 百万级数据的情况下，select count(*) 比select count(数字主键) 还要快的多
1. mysql多主的时候，协程A往里面写数据，如果是向其中一个实例发送了锁行C的操作，协程B以及其他协程在 另外的mysql主服务器实例上发送了锁行C的操作，那么此时是如何解决竞争的呢？如果不能解决竞争，那么如何解决脏写呢？
