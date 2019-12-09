---
title: SSIS Migrálás Azure SQL Database felügyelt példánnyal az adatbázis-munkaterhelés céljaként
description: A SSIS áttelepítése Azure SQL Database felügyelt példánnyal az adatbázis-munkaterhelés céljaként.
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
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929782"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS Migrálás Azure SQL Database felügyelt példánnyal az adatbázis-munkaterhelés céljaként

Ha az adatbázis-munkaterheléseket a helyszíni SQL Serverról Azure SQL Database felügyelt példányra telepíti át, ismernie kell az [Azure adatáttelepítési szolgáltatást](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), valamint a [Azure SQL Database felügyelt példányok áttelepítéséhez szükséges hálózati topológiákat a DMS használatával](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Ez a cikk a SSIS-katalógusban (SSISDB) tárolt SQL Server Integration Service (SSIS) csomagok áttelepítését, valamint a SSIS-csomagok végrehajtását ütemezhető SQL Server Agent feladatokat ismerteti.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-katalógus migrálása (SSISDB)

A SSISDB áttelepítését a DMS használatával végezheti el a következő cikkben leírtak szerint: [SSIS-csomagok áttelepítése Azure SQL Database felügyelt példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS feladatok Azure SQL Database felügyelt példány ügynökének

Azure SQL Database felügyelt példány natív, első osztályú ütemező, akárcsak SQL Server Agent a helyszínen.  Mivel a SSIS-feladatok áttelepítési eszköze még nem érhető el, át kell telepíteni őket SQL Server Agentról a helyszínről Azure SQL Database felügyelt példány-ügynöknek a parancsfájlok/manuális másolás használatával.

## <a name="additional-resources"></a>További források

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Hálózati topológiák Azure SQL Database felügyelt példányok áttelepítéséhez a DMS használatával](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-csomagok migrálása Azure SQL Database felügyelt példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Következő lépések

- [Kapcsolódás a SSISDB az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Az Azure-ban üzembe helyezett SSIS-csomagok futtatása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
