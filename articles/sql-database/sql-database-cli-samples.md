---
title: Példák az Azure CLI parancsfájljának
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061721"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-minták az Azure SQL Database-hez

Az Azure SQL Database konfigurálható az <a href="/cli/azure">Azure CLI</a>használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

# <a name="single-database--elastic-pools"></a>[Egyetlen adatbázis & rugalmas készletek](#tab/single-database)

| | |
|---|---|
|**Önálló adatbázis és rugalmas készlet létrehozása**||
| [Önálló adatbázis létrehozása és egy tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-cli.md) | Létrehoz egy Azure SQL-adatbázist, és konfigurálja a kiszolgálószintű tűzfalszabályt. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-cli.md) | SQL rugalmas készleteket hoz létre, áthelyezi a készletezett Azure SQL-adatbázisokat, és módosítja a számítási méreteket. |
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-cli.md) | Az Azure SQL-adatbázist más számítási méretre méretezi az adatbázis méretadatainak lekérdezése után. |
| [Rugalmas készlet méretezése](scripts/sql-database-scale-pool-cli.md) | Egy SQL rugalmas készlet egy másik számítási méretre. |
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Egyetlen adatbázis hozzáadása feladatátvételi csoporthoz](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Létrehoz egy adatbázist és egy feladatátvételi csoportot, hozzáadja az adatbázist a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóhoz. |
| [Feladatátvételi csoport konfigurálása rugalmas készlethez](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Létrehoz egy adatbázist, hozzáadja egy rugalmas készlethez, hozzáadja a rugalmas készletet a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóhoz. |
| [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Konfigurálja az Azure SQL-adatbázis aktív georeplikációját, és átadja azt a másodlagos replikának. |
| [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfigurálja az aktív georeplikációt egy SQL rugalmas készletben lévő Azure SQL-adatbázishoz, majd átadja azt a másodlagos replikának. |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-cli.md)| Naplózási és fenyegetésészlelési szabályzatok konfigurálása egy Azure SQL-adatbázishoz. |
| **Adatbázis biztonsági mentése, visszaállítása, másolása és importálása**||
| [Adatbázis biztonsági mentése](scripts/sql-database-backup-database-cli.md)| Biztonsági másolatot készít egy Azure SQL-adatbázisról egy Azure-tárhely biztonsági mentésére. |
| [Adatbázis visszaállítása](scripts/sql-database-restore-database-cli.md)| Visszaállít egy Azure SQL-adatbázist egy georedundáns biztonsági másolatból, és visszaállítja a törölt Azure SQL-adatbázist a legújabb biztonsági mentésre. |
| [Adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-cli.md) | Egy meglévő Azure SQL-adatbázis másolatát hozza létre egy új Azure SQL-kiszolgálón. |
| [Adatbázis importálása BACPAC-fájlból](scripts/sql-database-import-from-bacpac-cli.md)| Adatbázis importálása egy Azure SQL-kiszolgálóra *.bacpac* fájlból. |
|||

További információ az [egyetlen adatbázisazure CLI API-](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)ról.

# <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

Az alábbi táblázat az Azure CLI parancsfájlpéldákra mutató hivatkozásokat tartalmaz az Azure SQL Database – Felügyelt példány számára.

| | |
|---|---|
| **Felügyelt példány létrehozása**||
| [Felügyelt példány létrehozása](scripts/sql-database-create-configure-managed-instance-cli.md)| Felügyelt példányt hoz létre. |
| **Transzparens adattitkosítás (TDE) konfigurálása**||
| [Átlátható adattitkosítás kezelése felügyelt példányban az Azure Key Vault használatával](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Az Azure SQL felügyelt példányában konfigurálja az átlátszó adattitkosítást (TDE) az Azure Key Vault használatával különböző kulcsfontosságú forgatókönyvekkel. |
|**Feladatátvevő csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása felügyelt példányhoz](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Két felügyelt példányt hoz létre, hozzáadja őket egy feladatátvételi csoporthoz, majd teszteli a feladatátvételt az elsődleges felügyelt példányból a másodlagos felügyelt példányba. |
|||

További felügyelt példánypéldákat a [create](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [update](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [move a database](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), working [with](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) scripts című témakörben talál.

További információ az [Azure CLI API felügyelt példányáról.](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances)

---
