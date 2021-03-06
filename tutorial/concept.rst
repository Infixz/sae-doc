关键概念
################

开发者、客户、用户
=======================

本文档中会经常提到如下几个角色。

+ **开发者** 是云计算服务的使用者，因此在文档中提到的 **客户** 等同于 **开发者** 。
+ **用户** 是开发者所推出产品的直接使用者，也是云计算服务的间接使用者。

.. image:: /images/developer-user-client.jpg

负载均衡、Web服务池、Web服务器、运行环境、服务
=================================================

+ **负载均衡** 是SAE云计算服务的最外层，负责接收应用的请求，并将请求转发到应用所在的 **Web服务池** 去由其做实际的请求处理。
+ 一个 **Web服务池** 一般由一组 **Web服务器** 组成， **负载均衡** 使用Round-Robin算法在应用的 **Web服务池** 中选择一个健康的 **Web服务器** 来来处理该应用的请求。
+ **Web服务器** 负责解析 **负载均衡** 转发过来的请求，并调用对应开发者编写的应用代码来处理这个应用的请求，并将用户代码生成的回复返回给 **负载均衡** ，最终由 **负载均衡** 返回给请求者。
+ 目前各语言环境使用的 **Web服务器** 分别是：Apache（PHP）、Jetty（Java）、Direwolf（Python） [1]_ 。
+ **运行环境** 是用户代码执行的环境，譬如 **Web服务器** 的版本、本地IO权限、可用的扩展及其版本、SDK等。本文档中一般会用 **PHP运行环境** 、 **Java运行环境** 、 **Python运行环境** 来指代各语言代码的执行环境。
+ SAE不需要开发者去部署和运维MySQL、Memcached等程序，而是将这些程序通过 **服务** 的形式以接口提供给开发者去使用。

.. image:: /images/lb-pool-server-runtime-service.png
   :width: 700px

.. [1] Direwolf是SAE为Python语言环境开发的Web服务器，支持WSGI协议。

应用目录、应用版本目录、应用版本
===================================

在SAE中一个应用可以部署多个版本，每个版本在SVN中对应一个数字为名的目录。下面是一个应用的典型目录结构： ::

    $ tree myapp
    myapp
    |-- 1
    |   |-- build-doc.sh
    |   |-- config.yaml
    |   |-- index.wsgi
    |   `-- README.md
    `-- 2
        |-- config.yaml
        `-- index.wsgi

    2 directories, 6 files

我们称顶级目录 `myapp` 目录为 **应用目录** ，而 `myapp/1` 为 **应用版本目录** 。

**应用版本** 是指用户部署的同一个应用的多个版本的代码，对应 **应用版本目录** 。

因为SVN作为版本管理系统，本身有个版本的概念，为了防止和SAE的 **应用版本** 相混淆，在本文档中我们使用 **SVN版本** 来指代SVN的版本。

