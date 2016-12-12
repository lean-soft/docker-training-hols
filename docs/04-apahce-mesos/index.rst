应用编排平台 Apache Mesos & Marathon
------------------------------------

Mesos是Apache下的开源分布式资源管理框架，它被称为是分布式系统的内核。Mesos最初是由加州大学伯克利分校的AMPLab开发的，后在Twitter得到广泛使用。

本实验将使用微软云计算平台Microsoft Azure运行的Azure容器服务(ACS)来运行Mesos(DCOS)集群，展示其应用编排能力。Azure 容器服务特别为 Azure 优化了常用开源工具和技术的配置。你获得的开放解决方案为你的容器和应用程序配置提供可移植性。你只需选择大小、主机数和 Orchestrator (编排)工具选项，容器服务会为你处理所有其他事项。当前Azure容器服务支持以下编排工具：

- DCOS (Apache Mesos)
- Google Kubernetes 
- Docker Swarm

.. figure:: images/acs-cluster-new.png

本实验所使用的DCOS集群架构如下，为了演示用途，我们使用一个3节点的DCOS集群，其中包括

- 1个master节点用于管理员连接操作
- 1个公开的agent节点用于对外提供http服务
- 1个私有的agent节点用于集群内部服务

.. figure:: images/dcos.png

**练习列表**

.. toctree::
   :titlesonly:

   01-connect
   02-run-simple-job
   03-run-php-sample