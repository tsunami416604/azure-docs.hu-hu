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
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419051"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS Migrálás Azure SQL Database felügyelt példánnyal az adatbázis-munkaterhelés céljaként

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha az adatbázis-munkaterheléseket a helyszíni SQL Serverról Azure SQL Database felügyelt példányra telepíti át, ismernie kell az [Azure adatáttelepítési szolgáltatást](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), valamint a [Azure SQL Database felügyelt példányok áttelepítéséhez szükséges hálózati topológiákat a DMS használatával](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Ez a cikk a SSIS-katalógusban (SSISDB) tárolt SQL Server Integration Service (SSIS) csomagok áttelepítését, valamint a SSIS-csomagok végrehajtását ütemezhető SQL Server Agent feladatokat ismerteti.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-katalógus migrálása (SSISDB)

A SSISDB áttelepítését a DMS használatával végezheti el a következő cikkben leírtak szerint: [SSIS-csomagok áttelepítése Azure SQL Database felügyelt példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS feladatok Azure SQL Database felügyelt példány ügynökének

Azure SQL Database felügyelt példány natív, első osztályú ütemező, akárcsak SQL Server Agent a helyszínen.  Mivel a SSIS-feladatok áttelepítési eszköze még nem érhető el, át kell telepíteni őket SQL Server Agentról a helyszínről Azure SQL Database felügyelt példány-ügynöknek a parancsfájlok/manuális másolás használatával.

## <a name="additional-resources"></a>További háttéranyagok

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS integrációs modul](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Hálózati topológiák Azure SQL Database felügyelt példányok áttelepítéséhez a DMS használatával](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-csomagok migrálása az Azure SQL Database felügyelt példányára](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>További lépések

- [Kapcsolódás a SSISDB az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Az Azure-ban üzembe helyezett SSIS-csomagok futtatása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
