---
title: Példák az Azure CLI parancsfájlokra
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure CLI-szkriptek példák a Azure SQL Database és az Azure SQL felügyelt példányának létrehozására és kezelésére
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: f5a8e5c9af9f3dc27b517c386c4abae24dc45239
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053416"
---
# <a name="azure-cli-samples-for-azure-sql-database--sql-managed-instance"></a>Azure CLI-minták a Azure SQL Database & SQL felügyelt példányához 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az Azure SQL Database és az SQL felügyelt példánya az <a href="/cli/azure">Azure CLI</a>használatával konfigurálható.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Az alábbi táblázat az Azure CLI parancsfájlokra mutató hivatkozásokat tartalmaz, amelyek a Azure SQL Database egyetlen és készletezett adatbázisait kezelik. 

| | |
|---|---|
|**Azure SQL Database-adatbázis létrehozása**||
| [Önálló adatbázis létrehozása és egy tűzfalszabály konfigurálása](scripts/create-and-configure-database-cli.md) | Létrehoz egy SQL Database, és konfigurál egy kiszolgálói szintű tűzfalszabály-szabályt. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/move-database-between-elastic-pools-cli.md) | Rugalmas készleteket hoz létre, áthelyezi a készletezett SQL-adatbázisokat, és megváltoztatja a számítási méreteket. |
|**Azure SQL Database méretezése**||
| [Önálló adatbázis méretezése](scripts/monitor-and-scale-database-cli.md) | Az adatbázis méretére vonatkozó információk lekérdezése után a SQL Database méretezése eltérő számítási méretre. |
| [Rugalmas készlet méretezése](scripts/scale-pool-cli.md) | Egy rugalmas SQL-készlet méretezése más számítási méretre. |
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Önálló adatbázis hozzáadása a feladatátvételi csoporthoz](scripts/add-database-to-failover-group-cli.md)| Létrehoz egy adatbázist és egy feladatátvételi csoportot, hozzáadja az adatbázist a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóra. |
| [Feladatátvevő csoport konfigurálása rugalmas készlethez](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Létrehoz egy adatbázist, hozzáadja azt egy rugalmas készlethez, hozzáadja a rugalmas készletet a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóra. |
| [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Az aktív geo-replikálást konfigurálja egy Azure SQL Database-adatbázishoz, és feladatátvételt hajt végre a másodlagos replikára. |
| [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Egy rugalmas SQL-készletben konfigurálja az aktív geo-replikálást egy Azure SQL-adatbázishoz, majd a feladatátvételt a másodlagos replikára. |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| A naplózási és veszélyforrás-észlelési szabályzatok konfigurálása egy Azure SQL Database-adatbázishoz. |
| **Adatbázis biztonsági mentése, visszaállítása, másolása és importálása**||
| [Adatbázis biztonsági mentése](../../sql-database/scripts/sql-database-backup-database-cli.md)| Biztonsági mentést készít egy SQL-adatbázisról egy Azure Storage biztonsági másolatba. |
| [Adatbázis visszaállítása](../../sql-database/scripts/sql-database-restore-database-cli.md)| Visszaállítja az SQL-adatbázist egy geo-redundáns biztonsági mentésből, és visszaállítja a törölt SQL-adatbázist a legújabb biztonsági mentésre. |
| [Adatbázis másolása új kiszolgálóra](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Egy meglévő SQL-adatbázis másolatát hozza létre egy új kiszolgálón. |
| [Adatbázis importálása bacpac-fájlból](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Egy adatbázis importálása egy *. bacpac* -fájlból SQL Databaseba. |
|||

További információ az [önálló adatbázis Azure CLI API](single-database-manage.md#azure-cli)-ról.

# <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

Az alábbi táblázat a felügyelt Azure SQL-példányokra mutató hivatkozásokat tartalmaz az Azure CLI parancsfájlokra.

| | |
|---|---|
| **SQL felügyelt példány létrehozása**||
| [SQL felügyelt példány létrehozása](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Létrehoz egy SQL felügyelt példányt. |
| **Transzparens adattitkosítás konfigurálása (TDE)**||
| [A felügyelt SQL-példányok transzparens adattitkosítás kezelése Azure Key Vault használatával](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| A transzparens adattitkosítás (TDE) konfigurálása az SQL felügyelt példányában Azure Key Vault különböző főbb forgatókönyvek használatával. |
|**Feladatátvételi csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása SQL felügyelt példányhoz](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Két SQL felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd az elsődleges SQL felügyelt példányról a másodlagos SQL felügyelt példányra történő feladatátvételt teszteli. |
|||

További példák a felügyelt SQL-példányokra: adatbázis [létrehozása](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [frissítése](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [áthelyezése](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), parancsfájlok [használata](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) .

További információ az [SQL felügyelt példány Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-manage-managed-instances)-ról.

---
