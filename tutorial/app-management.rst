应用管理
#############

SAE为用户提供后台调试、管理、监控等功能。操作入口：应用—>应用列表，点击对应的应用，即可进入应用管理页面，页面左侧有功能导航栏，包括应用信息、应用管理、应用调优、服务管理、外部服务，详细介绍如下。

汇总信息
============

包括用户Key信息及访问的统计信息（如PV、UIP）。Key信息在一些关键操作中需要用到，访问信息统计可提供最长一个月（任意月份）的变化曲线。

.. image:: /images/pvuip.jpg

PV: 是指对\*.php等动态脚本的请求计数，对于以下格式的静态资源的请求不计算在内：js\|css\|jpg\|gif\|png\|swf\|ico

UIP: 是指一个用户通过浏览器发出的请求，在十分钟内该用户IP只算一次。注意：通过cURL的请求因为是不带Cookie的header，因此同一IP来源的cURL多次请求会计算多次。

预算设置
===========

设置应用或某服务每天消耗云豆的最高限额，若设置，则应用或某服务消耗完设置的最高限额后，应用或某服务即被禁用。可对应用设置每天总预算，也可展开“详细设置”，对服务设置单独的预算。若不设置某服务的预算，则该服务每天最多能消耗的云豆数为应用的总预算(应用设置了预算)或账户剩余云豆(应用未设置预算).

.. image:: /images/budget.jpg

资源报表
===========

可查看应用每日、每周、每月的资源消耗。

.. image:: /images/report.jpg

服务状态
==============

可监控近10天的各服务的状态，如下图。

.. image:: /images/service-status.jpg

应用设置
==============

可更新应用的二级域名、应用名称及应用描述，如下图。 

.. image:: /images/app-manage.jpg

成员管理
===========

可组建、管理团队进行应用开发，通过成员管理邀请SAE注册用户一起参与、管理当前应用等，若有成员变动，也可进行对已有成员进行删除，详见下图。

.. image:: /images/team-manage.jpg

.. note:: “管理者”和“参与者”或“自定义”中有“代码管理权限”的成员具有代码管理权限(通过svn修改代码)

代码管理
==========

可实现对代码进行管理，可选择是否开启 xhprof 调试功能、错误提示功能、也可创建、编辑、删除某一版本代码等。对于开发或测试中的应用，建议开始“错误提示”。开启“ xhprof 调试”后，每一次页面请求都会在domain名为xhproxy的 storage 中生成一个调试页面，比较耗费http cpu资源和占用 storage 容量，当遇到性能问题时才开始“ xhprof 调试”，否则应用会消耗云豆。

.. image:: /images/code-manage.jpg
   
管理记录
==========

记录用户对应用管理操作的日志，如部署代码、创建domain、设置默认版本、关闭开启某一项服务或功能等操作的日志信息。

.. image:: /images/manage-log.jpg
   
日志中心
============

提供HTTP后台日志、 Mysql 慢查询、 Mail 、 Cron 、 Taskqueue 日志，用户可以通过日志进行分析，锁定应对哪里进行调优；

服务管理
===============

本模块介绍各项服务（包括 Mysql 、Storage 、Memcache 、Cron 、Image 、FetchURL 、Mail 、TaskQueue 、DeferredJob 、Counter ）的使用说明、监控、相关设置等信息。
