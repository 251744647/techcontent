<properties
   pageTitle="云业务连续性 — 数据库恢复 | Azure"
   description="了解 Azure SQL 数据库如何支持云业务连续性和数据库恢复以及如何帮助保持运行任务关键型云应用程序。"
   keywords="业务连续性, 云业务连续性, 数据库灾难恢复, 数据库恢复"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.date="05/10/2016"
   wacn.date="06/14/2016"/>

# 概述：云业务连续性与使用 SQL 数据库进行数据库灾难恢复

业务连续性是指以弹性方式设计、部署和运行应用程序，以应对计划或非计划的中断事件，避免应用程序永久性或暂时性地无法执行其业务功能。非计划事件覆盖了从人为失误、到永久或暂时性的中断、再到区域性的灾难（这可能会导致特定 Azure 区域中出现大规模的机能损失）的整个范围。计划事件包括向不同的区域重新部署应用程序以及应用程序升级。业务连续性的目标是发生这些事件期间使应用程序能够继续正常运行，而只会对业务功能造成极小的影响。

若要探讨云业务连续性解决方案，你需要熟悉几个概念：

* **灾难恢复 (DR)：**还原应用程序正常业务功能的过程

* **预计恢复时间 (ERT)：**在发出还原或故障转移请求后，数据库完全可用之前预计持续的时间。

* **恢复时间目标 (RTO)**：在发生中断性事件后，应用程序完全恢复之前的最长可接受时间。RTO 用于度量故障期间的最大可用性损失。

* **恢复点目标 (RPO)**：在发生中断性事件后，应用程序在完全恢复时可以丢失的最大最近更新数量（时间间隔）。RPO 用于度量故障期间的最大数据丢失。


## 云业务连续性方案

以下是在规划业务连续性和数据库恢复时要考虑的关键方案。

###设计用于保持业务连续性的应用程序

我要构建的应用程序对我的业务而言至关重要。我希望设计和配置的应用程序在服务发生区域性的灾难故障时可以生存。我知道应用程序的 RPO 和 RTO 要求，现在想要选择满足这些要求的配置。

###在人为失误后恢复

我对应用程序的生产版本拥有管理访问权限。在日常维护过程中，我犯了一个错误，删除了生产中使用的一些重要数据。我想要快速还原这些数据，以减轻该错误造成的影响。

###在中断后恢复

我在生产环境中运行应用程序时收到了一条警报，其中指出，该应用程序所部署到的区域中发生了严重的服务中断。我想要启动恢复过程，以便在另一个区域中重新运行该应用程序，以减轻对业务造成的影响。

###执行灾难恢复演练

由于在中断后进行恢复需要将应用程序的数据层重新定位到其他区域，我想要定期测试恢复过程，并评估它对应用程序的影响，从而使应用程序随时保持工作状态。

###在不停机的情况下升级应用程序

我正在释放应用程序的一项重要升级。这涉及到数据库架构更改、部署其他存储过程，等等。此过程需要阻止用户访问数据库。同时，我想要确保升级过程不会导致业务运营出现重大中断。

##业务连续性功能

下表显示了各服务层的云业务连续性功能差异：

| 功能 | 基本层 | 标准层 |高级层
| --- |--- | --- | ---
| 时间点还原 | 7 天内的任何还原点 | 14 天内的任何还原点 | 35 天内的任何还原点
| 异地还原 | ERT < 12 小时，RPO < 1 小时 | ERT < 12 小时，RPO < 1 小时 | ERT < 12 小时，RPO < 1 小时
| 活动异地复制 | ERT < 30 秒，RPO < 5 秒 | ERT < 30 秒，RPO < 5 秒 | ERT < 30 秒，RPO < 5 秒

提供这些功能是为了解决前面列出的方案。有关如何选择特定功能的指导，请参阅[业务连续性设计](/documentation/articles/sql-database-business-continuity-design/)部分。

> [AZURE.NOTE] ERT 和 RPO 值是工程目标，并仅提供指导。它们不属于 [SQL 数据库的 SLA](/support/legal/sla)


###时间点还原

[时间点还原](/documentation/articles/sql-database-point-in-time-restore/)旨在将数据库还原到以前的某个时间点。它使用服务自动为每个用户数据库维护的数据库备份、增量备份和事务日志备份。此功能适用于所有服务层。基本、标准和高级数据库的还原期限分别为 7 天、14 天和 35 天。有关如何使用时间点还原的详细信息，请参阅[在人为失误后恢复](/documentation/articles/sql-database-user-error-recovery/)。

###异地还原

[异地还原](/documentation/articles/sql-database-geo-restore/)也适用于基本、标准和高级数据库。当数据库由于它所在的区域发生事故而不可用时，异地还原会提供默认的恢复选项。与时间点还原一样，异地还原依赖于异地冗余的 Azure 存储空间中的数据库备份。它会从异地复制的备份副本中还原，因此可以灵活应对主要区域中的存储中断。有关如何使用异地还原的详细信息，请参阅[在中断后恢复](/documentation/articles/sql-database-disaster-recovery/)。

###活动异地复制

[活动异地复制](/documentation/articles/sql-database-geo-replication-overview/)适用于所有数据库层。它专为恢复要求超出了异地还原的能力的应用程序而设计。使用活动异地复制，最多可以在不同区域中的服务器上创建四个可读辅助数据库。可以启动到任何辅助数据库的故障转移。此外，活动异地复制可用于支持应用程序升级或重定位方案，以及只读工作负荷的负载平衡。有关如何在不停机的情况下实现应用程序升级的详细信息，请参阅[在不停机的情况下升级应用程序](/documentation/articles/sql-database-business-continuity-application-upgrade/)。

<!---HONumber=Mooncake_0530_2016-->
