---
title: Az Azure Database Migration Service eszközeinek mátrixa
description: Ismerje meg az adatbázisok áttelepítéséhez és az áttelepítési folyamat különböző fázisainak támogatásához rendelkezésre álló szolgáltatásokat és eszközöket.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/03/2020
ms.openlocfilehash: 2170612f9365ec9645b70c24236b865f106b646e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254830"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Az adatmigrálási forgatókönyvekhez elérhető szolgáltatások és eszközök

Ez a cikk a Microsoft és a külső felek által elérhető szolgáltatások és eszközök mátrixát tartalmazza, amelyek segítséget nyújtanak a különböző adatbázis- és adatáttelepítési forgatókönyvekben és speciális feladatokban.

Az alábbi táblázatok azonosítják azokat a szolgáltatásokat és eszközöket, amelyekkel sikeresen megtervezheti az adatáttelepítést, és befejezheti a különböző fázisokat.

> [!NOTE]
> A következő táblázatokban a csillaggal (*) jelölt elemek harmadik féltől származó eszközöket jelölnek.

## <a name="business-justification-phase"></a>Üzleti indoklási fázis

| Forrás | Cél | Fedezze fel /<br/>Leltár | Cél és termékváltozat<br/>Ajánlás | TCO/ROI és<br/>Üzleti eset |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Azure SQL DB MI | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL virtuális gép | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DW | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Felhőmize*](https://www.cloudamize.com/) |  | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL | Azure SQL DB, MI, VM |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB, MI, VM | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | SQL DW | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló | [MAP eszközkészlet](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Felhőmize*](https://www.cloudamize.com/) | [Felhőmize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/) | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | MySQL-hez készült Azure DB | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | MySQL-hez készült Azure DB |  |  | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló |  |  | [TCO kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Hozzáférés | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP ASE | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP IQ | Azure SQL DB, MI, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Áttelepítés előtti fázis

| Forrás | Cél | Alkalmazás-adatelérés<br/>Rétegfelmérés | Adatbázis<br/>Értékelés | Teljesítmény<br/>Értékelés |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Felhőmize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Felhőmize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL virtuális gép | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Felhőmize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | SQL DW | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Felhőmize*](https://www.cloudamize.com/) | [Felhőmize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | MySQL-hez készült Azure DB |  |  |  |
| RDS MySQL | MySQL-hez készült Azure DB |  |  |  |
| PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló |  |  |  |
| RDS PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló |  |  |  |
| DB2 | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Hozzáférés | Azure SQL DB, MI, VM |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP ASE | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP IQ | Azure SQL DB, MI, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Áttelepítési fázis

| Forrás | Cél | Séma | Adatok<br/>(Offline) | Adatok<br/>(Online) |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Felhőmize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL virtuális gép | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Felhőmize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Felhőmize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DW |  |  |  |
| Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL | Azure SQL DB, MI, VM | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló | [Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/)<br/>[Imanis adatok*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/)<br/>[Imanis adatok*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Felhőmize*](https://www.cloudamize.com/)<br/>[Imanis adatok*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis adatok*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis adatok*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis adatok*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | MySQL-hez készült Azure DB | [MySQL memóriakép*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | MySQL-hez készült Azure DB | [MySQL memóriakép*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló | [PG dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló | [PG dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Hozzáférés | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase - SAP ASE | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase - SAP IQ | Azure SQL DB, MI, VM | [Ispirer*](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Ispirer*](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | |
| | | | | |

## <a name="post-migration-phase"></a>Az áttelepítés utáni fázis

| Forrás | Cél | Optimalizálás |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL virtuális gép | [Felhőatlasz*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Felhőmize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |
| Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL | Azure SQL DB, MI, VM |  |
| Oracle | Azure SQL DB, MI, VM |  |
| Oracle | SQL DW |  |
| Oracle | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló |  |
| MongoDB | Cosmos DB | [Felhőmize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB, MI, VM |  |
| MySQL | MySQL-hez készült Azure DB |  |
| RDS MySQL | MySQL-hez készült Azure DB |  |
| PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló |  |
| RDS PostgreSQL | Azure DB a PostgreSQL-hez -<br/>Egyetlen kiszolgáló |  |
| DB2 | Azure SQL DB, MI, VM |  |
| Hozzáférés | Azure SQL DB, MI, VM |  |
| Sybase - SAP ASE | Azure SQL DB, MI, VM |  |
| Sybase - SAP IQ | Azure SQL DB, MI, VM |  |
| | | |

## <a name="next-steps"></a>További lépések

Az Azure database migration service áttekintését a [Mi az Azure-adatbázis-áttelepítési szolgáltatás című cikkben találja.](dms-overview.md)
