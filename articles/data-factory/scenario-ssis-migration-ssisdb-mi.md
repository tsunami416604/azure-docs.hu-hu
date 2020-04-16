---
title: SSIS-áttelepítés az Azure SQL Database által felügyelt példánysal az adatbázis számítási feladatának célhelyeként
description: SSIS-áttelepítés az Azure SQL Database felügyelt példány, mint az adatbázis számítási feladatok cél.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419051"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS-áttelepítés az Azure SQL Database által felügyelt példánysal az adatbázis számítási feladatának célhelyeként

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Amikor adatbázis-számítási feladatokat telepít át az SQL Serverből az Azure SQL Database által felügyelt példányba, ismernie kell az [Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) szolgáltatást és az Azure SQL Database [dms-es felügyelt példányok áttelepítésének hálózati topológiáit.](https://docs.microsoft.com/azure/dms/resource-network-topologies)

Ez a cikk az SSIS-katalógusban (SSIS) tárolt SQL Server Integration Service (SSIS) csomagok és az SSIS-csomagvégrehajtás-végrehajtást ütemező SQL Server Agent-feladatokban tárolt csomagok áttelepítésére összpontosít.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-katalógus áttelepítése (SSISDB)

Az SSISDB-áttelepítés a DMS használatával végezhető el, a cikkben leírtak szerint: [SSIS-csomagok áttelepítése egy Azure SQL Database által felügyelt példányba.](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS-feladatok az Azure SQL Database felügyelt példányügynökéhez

Az Azure SQL Database által felügyelt példány rendelkezik egy natív, első osztályú ütemező, csakúgy, mint az SQL Server Agent a helyszínen.  Mivel az SSIS-feladatok áttelepítési eszköze még nem érhető el, azokat a helyszíni SQL Server Agentből az Azure SQL Database által felügyelt példányügynökbe parancsfájlokon/manuális másoláson keresztül kell áttelepíteni.

## <a name="additional-resources"></a>További források

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS integrációs modul](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Az Azure SQL Database által felügyelt példányok DMS használatával történő áttelepítésének hálózati topológiái](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-csomagok migrálása az Azure SQL Database felügyelt példányára](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>További lépések

- [Csatlakozás az SSISDB-hez az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Az Azure-ban telepített SSIS-csomagok futtatása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
