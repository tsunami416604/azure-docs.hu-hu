---
title: Adatok áttelepítése szolgáltatás és eszközök mátrix – Azure |} A Microsoft Docs
description: Ismerje meg a szolgáltatások és eszközök számára érhető el migrálhat adatbázisokat támogatja az áttelepítési folyamat különböző fázisait.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 3b3bbe45c4850d1bb37a4d991e323d5f6d9a8a0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180772"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>A szolgáltatások és eszközök érhető el áttelepítési forgatókönyvek

Ez a cikk foglalja össze a Microsoft és külső szolgáltatások segítségére lehetnek a különböző adatbázis és az adatok áttelepítési forgatókönyvek és speciális feladatok rendelkezésre álló eszközöket.

Az alábbi táblázatok a szolgáltatás és az adatok áttelepítése sikeresen tervezése és annak különböző fázisait befejezéséhez használható eszközök azonosítása.

> [!NOTE]
> Az alábbi táblázatban a csillaggal (*) jelölt elemek harmadik felektől származó eszközök képviseli.

## <a name="business-justification-phase"></a>Üzleti indoklás fázis

| **Forrás** | **Target** | **Fedezze fel és**<br/>**Szoftverleltár** | **Cél- és a Termékváltozat**<br/>**Az ajánlás** | **Teljes birtoklási költség/ROI és**<br/>**Vállalkozását** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL DB MI | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL virtuális gép | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DW |  |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | Az Azure SQL DB, MI, virtuális gép |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Az Azure SQL DB, MI, virtuális gép | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor *](https://www.migvisor.com/) |  |
| Oracle | SQL DW | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Az Azure SQL DB, MI, virtuális gép | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | MySQL-hez készült Azure DB | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | MySQL-hez készült Azure DB |  |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB for PostgreSQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | Azure DB for PostgreSQL |  |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Az Azure SQL DB, MI, virtuális gép |  |  |  |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép |  |  |  |
| Sybase | Az Azure SQL DB, MI, virtuális gép |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Az áttelepítés előtti fázist

| **Forrás** | **Target** | **Alkalmazás-adatok elérése**<br/>**Réteg értékelése** | **Adatbázis**<br/>**Értékelés** | **Teljesítmény**<br/>**Értékelés** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL virtuális gép |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |  |  |
| RDS SQL | Az Azure SQL DB, MI, virtuális gép |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Az Azure SQL DB, MI, virtuális gép |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | SQL DW |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Cosmos DB |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Az Azure SQL DB, MI, virtuális gép |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | MySQL-hez készült Azure DB |  |  |  |
| RDS MySQL | MySQL-hez készült Azure DB |  |  |  |
| PostgreSQL | Azure DB for PostgreSQL |  |  |  |
| RDS PostgreSQL | Azure DB for PostgreSQL |  |  |  |
| DB2 | Az Azure SQL DB, MI, virtuális gép |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | Az Azure SQL DB, MI, virtuális gép |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-phase"></a>Migrálási fázis

| **Forrás** | **Target** | **séma** | **Adatok**<br/>**(Kapcsolat nélküli módban)** | **Adatok**<br/>**(Online)** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL virtuális gép | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DW |  |  |  |
| RDS SQL | Az Azure SQL DB, MI, virtuális gép | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Az Azure SQL DB, MI, virtuális gép | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Az Azure SQL DB, MI, virtuális gép | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | MySQL-hez készült Azure DB | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | MySQL-hez készült Azure DB | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB for PostgreSQL | [PG memóriakép *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | Azure DB for PostgreSQL | [PG memóriakép *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Az Azure SQL DB, MI, virtuális gép | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | Az Azure SQL DB, MI, virtuális gép | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-phase"></a>Áttelepítés utáni fázist

| **Forrás** | **Target** | **Optimalizálás** |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL virtuális gép | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |
| RDS SQL | Az Azure SQL DB, MI, virtuális gép |  |
| Oracle | Az Azure SQL DB, MI, virtuális gép |  |
| Oracle | SQL DW |  |
| Oracle | Azure DB for PostgreSQL |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Az Azure SQL DB, MI, virtuális gép |  |
| MySQL | MySQL-hez készült Azure DB |  |
| RDS MySQL | MySQL-hez készült Azure DB |  |
| PostgreSQL | Azure DB for PostgreSQL |  |
| RDS PostgreSQL | Azure DB for PostgreSQL |  |
| DB2 | Az Azure SQL DB, MI, virtuális gép |  |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép |  |
| Sybase | Az Azure SQL DB, MI, virtuális gép |  |
| | | |

## <a name="next-steps"></a>További lépések

Az Azure Database Migration Service áttekintését lásd: a cikk [Mi az az Azure Database Migration Service](dms-overview.md).
