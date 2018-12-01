---
title: Adatok áttelepítése szolgáltatás és eszközök mátrix – Azure |} A Microsoft Docs
description: Ismerje meg a szolgáltatások és eszközök számára érhető el migrálhat adatbázisokat támogatja az áttelepítési folyamat különböző fázisait.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/30/2018
ms.openlocfilehash: d38cb244eeaf64fa0adfa78547d6d9dd4a8ad366
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52684725"
---
# <a name="service-and-tools-for-data-migration"></a>Szolgáltatás és eszközök az adatáttelepítés

Ez a cikk foglalja össze a Microsoft és külső szolgáltatások segítségére lehetnek a különböző adatbázis és az adatok áttelepítési forgatókönyvek és speciális feladatok rendelkezésre álló eszközöket.

Az alábbi táblázatok a szolgáltatás és az adatok áttelepítése sikeresen tervezése és annak különböző szakaszaiban befejezéséhez használható eszközök azonosítása.

> [!NOTE]
> Az alábbi táblázatban a csillaggal (*) jelölt elemek harmadik felektől származó eszközök képviseli.

## <a name="business-justification-stage"></a>Üzleti indoklás fázis

| **Forrás** | **Target** | **Fedezze fel és**<br/>**Szoftverleltár** | **Cél- és a Termékváltozat**<br/>**Az ajánlás** | **Teljes birtoklási költség/ROI és**<br/>**Vállalkozását** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Az Azure SQL DB Buszpéldány | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL virtuális gép | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DW-VEL |  |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL | Az Azure SQL DB, MI, virtuális gép |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Az Azure SQL DB, MI, virtuális gép | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor *](https://www.migvisor.com/) |  |
| Oracle | SQL DW-VEL | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni MySQL | MySQL-hez készült Azure DB |  |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni PostgreSQL | Azure DB for PostgreSQL |  |  | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Az Azure SQL DB, MI, virtuális gép | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [Költségkalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Az Azure SQL DB, MI, virtuális gép |  |  |  |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép |  |  |  |
| Sybase | Az Azure SQL DB, MI, virtuális gép |  |  |  |
| | | | | |

## <a name="pre-migration-stage"></a>Az áttelepítés előtti szakasz

| **Forrás** | **Target** | **Alkalmazás-adatok elérése**<br/>**Réteg értékelése** | **Adatbázis**<br/>**Értékelés** | **Teljesítmény**<br/>**Értékelés** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Az Azure SQL DB Buszpéldány |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Azure SQL virtuális gép |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | SQL DW-VEL |  |  |  |
| A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL | Az Azure SQL DB, MI, virtuális gép |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Az Azure SQL DB, MI, virtuális gép |  | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora *](http://www.simora.co.uk/) |
| Oracle | SQL DW-VEL |  | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora *](http://www.simora.co.uk/) |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Cosmos DB |  | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni MySQL | MySQL-hez készült Azure DB |  |  |  |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni PostgreSQL | Azure DB for PostgreSQL |  |  |  |
| MySQL | Az Azure SQL DB, MI, virtuális gép |  | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| DB2 | Az Azure SQL DB, MI, virtuális gép |  | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép |  | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | Az Azure SQL DB, MI, virtuális gép |  | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-stage"></a>Áttelepítési fázis

| **Forrás** | **Target** | **séma** | **Adatok**<br/>**(Kapcsolat nélküli módban)** | **Adatok**<br/>**(Online)** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Az Azure SQL DB Buszpéldány | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL virtuális gép | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DW-VEL |  |  |  |
| A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL | Az Azure SQL DB, MI, virtuális gép | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [A DMS](https://azure.microsoft.com/services/database-migration/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Az Azure SQL DB, MI, virtuális gép | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex *](https://www.quest.com/products/shareplex/) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex *](https://www.quest.com/products/shareplex/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex *](https://www.quest.com/products/shareplex/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW-VEL | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis adatok *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis adatok *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis adatok *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis adatok *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis adatok *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis adatok *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni MySQL | MySQL-hez készült Azure DB | [MySQL memóriakép *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [A DMS](https://azure.microsoft.com/services/database-migration/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni PostgreSQL | Azure DB for PostgreSQL | [PG memóriakép *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [A DMS](https://azure.microsoft.com/services/database-migration/) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Az Azure SQL DB, MI, virtuális gép | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Az Azure SQL DB, MI, virtuális gép | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | Az Azure SQL DB, MI, virtuális gép | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA-BAN](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [A DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Az Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-stage"></a>Áttelepítés utáni szakasz

| **Forrás** | **Target** | **Optimalizálás** |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Az Azure SQL DB Buszpéldány | [A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Azure SQL virtuális gép | [A felhő Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | SQL DW-VEL |  |
| A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL | Az Azure SQL DB, MI, virtuális gép |  |
| Oracle | Az Azure SQL DB, MI, virtuális gép |  |
| Oracle | SQL DW-VEL |  |
| Oracle | Azure DB for PostgreSQL |  |
| MongoDB | Cosmos DB | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni MySQL | MySQL-hez készült Azure DB |  |
| A távoli asztali szolgáltatások/Sarki fény/helyszíni PostgreSQL | Azure DB for PostgreSQL |  |
| MySQL | Az Azure SQL DB, MI, virtuális gép |  |
| DB2 | Az Azure SQL DB, MI, virtuális gép |  |
| Hozzáférés | Az Azure SQL DB, MI, virtuális gép |  |
| Sybase | Az Azure SQL DB, MI, virtuális gép |  |
| | | |

## <a name="next-steps"></a>További lépések

Az Azure Database Migration Service áttekintését lásd: [Mi az az Azure-adatbázis áttelepítése Service előzetes](dms-overview.md).
