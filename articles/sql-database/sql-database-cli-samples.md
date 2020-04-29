---
title: Példák az Azure CLI parancsfájlokra
description: Az Azure CLI-példaszkriptek bemutatják Azure SQL-adatbáziskiszolgálók, rugalmas készletek, adatbázisok és tűzfalak létrehozásának és felügyeletének folyamatát.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061721"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-minták az Azure SQL Database-hez

Azure SQL Database konfigurálható az <a href="/cli/azure">Azure CLI</a>-vel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

# <a name="single-database--elastic-pools"></a>[Önálló adatbázis & rugalmas készletek](#tab/single-database)

| | |
|---|---|
|**Önálló adatbázis és rugalmas készlet létrehozása**||
| [Önálló adatbázis létrehozása és egy tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-cli.md) | Létrehoz egy Azure SQL Database-adatbázist, és konfigurál egy kiszolgálói szintű tűzfalszabály-szabályt. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-cli.md) | Rugalmas SQL-készleteket hoz létre, áthelyezi a készletezett Azure SQL-adatbázisokat, és megváltoztatja a számítási méreteket. |
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-cli.md) | Az Azure SQL Database-t egy másik számítási méretre méretezi az adatbázis méretére vonatkozó információ lekérdezése után. |
| [Rugalmas készlet méretezése](scripts/sql-database-scale-pool-cli.md) | Egy rugalmas SQL-készlet méretezése más számítási méretre. |
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Önálló adatbázis hozzáadása a feladatátvételi csoporthoz](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Létrehoz egy adatbázist és egy feladatátvételi csoportot, hozzáadja az adatbázist a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóra. |
| [Feladatátvevő csoport konfigurálása rugalmas készlethez](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Létrehoz egy adatbázist, hozzáadja azt egy rugalmas készlethez, hozzáadja a rugalmas készletet a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóra. |
| [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Az aktív geo-replikálást konfigurálja egy Azure SQL Database-adatbázishoz, és feladatátvételt hajt végre a másodlagos replikára. |
| [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Egy rugalmas SQL-készletben konfigurálja az aktív geo-replikálást egy Azure SQL-adatbázishoz, majd a feladatátvételt a másodlagos replikára. |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-cli.md)| A naplózási és veszélyforrás-észlelési szabályzatok konfigurálása egy Azure SQL Database-adatbázishoz. |
| **Adatbázis biztonsági mentése, visszaállítása, másolása és importálása**||
| [Adatbázis biztonsági mentése](scripts/sql-database-backup-database-cli.md)| Biztonsági mentést készít egy Azure SQL Database-adatbázisról egy Azure Storage biztonsági másolatba. |
| [Adatbázis visszaállítása](scripts/sql-database-restore-database-cli.md)| Visszaállítja az Azure SQL-adatbázist egy geo-redundáns biztonsági mentésből, és visszaállítja a törölt Azure SQL Database-adatbázist a legújabb biztonsági mentésre. |
| [Adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-cli.md) | Egy meglévő Azure SQL-adatbázis másolatát hozza létre egy új Azure SQL Server-kiszolgálón. |
| [Adatbázis importálása bacpac-fájlból](scripts/sql-database-import-from-bacpac-cli.md)| Adatbázis importálása egy Azure SQL Server-kiszolgálóra egy *. bacpac* -fájlból. |
|||

További információ az [önálló adatbázis Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)-ról.

# <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

Az alábbi táblázat az Azure CLI parancsfájlokra mutató hivatkozásokat tartalmaz Azure SQL Database által felügyelt példányra.

| | |
|---|---|
| **Felügyelt példány létrehozása**||
| [Felügyelt példány létrehozása](scripts/sql-database-create-configure-managed-instance-cli.md)| Létrehoz egy felügyelt példányt. |
| **Transzparens adattitkosítás konfigurálása (TDE)**||
| [Felügyelt példányok transzparens adattitkosítás kezelése Azure Key Vault használatával](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Transzparens adattitkosítás (TDE) konfigurálása az Azure SQL felügyelt példányában Azure Key Vault különböző főbb forgatókönyvek használatával. |
|**Feladatátvételi csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása felügyelt példányhoz](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Két felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd az elsődleges felügyelt példányról a másodlagos felügyelt példányra végzi a feladatátvételi tesztet. |
|||

A felügyelt példányok további példáit lásd: adatbázisok [létrehozása](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [frissítése](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [áthelyezése](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), parancsfájlok [használata](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) .

További információ a [felügyelt példány Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances)-ról.

---
