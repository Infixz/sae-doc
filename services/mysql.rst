MySQL
#########

服务概述
=========

..  这个文档是由原来的MySQL和RDC整合而成的，实现的细节没有必要暴露给开发者，开发者只需要知道这是一个高可用的MySQ数据库集群就行了，没有必要了解我们具体的实现细节，关于SAE MySQL数据库集群的架构实现可以加一个链接到某个介绍数据库集群架构的ppt

MySQL是SAE为用户提供的分布式MySQL数据库集群服务，可以支持百万级的数据库访问。SAE的MySQL数据库集群服务有以下特点：

1. 通过其强隔绝性为开发者提供了更高的安全性,保障开发者的数据安全。
2. 当某个数据库发生故障和延迟时,系统能够自动切换,保证服务稳定可靠。
3. 能通过对SQL的智能预处理降低用户触发分钟配额的可能。
4. 整体数据库集群性能提升。

SAE平台为支持几乎所有MySQL的特性。目前支持MyISAM引擎，暂不支持InnoDB.需要注意的是SAE的MySQL管理页面显式开启才能使用。

目前MySQL服务对用户开放的操作：

+ select, insert, update, delete
+ create table
+ alter table
+ drop table
+ index

您可以通过在MySQL的管理页面中集成的PhpMySQLAdmin里创建数据库和数据表。

数据导入、导出
================

SAE支持两种方式数据导入、导出：

+ 对于4M以内的小数据，用户可以通过PhpAdmin在线同步的导入、导出
+ 对于4M以上的数据，我们不推荐用户使用PhpAdmin同步的执行（实际因为php等限制用户也无法执行该操作），而是强烈建议使用 `DefferedJob <deferredjob.html>`_ 来完成MySQL数据的导入。

数据备份
============

SAE提供MySQL服务的高可靠性，根据SLA需求不同，一个一主一从到一主N从的数据库HA，用户如果出于可靠性的考虑，SAE不推荐用户自行做数据备份。当然用户也可以使用数据导出功能自行备份。

服务限制
=============

SAE的MySQL服务作为面向公有云计算平台的分布式数据库集群，对安全性要求极高，系统会预判用户执行的SQL语句，并提前拦截可能损伤系统的SQL语句，被拦截的查询会按照按照MySQL标准的错误返回方式返回，用户可以通过获取MySQL的错误码和错误信息来判断是否触发了相关限制。

SAE的MySQL服务目前屏蔽SQL的情况有三种：

1、SQL语句性能低下，执行效率低，或者表过大或者结构极其复杂，在其上执行的操作可能危害到整个系统的安全。这里涉及的SQL语句包括：

    + select
    + update
    + insert
    + delete
    + replace
    + create table
    + alter table
    + create index

2、某个应用的当前并发SQL执行时间和过大。SAE的MySQL服务通过应用的MySQL的并发SQL执行时间而不是并发连接数来限制应用对MySQL的限制应用对资源的使用。从而使得SQL效率高的应用能够获得更大的并发支持，而SQL执行效率低的用户则可能不会获得大的并发，以鼓励用户优化自己的SQL，提高SQL的执行效率，减少对系统的消耗。

    举个例子，如果SQL并发执行时间和为10000ms：

    A用户的平均每条SQL消耗100ms，那么A获得的最大并发为100
    B用户的平均每条SQL消耗1000ms，那么B获得最大并发仅为10

3、某个应用的SQL产生了过多的慢查询。SQL执行时间超过1秒，即为慢查询。

=============================== =========================================== ===============
限制                            相关错误信息                                数值
=============================== =========================================== ===============
单表的最大行数                  Table has too many rows                     10,000,000行
库的最大表数量                  Database has too many tables                512个
不支持的存储引擎类型            Not support table type  memory temporary
不支持的内置函数                Not support function                        sleep
最大外排序的行数                Filesort on too many rows                   65,536行
最大无索引的操作行数            无                                          200,000行
查询的最大操作行数              Select on too many rows                     1,000,000行
更新的最大操作行数              Update on too many rows                     1,000,000行
删除的最大操作行数              Delete on too many rows                     1,000,000行
创建索引时允许的表的最大行数    Create index on big table                   500,000行
修改表结构时允许的表的最大行数  Alter table on big table                    500,000行
SQL并发执行时间和(读库)         Operations take too much time cost          500,000毫秒
SQL并发执行时间和(写库)         Operations take too much time cost          200,000毫秒
警报阈值百分比                  无                                          80%
表主键及聚簇索引奖励系数        无                                          1024倍
=============================== =========================================== ===============

自定义错误码
===============

+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|错误码| 错误信息                              | 说明                          | 建议                                                 |
+======+=======================================+===============================+======================================================+
|13000 | Not support multi statements          | 不支持一个字符串多条SQL语句   | 无                                                   |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13001 | Select on too many rows               | 查询的表记录超过了限制 [1]_   | 无                                                   |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13002 | Update on too many rows               | 更新的表记录超过了限制 [1]_   | 无                                                   |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13003 | Delete on too many rows               | 删除的表记录超过了限制 [1]_   | 无                                                   |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13004 | Create index on big table             | 在一个过大的表上创建索引      | 使用SAE DefferedJob离线任务队列执行                  |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13005 | Alter table on big table              | 在一个过大的表上改变表结构    | 使用SAE DefferedJob离线任务队列执行                  |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13006 | Operations take too much time cost    | 超过SQL并发执行时间和         | 优化SQL语句，或者购买更大的并发支持                  |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13007 | Filesort on too many rows             | SQL导致高时间复杂度的外排序   | 优化SQL语句                                          |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13008 | Table has too many rows               | 单表行数超过规定上限          | 分表以降低表内的记录数                               |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13009 | Database has too many tables          | 用户当前表数目已达到规定上限  | 降低表的数量（可以通过MySQL的跨应用授权使用多库）    |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13010 | Not support table type                | 试图创建不支持的表类型        | 咨询SAE，了解支持的表类型                            +
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13011 | Not support table optimization        | 试图执行optimize table语句    | 去掉该语句                                           +
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13012 | Not support function                  | 试图执行禁用函数(sleep)       | 不执行该函数                                         +
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13013 | Scanned too many databases when       | 查询INFORMATION_SCHEMA        |                                                      |
|      | querying INFORMATION_SCHEMA           | 时导致过多的跨库扫描          | 查询时INFORMATION_SCHEMA时显式指明库和表             |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13014 | Too complicated sql case uncacheable  | 过于复杂的语句导致不可被cache | 降低语句复杂度                                       |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13044 | Backends connection error             | 连接时出现未知错误            | 稍后重试，连续失败时，请向官方反馈                   |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13045 | Backends connection timeout           | 连接时超时                    | 稍后重试，连续失败时，请向官方反馈                   |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13046 | No available backends                 | 没有可用的后端                | 向官方反馈                                           |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+
|13047 | Be banned (maybe out of quota)        | 因为慢查询过多等原因导致被禁用| 优化SQL语句                                          |
+------+---------------------------------------+-------------------------------+------------------------------------------------------+

.. [1] 这里的考虑因素有表结构、表行数、带没带索引、有没有limit、有没有join    调整表大小，或者优化SQL语句
