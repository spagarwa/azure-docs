---
title: Pricing tiers for Azure Database for PostgreSQL
description: This article describes the pricing tiers for Azure Database for PostgreSQL.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2019
---

# Azure Database for PostgreSQL pricing tiers

You can create an Azure Database for PostgreSQL server in one of three different pricing tiers: Basic, General Purpose, and Memory Optimized. The pricing tiers are differentiated by the amount of compute in vCores that can be provisioned, memory per vCore, and the storage technology used to store the data. All resources are provisioned at the PostgreSQL server level. A server can have one or many databases.

|    | **Basic** | **General Purpose** | **Memory Optimized** |
|:---|:----------|:--------------------|:---------------------|
| Compute generation | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memory per vCore | 2 GB | 5 GB | 10 GB |
| Storage size | 5 GB to 1 TB | 5 GB to 4 TB | 5 GB to 4 TB |
| Storage type | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Database backup retention period | 7 to 35 days | 7 to 35 days | 7 to 35 days |

To choose a pricing tier, use the following table as a starting point.

| Pricing tier | Target workloads |
|:-------------|:-----------------|
| Basic | Workloads that require light compute and I/O performance. Examples include servers used for development or testing or small-scale infrequently used applications. |
| General Purpose | Most business workloads that require balanced compute and memory with scalable I/O throughput. Examples include servers for hosting web and mobile apps and other enterprise applications.|
| Memory Optimized | High-performance database workloads that require in-memory performance for faster transaction processing and higher concurrency. Examples include servers for processing real-time data and high-performance transactional or analytical apps.|

After you create a server, the number of vCores, hardware generation, and pricing tier (except to and from Basic) can be changed up or down within seconds. You also can independently adjust the amount of storage up and the backup retention period up or down with no application downtime. You can't change the backup storage type after a server is created. For more information, see the [Scale resources](#scale-resources) section.


## Compute generations and vCores

Compute resources are provided as vCores, which represent the logical CPU of the underlying hardware. Currently, you can choose from two compute generations, Gen 4 and Gen 5. Gen 4 logical CPUs are based on Intel E5-2673 v3 (Haswell) 2.4-GHz processors. Gen 5 logical CPUs are based on Intel E5-2673 v4 (Broadwell) 2.3-GHz processors. Gen 4 and Gen 5 are available in the following regions ("X" denotes available). 

> [!IMPORTANT]
> Beginning December 12, 2018, new customers will not be able to provision compute generation 4 servers in Brazil South, Canada Central, Canada East, East Asia, East US 2, Central India, West India, Japan West, North Central US, West US. Previously created compute generation 4 servers will be migrated to compute generation 5 starting February 1, 2019 in these regions.

| **Azure region** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Central US |  | X |
| East US |  | X |
| East US 2 | X | X |
| North Central US | X | X |
| South Central US | X | X |
| West US | X | X |
| West US 2 |  | X |
| Brazil South | X | X |
| Canada Central | X | X |
| Canada East | X | X |
| North Europe | X | X |
| West Europe |  | X |
| France Central |  | X |
| UK South |  | X |
| UK West |  | X |
| East Asia | X | X |
| Southeast Asia | X | X |
| Australia East |  | X |
| Australia Central |  | X |
| Australia Central 2 |  | X |
| Australia Southeast |  | X |
| Central India | X | X |
| South India |  | X |
| West India | X | X |
| Japan East | X | X |
| Japan West | X | X |
| Korea Central |  | X |
| Korea South |  | X |
| China East 1 | X |  |
| China East 2 |  | X |
| China North 1 | X |  |
| China North 2 |  | X |
| Germany Central |  | X |
| US DoD Central  | X |  |
| US DoD East  | X |  |
| US Gov Arizona |  | X |
| US Gov Texas |  | X |
| US Gov Virginia |  | X |

## Storage

The storage you provision is the amount of storage capacity available to your Azure Database for PostgreSQL server. The storage is used for the database files, temporary files, transaction logs, and the PostgreSQL server logs. The total amount of storage you provision also defines the I/O capacity available to your server.

|    | **Basic** | **General Purpose** | **Memory Optimized** |
|:---|:----------|:--------------------|:---------------------|
| Storage type | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Storage size | 5 GB to 1 TB | 5 GB to 4 TB | 5 GB to 4 TB |
| Storage increment size | 1 GB | 1 GB | 1 GB |
| IOPS | Variable |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS |

You can add additional storage capacity during and after the creation of the server. The Basic tier does not provide an IOPS guarantee. In the General Purpose and Memory Optimized pricing tiers, the IOPS scale with the provisioned storage size in a 3:1 ratio.

You can monitor your I/O consumption in the Azure portal or by using Azure CLI commands. The relevant metrics to monitor are [storage limit, storage percentage, storage used, and IO percent](concepts-monitoring.md).

### Reaching the storage limit

The server is marked read-only when the amount of free storage reaches less than 5 GB or 5% of provisioned storage, whichever is less. For example, if you have provisioned 100 GB of storage, and the actual utilization goes over 95 GB, the server is marked read-only. Alternatively, if you have provisioned 5 GB of storage, the server is marked read-only when the free storage reaches less than 250 MB.  

When the server is set to read-only, all existing sessions are disconnected and uncommitted transactions are rolled back. Any subsequent write operations and transaction commits fail. All subsequent read queries will work uninterrupted.  

You can either increase the amount of provisioned storage to your server or start a new session in read-write mode and drop data to reclaim free storage. Running `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` sets the current session to read write mode. In order to avoid data corruption, do not perform any write operations when the server is still in read-only status.

We recommend that you set up an alert to notify you when your server storage is approaching the threshold so you can avoid getting into the read-only state. For more information, see the documentation on [how to set up an alert](howto-alert-on-metric.md).

## Backup

The service automatically takes backups of your server. The minimum retention period for backups is seven days. You can set a retention period of up to 35 days. The retention can be adjusted at any point during the lifetime of the server. You can choose between locally redundant and geo-redundant backups. Geo-redundant backups also are stored in the [geo-paired region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) of the region where your server is created. This redundancy provides a level of protection in the event of a disaster. You also gain the ability to restore your server to any other Azure region in which the service is available with geo-redundant backups. It's not possible to change between the two backup storage options after the server is created.

## Scale resources

After you create your server, you can independently change the vCores, the hardware generation, the pricing tier (except to and from Basic), the amount of storage, and the backup retention period. You can't change the backup storage type after a server is created. The number of vCores can be scaled up or down. The backup retention period can be scaled up or down from 7 to 35 days. The storage size can only be increased. Scaling of the resources can be done either through the portal or Azure CLI. For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for PostgreSQL server by using Azure CLI](scripts/sample-scale-server-up-or-down.md).

When you change the number of vCores, the hardware generation, or the pricing tier, a copy of the original server is created with the new compute allocation. After the new server is up and running, connections are switched over to the new server. During the moment when the system switches over to the new server, no new connections can be established, and all uncommitted transactions are rolled back. This window varies, but in most cases, is less than a minute.

Scaling storage and changing the backup retention period are true online operations. There is no downtime, and your application isn't affected. As IOPS scale with the size of the provisioned storage, you can increase the IOPS available to your server by scaling up storage.

## Pricing

For the most up-to-date pricing information, see the service [pricing page](https://azure.microsoft.com/pricing/details/PostgreSQL/). To see the cost for the configuration you want, the [Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) shows the monthly cost on the **Pricing tier** tab based on the options you select. If you don't have an Azure subscription, you can use the Azure pricing calculator to get an estimated price. On the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) website, select **Add items**, expand the **Databases** category, and choose **Azure Database for PostgreSQL** to customize the options.

## Next steps

- Learn how to [create a PostgreSQL server in the portal](tutorial-design-database-using-azure-portal.md).
- Learn how to [monitor and scale an Azure Database for PostgreSQL server by using Azure CLI](scripts/sample-scale-server-up-or-down.md).
- Learn about the [service limitations](concepts-limits.md). 
