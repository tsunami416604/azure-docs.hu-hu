---
title: SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként
description: SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként.
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
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116314"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az adatbázis-munkaterhelések SQL Server példányról az Azure SQL felügyelt példányra történő áttelepítésekor ismernie kell az [Azure adatáttelepítési szolgáltatást](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), valamint az [SQL felügyelt példányok áttelepítésének hálózati topológiáit a DMS használatával](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Ez a cikk a SSIS-katalógusban (SSISDB) tárolt SQL Server Integration Service (SSIS) csomagok áttelepítését, valamint a SSIS-csomagok végrehajtását ütemezhető SQL Server Agent feladatokat ismerteti.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-katalógus migrálása (SSISDB)

A SSISDB áttelepítését a DMS használatával végezheti el a következő cikkben leírtak szerint: [SSIS-csomagok áttelepítése SQL felügyelt példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS feladatok SQL felügyelt példány-ügynökhöz

A felügyelt SQL-példányok natív, első osztályú ütemező szolgáltatással rendelkeznek, akárcsak SQL Server Agent a helyszínen.  Mivel a SSIS-feladatokhoz tartozó áttelepítési eszköz még nem érhető el, a SQL Server Agent a helyszínen át kell telepíteni az SQL felügyelt példány ügynökéhez parancsfájlok/manuális másolás útján.

## <a name="additional-resources"></a>További források

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS integrációs modul](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Hálózati topológiák az SQL felügyelt példányainak a DMS használatával történő áttelepítéséhez](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-csomagok migrálása egy felügyelt SQL-példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>További lépések

- [Kapcsolódás a SSISDB az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Az Azure-ban üzembe helyezett SSIS-csomagok futtatása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
